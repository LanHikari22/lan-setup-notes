\#lan #entry #research #todo #compiler #rust #external

# 1 Problem

Rust build can take a long time. We can pass `-j$(nproc)` to speed things up. This can help with building multiple files simultaneously, but then the linking stage comes the bottleneck.

# 2 Objective

* [ ] Identify an issue ticket for this if present.
* [ ] Identify why it cannot be multi-threaded to be used with `-j$(nproc)`
* [ ] Run some empirical tests and record data on build times given some system context.

# 3 Journal

## 3.1 System under test

### 3.1.1 Which is?

2025-06-24 Wk 26 Tue - 13:15

We need a system to build and test with.

2025-06-24 Wk 26 Tue - 13:38

Let's go with [github: pixie_wrangler](https://github.com/rparrett/pixie_wrangler) [<a name="5" />^5](Wk%2026%20000%20On%20why%20the%20rust%20linker%20is%20single-threaded.md#5)! This is a cool game about PCB design! It works on itch.io but let's try to build it ourselves.

### 3.1.2 Building pixie_wrangler

2025-06-24 Wk 26 Tue - 13:39

Let's clone the project

````sh
mkdir -p ~/src/cloned/gh/rparrett
cd ~/src/cloned/gh/rparrett
git clone git@github.com:rparrett/pixie_wrangler.git
````

The  [README](https://github.com/rparrett/pixie_wrangler) [^5](Wk%2026%20000%20On%20why%20the%20rust%20linker%20is%20single-threaded.md#5) recommends that we first setup the dependencies of the Bevy engine found [here](https://github.com/bevyengine/bevy/blob/latest/docs/linux_dependencies.md) [<a name="6" />^6](Wk%2026%20000%20On%20why%20the%20rust%20linker%20is%20single-threaded.md#6).

````sh
sudo apt-get install g++ pkg-config libx11-dev libasound2-dev libudev-dev libxkbcommon-x11-0
# Since I am on Wayland
sudo apt-get install libwayland-dev libxkbcommon-dev
````

Let's build the project native:

````sh
cd pixie_wrangler
cargo run --release
````

460 dependencies. This will make a good unit to test.

![Pasted image 20250624135659.png](../../../../../../../attachments/Pasted%20image%2020250624135659.png)

![Pasted image 20250624135755.png](../../../../../../../attachments/Pasted%20image%2020250624135755.png)

![Pasted image 20250624135814.png](../../../../../../../attachments/Pasted%20image%2020250624135814.png)

The game builds and runs!

Yay!

## 3.2 Timing build times for pixel_wrangler

2025-06-24 Wk 26 Tue - 13:59

Let's record build times.

2025-06-24 Wk 26 Tue - 18:56

For tools and techniques on timing execution and thread control: [Wk 26 003 How to time execution and thread impact](../../../../../../llm/weekly/2025/Wk%2026%20003%20How%20to%20time%20execution%20and%20thread%20impact.md)

### 3.2.1 hyperfine benchmark with thread sweep for cargo build -- release

Install the tool:

````sh
sudo apt install hyperfine
````

Attempting the following:

````sh
# in /home/lan/src/cloned/gh/rparrett/pixie_wrangler
hyperfine -P threads 1 24 'cargo clean && cargo build --release -j{threads}'
````

Takes too long. Even building in `-j1` is very slow for the 400 dependencies.  But it's farily fast in `-j24`.

## 3.3 Isolating build from linking

Build and link are intertwined. For each dependency, it is built and the linker is invoked `/usr/bin/ld`. See [Wk 26 004 Running cargo for build but not linking](../../../../../../llm/weekly/2025/Wk%2026%20004%20Running%20cargo%20for%20build%20but%20not%20linking.md).

2025-06-24 Wk 26 Tue - 20:33

This means thinking of this in terms of a build and a linker stage is likely not accurate. `cargo` seems to invoke multiple build units in parallel, each one invoking `rustc` to emit objects as well as internally call `/usr/bin/ld` to link them.

This could mean that we have parallelism gains here at the build unit level, but not necessarily per dependency. `/usr/bin/ld` could also be swapped for another linker. We could try a parallel one and compare build times.

2025-06-24 Wk 26 Tue - 19:07

## 3.4 Identifying that the linker used is single threaded

2025-06-24 Wk 26 Tue - 13:15

Let's start by testing our proposition. We suspect the linker is single-threaded. Let's prove it.

We get a confirmation from the documentation. [rustprojectprimer: linking](https://rustprojectprimer.com/building/linker.html) [<a name="7" />^7](Wk%2026%20000%20On%20why%20the%20rust%20linker%20is%20single-threaded.md#7) specifies that there exists rust parallel linkers, but normally they are single-threaded. But it would be best to get an operational proof.

# 4 References

1. [most_impressive_game_ive_seen_written_in_rust](https://www.reddit.com/r/rust/comments/17va1js/most_impressive_game_ive_seen_written_in_rust/) <a name="1" />^1
1. [Rust GameDev WG](https://gamedev.rs/) <a name="2" />^2
1. [itch.io open source rust games](https://itch.io/games/made-with-rust/tag-open-source) <a name="3" />^3
1. [itch.io: pixie_wrangler](https://euclidean-whale.itch.io/pixie-wrangler) <a name="4" />^4
1. [github: pixie_wrangler](https://github.com/rparrett/pixie_wrangler) ^5
1. [bevy engine linux_dependencies](https://github.com/bevyengine/bevy/blob/latest/docs/linux_dependencies.md) ^6
1. [rustprojectprimer: linking](https://rustprojectprimer.com/building/linker.html) ^7

````mermaid
graph TD

%% Nodes
A1[^1 most_impressive_game_...]
A2[^2 Rust GameDev WG]
N2_1[As of June 2025, Latest post is in June 2024]:::note

%% Settings
classDef note fill:#f9f9a6,stroke:#333,stroke-width:1px,color:#000,font-style:italic;

%% Connections
A1 --> |mentions| A2
N2_1 -.-> |about| A2
````

````mermaid
graph TD

%% Nodes
A1[^3 itch.io open source rust games]
A2[^4 itch.io: pixie_wrangler]
A3[^5 github: pixie_wrangler]
A4[^6 bevy_engine linux_dependencies]

N2_1[A rust game about PCB design!]:::note

%% Settings
classDef note fill:#f9f9a6,stroke:#333,stroke-width:1px,color:#000,font-style:italic;

%% Connections
A1 --> |lists| A2
A2 --> |has_source| A3
A3 --> |requires_for_linux| A4
N2_1 -.-> |about| A2
````
