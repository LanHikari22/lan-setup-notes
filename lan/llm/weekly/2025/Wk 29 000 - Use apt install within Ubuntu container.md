\#lan #llm #st/resolved

# 1 Wk 29 000 - Use apt install within Ubuntu container

# 2 Objective

From [002 Creating base system for installation testing > 4.3 Unable to use apt install within box](../../../topics/tooling/linux/tasks/2025/002%20Creating%20base%20system%20for%20installation%20testing.md#43-unable-to-use-apt-install-within-box).

````sh
apt install python3

# out
Error: Could not open lock file /var/lib/dpkg/lock-frontend - open (13: Permission denied)
Error: Unable to acquire the dpkg frontend lock (/var/lib/dpkg/lock-frontend), are you root?
````

Diagnostics similar to those running in this [post](https://forums.docker.com/t/not-able-to-install-anything-with-apt-get-on-a-docker-container/1595),

````
$ uname -r
6.14.0-23-generic
$ uname -a
Linux c06c8bc1c33e 6.14.0-23-generic #23-Ubuntu SMP PREEMPT_DYNAMIC Fri Jun 13 23:02:20 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
````

# 3 Solution

For timezone,

````dockerfile
ARG image
RUN /bin/sh -c '\
  if echo "$image" | grep -q "^ubuntu"; then \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends tzdata && \
    ln -fs /usr/share/zoneinfo/Asia/Kolkata /etc/localtime && \
    echo "Asia/Kolkata" > /etc/timezone && \
    DEBIAN_FRONTEND=noninteractive dpkg-reconfigure -f noninteractive tzdata; \
  else \
   ( exit 0 ); \
  fi \
'
````

For root,

````
    docker run -d \
      -v $MOUNT:/mnt/ \
      --user root \
      --name $app_name \
      lan22h/$app_name:$TAG
````

We were specifically setting a different user.

````sh
$ whoami
root
$ id -u
0
````

# 4 LLM Instructions

* This is a diagnostic document and not a conversation. Everything shared is context. Address the questions tagged (Q#) like (Q1) for example. If you see something like (~1), assume it part of the archive and not a latest set of questions.
  * Since it keeps occurring, I ask Again
  * !!! NEVER RESPOND TO (~1), (~2), etc.
  * ONLY respond to the tagged questions. Nothing else.

(LLM chatgpt-4o)
(Note: Between these tags is responses from ChatGPT-4o.
(/LLM chatgpt-4o)

(LLM chatgpt-4o)
(/LLM chatgpt-4o)

# 5 Journal

2025-07-19 Wk 29 Sat - 05:41

````
$ whoami
ubuntu
$ id -u
1000
````

(Q 05:38) What's the issue?

We need to run this as root.

There's also some timezone things to resolve for ubuntu

(LLM chatgpt-4o)

````dockerfile
ARG image
RUN /bin/sh -c '\
  if echo "$image" | grep -q "^ubuntu"; then \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends tzdata && \
    ln -fs /usr/share/zoneinfo/Asia/Kolkata /etc/localtime && \
    echo "Asia/Kolkata" > /etc/timezone && \
    DEBIAN_FRONTEND=noninteractive dpkg-reconfigure -f noninteractive tzdata; \
  else \
   ( exit 0 ); \
  fi \
'
````

(/LLM chatgpt-4o)

We need `ARG image` there so it can interpret `$image`. I put `( exit 0 );` for testing in the else, in case we want to make sure this triggers or get a specific failure.

# 6 References

1. https://www.nushell.sh/
1. https://elv.sh/
