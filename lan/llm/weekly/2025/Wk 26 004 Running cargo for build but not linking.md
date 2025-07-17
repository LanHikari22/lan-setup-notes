#lan #llm #rust #build #linking #pend

# 1 Running cargo for build but not linking

# 2 Objective

I would like to testing building (think running rustc on source code to turn it into object files) separately from linking (taking all the object files and linking them into a single elf.)

Normally I use cargo, and it figures out the project structure. My current project has 400 dependencies. So I would like to separate the building and the linking stages here of all of it.

- [ ] Build the project but without the final linking stage, then manually call the linking stage explicitly.

# 3 LLM Instructions
- This is a diagnostic document and not a conversation. Everything shared is context. Address the questions tagged (Q#) like (Q1) for example. If you see something like (~1), assume it part of the archive and not a latest set of questions.
	- Since it keeps occurring, I ask Again
	- !!! NEVER RESPOND TO (~1), (~2), etc.
	- ONLY respond to the tagged questions. Nothing else.

# 4 Journal

(Q1) Satisfy objective

## 4.1 Figuring out what linker is being invoked

We can find out the processes spawned by `cargo build --release` via

```sh
strace -f -e execve cargo build --release 2>&1 | grep execve
```

In my case, this shows these different processes running for each dependency. Build and linking is done at once before moving on the next dependency:

```sh
/home/lan/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/bin/cargo
/home/lan/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/bin/rustc
/usr/bin/ld
/usr/libexec/gcc/x86_64-linux-gnu/14/collect2
/usr/bin/cc
/usr/sbin/cc

# various build scripts like
/home/lan/src/cloned/gh/rparrett/pixie_wrangler/target/release/build/nix-3dc817e1b6536000/build-script-build
```


2025-06-24 Wk 26 Tue - 19:23

# 5 Issues

## 5.1 Why is arm-none-eabi bin/cc running for a PC application?

via 

```sh
strace -f -e execve cargo build --release 2>&1 | grep execve
```

I found these processes being spawned (execve):

```
/home/lan/Downloads/gcc-arm-none-eabi-10.3-2021.10/bin/cc
/home/lan/miniconda3/condabin/cc
/home/lan/.nvm/versions/node/v24.1.0/bin/cc
```

(Q2) But why is that?

