#lan #llm #analysis #done

```table-of-contents
```

- [1 How to time execution and thread impact](#1-how-to-time-execution-and-thread-impact)
- [2 Objective](#2-objective)
- [3 LLM Instructions](#3-llm-instructions)
- [4 Journal](#4-journal)
	- [4.1 Basic timing solution](#41-basic-timing-solution)
	- [4.2 Tools](#42-tools)
		- [4.2.1 sysbench](#421-sysbench)
		- [4.2.2 Hyperfine](#422-hyperfine)
	- [4.3 Techniques](#43-techniques)
		- [4.3.1 Explicit limiting of simultaneous thread execution](#431-explicit-limiting-of-simultaneous-thread-execution)
			- [4.3.1.1 Answer](#4311-answer)
			- [4.3.1.2 Journal](#4312-journal)
		- [4.3.2 Number of threads and cores available on the system](#432-number-of-threads-and-cores-available-on-the-system)
- [5 References](#5-references)

# 1 How to time execution and thread impact

This entry is spawned by [[Wk 26 000 On why the rust linker is single-threaded]].

# 2 Objective

- [x] Be able to time how long it takes for a process to finish.
- [x] Run this many trials and give statistics.
- [x] Be able to differentiate certain aspects of the process. For example, is it running single-threaded or multi-threaded? Does this affect the timing analysis?

# 3 LLM Instructions
- This is a diagnostic document and not a conversation. Everything shared is context. Address the questions tagged (Q#) like (Q1) for example. If you see something like (~1), assume it part of the archive and not a latest set of questions.
	- Since it keeps occurring, I ask Again
	- !!! NEVER RESPOND TO (~1), (~2), etc.
	- ONLY respond to the tagged questions. Nothing else.

Using Chatgpt-4o for assistance.
# 4 Journal

## 4.1 Basic timing solution

2025-06-24 Wk 26 Tue - 15:56

Basic solution is to use `time`. In my case, specifically `/usr/bin/time`, because `time` points elsewhere.

It has a `-f FORMAT| --format FORMAT` option documented in `man time`. 

Let's use an example similar to the one in the manual, but with the additional following formats

It uses the following formats:
```
E      Elapsed real (wall clock) time used by the process, in [hours:]minutes:seconds.
U      Total number of CPU-seconds that the process used directly (in user mode), in seconds.
S      Total number of CPU-seconds used by the system on behalf of the process (in kernel mode), in seconds.
P      Percentage of the CPU that this job got.  This is just user + system times divided by the total running time.  It also prints a percentage sign.
c      Number of times the process was context-switched involuntarily (because the time slice expired).
w      Number of times that the program was context-switched voluntarily, for instance while waiting for an I/O operation to complete.
```

The example:

```sh
$ /usr/bin/time -f "%E real,\t%U user,\t%S sys,\t%P CPU,\tinvol(%c) vol(%w) switch," sleep 3.345                                      
0:03.34 real,   0.00 user,      0.00 sys,       0% CPU, invol(0) vol(2) switch,
```

Interestingly the first time showed 4 voluntary switches...

Sleep doesn't do much. Let's make something that gets the CPU busy!

```sh
/usr/bin/time -f "%E real,\t%U user,\t%S sys,\t%P CPU,\tinvol(%c) vol(%w) switch," python3 -c "print([x for x in range(10**8)][-1])"
99999999
0:05.82 real,   3.23 user,      2.57 sys,       99% CPU,        invol(466) vol(1) switch,
```

## 4.2 Tools

On LLM Suggestion,

### 4.2.1 sysbench
For thread stress testing:

```sh
sudo apt install sysbench
```

Though when I try to use it, it cryptically says I need an argument:

```sh
$ sysbench cpu help                      
sysbench 1.0.20 (using system LuaJIT 2.1.1731486438)

cpu options:
  --cpu-max-prime=N upper limit for primes generator [10000]
```

(~1)  Cryptic error message

in `man sysbench`, we can see in the synopsis that all tests share the same commands:

```
sysbench [common-options] --test=name [test-options] <command>
Commands: prepare run cleanup help version
```

Can't easily discern the difference or efficiency gains in using 2 threads instead of 1 for example with sysbench. It also allows me to specify 250 threads when only 24 are available.

The only thing that I notice is that `--threads=N` in my case gives $N \times 17187$ number of events in testing, though the number varies.

(~1) Need to be able to test performance changes depending on thread uses, ideally clear signal of multiplying time (because more jobs are simultaneously distributed and the work is uniform)

Using the following command, we are able to notice linear scaling in time gains by varying `--threads=N`

```sh
sysbench cpu --cpu-max-prime=10000 --events=10000 --time=0 --threads=1 run
```

| `--threads=N` | total time (s) | Expected Proportion of `--threads=1` | Actual Proportion of `--threads=1` |
| ------------- | -------------- | ------------------------------------ | ---------------------------------- |
| 1             | 6.82           | 1.00                                 | 1.00                               |
| 2             | 3.27           | 0.50                                 | 0.48                               |
| 24            | 0.42           | 0.04                                 | 0.06                               |
^table1
### 4.2.2 Hyperfine

For running multiple trials for more accurate benchmarking

```sh
sudo apt install hyperfine
```

They have a cool sweep option in `hyperfine --help | less`,

```
Example:  hyperfine -P threads 1 8 'make -j {threads}'
This performs benchmarks for 'make -j 1', 'make -j 2', …, 'make -j 8'.
```

```sh
hyperfine python3 -c "print([x for x in range(10**8)][-1])"

# stdout
Benchmark 1: python3
  Time (mean ± σ):      26.3 ms ±   0.8 ms    [User: 20.5 ms, System: 5.7 ms]
  Range (min … max):    20.0 ms …  28.0 ms    106 runs
 
  Warning: Statistical outliers were detected. Consider re-running this benchmark on a quiet system without any interferences from other programs. It might help to use the '--warmup' or '--prepare' options.
 
Error: The cleanup command terminated with a non-zero exit code. Append ' || true' to the command if you are sure that this can be ignored.
```

(~1) Error

It ran only `python3`.  Needs quotes.

```sh
 hyperfine 'python3 -c "print([x for x in range(10**8)][-1])"'

#stdout
Benchmark 1: python3 -c "print([x for x in range(10**8)][-1])"
  Time (mean ± σ):      6.477 s ±  0.802 s    [User: 3.719 s, System: 2.751 s]
  Range (min … max):    5.315 s …  7.615 s    10 runs


```

It can sweep!

```sh
hyperfine -P n 1 8 'python3 -c "print([x for x in range(10**{n})][-1])"' 

# stdout
Benchmark 1: python3 -c "print([x for x in range(10**1)][-1])"
  Time (mean ± σ):      26.8 ms ±   1.2 ms    [User: 20.8 ms, System: 5.9 ms]
  Range (min … max):    16.1 ms …  28.2 ms    106 runs
 
  Warning: Statistical outliers were detected. Consider re-running this benchmark on a quiet system without any interferences from other programs. It might help to use the '--warmup' or '--prepare' options.
 
Benchmark 2: python3 -c "print([x for x in range(10**2)][-1])"
  Time (mean ± σ):      26.4 ms ±   1.5 ms    [User: 20.7 ms, System: 5.7 ms]
  Range (min … max):    15.6 ms …  28.4 ms    109 runs
 
  Warning: Statistical outliers were detected. Consider re-running this benchmark on a quiet system without any interferences from other programs. It might help to use the '--warmup' or '--prepare' options.

# [...]

Summary
  python3 -c "print([x for x in range(10**2)][-1])" ran
    1.01 ± 0.08 times faster than python3 -c "print([x for x in range(10**3)][-1])"
    1.01 ± 0.07 times faster than python3 -c "print([x for x in range(10**1)][-1])"
    1.04 ± 0.10 times faster than python3 -c "print([x for x in range(10**4)][-1])"
    1.32 ± 0.12 times faster than python3 -c "print([x for x in range(10**5)][-1])"
    3.94 ± 0.53 times faster than python3 -c "print([x for x in range(10**6)][-1])"
   27.01 ± 5.19 times faster than python3 -c "print([x for x in range(10**7)][-1])"
  242.13 ± 32.65 times faster than python3 -c "print([x for x in range(10**8)][-1])"
```

Let's test it for threading performance:

```sh
hyperfine -P t 1 24 'sysbench cpu --cpu-max-prime=10000 --events=10000 --time=0 --threads={t} run'
```

| threads (#) | time (ms) | variance (ms) | user (ms) | system (ms) | max time (ms) | min time (ms) |
| ----------- | --------- | ------------- | --------- | ----------- | ------------- | ------------- |
| 1           | 6973      | 0.231         | 6957      | 0.007       | 6544          | 7242          |
| 2           | 3312      | 0.060         | 6601      | 0.006       | 3188          | 3387          |
| 3           | 2134      | 0.021         | 6367      | 0.009       | 2091          | 2160          |
| 4           | 1600      | 0.023         | 6354      | 0.007       | 1559          | 1637          |
| 5           | 1284      | 0.019         | 6360      | 0.007       | 1260          | 1303          |
| 6           | 1059      | 0.015         | 6281      | 0.007       | 1036          | 1078          |
| 7           | 909.2     | 12.4          | 6282.5    | 7.7         | 894.1         | 923.6         |
| 8           | 793.1     | 10.7          | 6252.7    | 7.3         | 781.4         | 809.8         |
| 9           | 711.2     | 12.4          | 6294.9    | 7.4         | 699.7         | 735.6         |
| 10          | 655.0     | 9.8           | 6429.8    | 7.6         | 641.9         | 669.6         |
| 11          | 602.6     | 10.2          | 6498.8    | 8.2         | 587.2         | 615.4         |
| 12          | 569.6     | 17.3          | 6686.4    | 10.5        | 546.2         | 599.5         |
| 13          | 512.1     | 6.7           | 6498.2    | 8.4         | 504.7         | 523.4         |
| 14          | 480.7     | 10.6          | 6556.7    | 8.5         | 474.1         | 510.0         |
| 15          | 450.1     | 5.6           | 6569.1    | 10.0        | 446.3         | 464.9         |
| 16          | 436.9     | 14.4          | 6789.2    | 8.0         | 422.8         | 467.1         |
| 17          | 440.9     | 19.6          | 7263.4    | 11.9        | 421.7         | 471.9         |
| 18          | 444.1     | 35.9          | 7756.5    | 10.8        | 420.9         | 520.1         |
| 19          | 448.1     | 20.6          | 8224.0    | 23.9        | 422.1         | 478.6         |
| 20          | 445.1     | 12.5          | 8478.3    | 17.2        | 428.7         | 465.6         |
| 21          | 441.6     | 11.4          | 8881.5    | 15.6        | 420.5         | 459.1         |
| 22          | 428.5     | 5.9           | 8992.3    | 13.5        | 419.8         | 439.3         |
| 23          | 434.9     | 6.5           | 9466.6    | 16.3        | 425.9         | 444.1         |
| 24          | 427.8     | 8.5           | 9441.8    | 16.1        | 418.9         | 442.9         |
^hypertime-table1

| threads (#) | time (ms) | max time (ms) | min time (ms) |
| ----------- | --------- | ------------- | ------------- |
| 1           | 6973      | 6544          | 7242          |
| 2           | 3312      | 3188          | 3387          |
| 3           | 2134      | 2091          | 2160          |
| 4           | 1600      | 1559          | 1637          |
| 5           | 1284      | 1260          | 1303          |
| 6           | 1059      | 1036          | 1078          |
| 7           | 909.2     | 894.1         | 923.6         |
| 8           | 793.1     | 781.4         | 809.8         |
| 9           | 711.2     | 699.7         | 735.6         |
| 10          | 655.0     | 641.9         | 669.6         |
| 11          | 602.6     | 587.2         | 615.4         |
| 12          | 569.6     | 546.2         | 599.5         |
| 13          | 512.1     | 504.7         | 523.4         |
| 14          | 480.7     | 474.1         | 510.0         |
| 15          | 450.1     | 446.3         | 464.9         |
| 16          | 436.9     | 422.8         | 467.1         |
| 17          | 440.9     | 421.7         | 471.9         |
| 18          | 444.1     | 420.9         | 520.1         |
| 19          | 448.1     | 422.1         | 478.6         |
| 20          | 445.1     | 428.7         | 465.6         |
| 21          | 441.6     | 420.5         | 459.1         |
| 22          | 428.5     | 419.8         | 439.3         |
| 23          | 434.9     | 425.9         | 444.1         |
| 24          | 427.8     | 418.9         | 442.9         |
^hypertime-table1-graph1

```chart
type: line
id: hypertime-table1-graph1
layout: columns
width: 80%
beginAtZero: true
```


Graph as image:
![[Pasted image 20250624185205.png]]
## 4.3 Techniques

On LLM Suggestion,

Use `htop`, `top`, or `perf` to observe actual thread usage during run.

### 4.3.1 Explicit limiting of simultaneous thread execution

#### 4.3.1.1 Answer

This limits execution to one logical core:

```sh
taskset --cpu-list 0 sysbench cpu --cpu-max-prime=10000 --events=10000 --time=0 --threads=24 run
```

#### 4.3.1.2 Journal

2025-06-24 Wk 26 Tue - 17:12

Now that we have consistent benchmarking of thread performance via [[#^table1]], we can test for techniques to force threads usable to be 1, and expect to get `total time` consistent with 1 threads.

LLM suggests setting OMP_NUM_THREADS (bad),

```sh
# bad suggestion
OMP_NUM_THREADS=1 sysbench cpu --cpu-max-prime=10000 --events=10000 --time=0 --threads=24 run
```

(~1) But this takes `0.42s`, consistent with 24-thread performance.

Other bad suggestions include creating directories in `/sys/fs/cgroup/` which can be hard to remove.

2025-06-24 Wk 26 Tue - 17:35

We are able to limit the number of threads that can be spawned with

```sh
sudo systemd-run --scope -p TasksMax=25 sysbench cpu --cpu-max-prime=10000 --events=10000 --time=0 --threads=24 run
```

On `TasksMax=24` or less, we get

```
FATAL: sb_thread_create() for thread #23 failed. errno = 11 (Resource temporarily unavailable)
```

This is around the maximum allowed threads to spawn, and not what can logically run simultaneously. For example,

```sh
$ sudo systemd-run --scope -p TasksMax=250 sysbench cpu --cpu-max-prime=10000 --events=10000 --time=0 --threads=250 run
FATAL: sb_thread_create() for thread #249 failed. errno = 11 (Resource temporarily unavailable)
```

We are able to spawn and limit the amount of threads spawnable to 250, much higher than 24.

(~1) Ideally, we would be able to limit execution itself to a single logical core.

This does what we expect:

```sh
taskset --cpu-list 0 sysbench cpu --cpu-max-prime=10000 --events=10000 --time=0 --threads=24 run
```

even though 24 threads are *spawned*, the total time `sysbench` takes is `5.97s`, consistent with 1 thread performance!

### 4.3.2 Number of threads and cores available on the system

`nproc` gives us the number of logical cores which can run threads. For me, it's `24.` We can also use `lscpu` to get more detailed information:

```
CPU(s):                   24
Thread(s) per core:   2
Core(s) per socket:   16
```

# 5 References