---
status: done
---
#lan #docker #linux #ubuntu #st/done #external 

# 1 Objective

To test installations on various systems, starting with mine (Ubuntu 25.04). 

# 2 Journal

## 2.1 On Starting docker setup 

We can use [gh docker awesome-compose ex](https://github.com/docker/awesome-compose/tree/master/wireguard) [[#^1]] as a starting point.

They use `compose.yml` and have an `.env` file...

Since we want these boxes to be immediately usable, we wouldn't want the user to modify configuration unless they want to. This needs to be something they can run both in Windows and Linux. 

2025-07-19 Wk 29 Sat - 01:11

I've handled the mounting issue in [dbmint](https://github.com/LanHikari22/dbmint) by basically just mounting to the current location `.`. The user can determine where to run it then on their own.

```sh
docker run --rm -it -v .:/mnt/ --user $(id -u):$(id -g) lan22h/dbmint:latest gen schema.dbml -o mydb.db
```

This fetches the image itself however, and not from repository by box id like we're planning here.

For [rs_repro](git clone https://github.com/LanHikari22/rs_repro.git && cd rs_repro && cargo run --features "repro003"), 

```rust
git clone https://github.com/LanHikari22/rs_repro.git && cd rs_repro && cargo run --features "repro003"
```

Here the repository cloning and building and running is all done. 

The issue is that it does setup + running, and so doing it multiple times in a row is not gonna work well.

Do we want to create an image `lan22h/boxXXX` for each box, then? I mean this is feasible and it will at least give us immediate running rather than clone and build and run which is not repeatable... In case of [rs_repro](git clone https://github.com/LanHikari22/rs_repro.git && cd rs_repro && cargo run --features "repro003") this is alright because it's a repository for reproducing rust code, so the user is technical. But for boxes, the user might not want to touch the command at all.

2025-07-19 Wk 29 Sat - 01:25

For box000, we will go the [dbmint](https://github.com/LanHikari22/dbmint) route. We will build an image, and people can then use that image once it's deployed for one-liners.

# 3 Tasks
## 3.1 Deploying new Docker image

- [x] 

2025-07-19 Wk 29 Sat - 03:05

```sh
docker login -u {user}
docker push {user}/{app_name}:{tag}
```

## 3.2 Start a docker container over a shell

- [x] 

2025-07-19 Wk 29 Sat - 01:59

We have an `is_persistent` file that can be set to `Yes` or `No`. If we set it to `Yes`, it will just boot a container. 

Then, like with this [answer](https://stackoverflow.com/a/30173220/6944447) we can use

```
docker exec -it {container_id} sh
```

But the application in this instance just prints `Hello world!` and exits, so the container dies.

2025-07-19 Wk 29 Sat - 02:39

Made `docker_sh.sh` and `docker_stop.sh` for a cleaner solution on starting a shell (and booting the container initially) and stopping or removing the container.

## 3.3 Create edit_app_name.sh for hardcoded name for web use

### 3.3.1 Objective

Since we will often use these scripts via curl, we do not want to get the app_name from an adjacent file. The app_name should simply be the current folder name.

- [ ] edit_app_name.sh changes all `$app_name` variables in the current folder scripts.

### 3.3.2 Journal

2025-07-19 Wk 29 Sat - 04:27

# 4 Issues

## 4.1 Docker refusing to run on non-root

- [x] 

2025-07-19 Wk 29 Sat - 01:38

```sh
./docker_run.sh                                            

# out
docker: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Head "http://%2Fvar%2Frun%2Fdocker.sock/_ping": dial unix /var/run/docker.sock: connect: permission denied

Run 'docker run --help' for more information
```

### 4.1.1 Solution

```sh
sudo groupadd docker
sudo usermod -aG docker $USER
sudo systemctl restart docker
sudo chmod 666 /var/run/docker.sock
```

Then test with 

```sh
docker run hello-world
```

### 4.1.2 Journal

From this [answer](https://stackoverflow.com/a/48957722/6944447),

```sh
sudo groupadd docker
sudo usermod -aG docker $USER
sudo systemctl restart docker
```

Test using

```sh
docker run hello-world
```

Seems I have to do `newgrp docker` everytime for the hello-world to run?

The other [answer](https://stackoverflow.com/a/51362528/6944447) helped with this:

```sh
sudo chmod 666 /var/run/docker.sock
```

## 4.2 Input device is not TTY when launching docker shell from web

- [x] 

2025-07-19 Wk 29 Sat - 03:42

This works:

```sh
# in /home/lan/src/cloned/gh/delta-domain-rnd/delta-box
TAG="ubuntu_25.04" MOUNT="." box/box000_blank_system/docker_sh.sh
```

But not this:

```sh
curl -sSf https://raw.githubusercontent.com/delta-domain-rnd/delta-box/refs/heads/main/box/box000_blank_system/docker_sh.sh | TAG="ubuntu_25.04" MOUNT="." sh

# out
the input device is not a TTY
```

2025-07-19 Wk 29 Sat - 03:48

Removing `-it` as it's not necessary in `docker_sh.sh` as suggested by this [answer](https://stackoverflow.com/a/57565119/6944447).

No effect even when removing it...

Note that if we just move that to a file and run the file after `chmod +x` it will work!

As per this [issue](<https://github.com/NixOS/nix/issues/11714>),

We are able to get TTY with

```sh
TAG="ubuntu_25.04" MOUNT="." sh <(curl -L https://raw.githubusercontent.com/delta-domain-rnd/delta-box/refs/heads/main/box/box000_blank_system/docker_sh.sh)
```


## 4.3 Unable to use apt install within box

- [x] 

```sh
apt install python3

# out
Error: Could not open lock file /var/lib/dpkg/lock-frontend - open (13: Permission denied)
Error: Unable to acquire the dpkg frontend lock (/var/lib/dpkg/lock-frontend), are you root?
```

Diagnostics similar to those running in this [post](https://forums.docker.com/t/not-able-to-install-anything-with-apt-get-on-a-docker-container/1595),

```
$ uname -r
6.14.0-23-generic
$ uname -a
Linux c06c8bc1c33e 6.14.0-23-generic #23-Ubuntu SMP PREEMPT_DYNAMIC Fri Jun 13 23:02:20 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
```

2025-07-19 Wk 29 Sat - 05:38

LLM Assist is [[Wk 29 000 - Use apt install within Ubuntu container|here]].

2025-07-19 Wk 29 Sat - 06:54

Had to install tzdata for automated apt installs without it asking me for timezone info. And created a root shell starting script.

# 5 Investigation

## 5.1 On Dockerfile and compose.yml

2025-07-19 Wk 29 Sat - 01:00

In this [forum question](https://stackoverflow.com/questions/29480099/whats-the-difference-between-docker-compose-vs-dockerfile), multiple explain how compose.yml is more to do with orchastrating how docker images are run especially in case of multiple... Of course we can also use the CLI to pass specific options in.

# 6 Howtos

## 6.1 Grepping Docker Containers

- [x] 

2025-07-19 Wk 29 Sat - 02:15

For grepping for currently  running containers, we can like this [answer](https://stackoverflow.com/a/69461585/6944447) do

```sh
docker ps -a --no-trunc --filter name=^/foo$
```

## 6.2 Getting Nth line in shell

- [x] 

2025-07-19 Wk 29 Sat - 02:16

From this [answer](https://stackoverflow.com/a/1429628/6944447),

```bash
ls -l | sed -n 2p
```

where 2 is the 2nd line.

## 6.3 Image arguments to DockerFile

- [x] 

2025-07-19 Wk 29 Sat - 02:47

[Reference](<https://docs.docker.com/reference/dockerfile/#arg>).


```
 - InvalidDefaultArgInFrom: Default value for ARG $image results in empty or invalid base image name (line 4)

```

Giving it a default value seemed to fix this:

```
ARG image=ubuntu:25.04
```

## 6.4 Looping over lines in shell

- [x] 

2025-07-19 Wk 29 Sat - 04:36

Similar to [answer](https://unix.stackexchange.com/a/670764),

```sh
echo 'A B C' | tr ' ' '\n' | while read line; do echo line: $line; done

# out
line: A
line: B
line: C
```

## 6.5 Space separated to line separated in shell

- [x] 

2025-07-19 Wk 29 Sat - 04:44

From [answer](https://stackoverflow.com/a/24704539/6944447),

```sh
echo 'foo bar boo you too' | tr ' ' '\n'
```

## 6.6 Simulating exit signals in Linux shell

- [x] 


2025-07-19 Wk 29 Sat - 06:27

```sh
$ ( exit 0 ) && echo 'haha'
haha
$ ( exit 34 ) && echo 'haha'
$ echo $?
34
```

For values >256, it just mods 256.

```sh
$ ( exit 258 ) && echo 'haha'
$ echo $?
2
```

# 7 References

1. [gh docker awesome-compose ex](https://github.com/docker/awesome-compose/tree/master/wireguard) ^1

**Search**

1. techoverflow.net/2021/01/13/how-to-use-apt-install-correctly-in-your-dockerfile/