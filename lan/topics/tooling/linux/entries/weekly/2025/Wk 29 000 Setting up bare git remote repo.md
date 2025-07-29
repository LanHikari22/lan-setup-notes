---
status: done
---
#lan #git #setup #entry

# 1 Objective

I would like to setup a repository in a server that acts as origin. That way I can push and pull from it from multiple devices.

# 2 Journal

## 2.1 Creating bare repo

- [x] 

2025-07-17 Wk 29 Thu - 21:40

Following process in this [answer](<https://stackoverflow.com/a/31590993/6944447>) by [adelphus](<https://stackoverflow.com/users/1132806/adelphus>),

```sh
# In the server
git init --bare /path/to/bare/repo.git

# In the client
git clone /path/to/bare/repo.git /path/to/work
git config user.email "email"
git config user.name "name"
```
^process


## 2.2 Adding SSH key to remote server

- [x] 

2025-07-17 Wk 29 Thu - 21:52

Instead of passwords, we need to add ssh key auth.

From this [tutorial](<https://linuxhandbook.com/add-ssh-public-key-to-server/>),

```sh
ssh-copy-id -i ~/.ssh/id_rsa.pub YOUR_USER_NAME@IP_ADDRESS_OF_THE_SERVER
```
^add-ssh-key

# 3 References