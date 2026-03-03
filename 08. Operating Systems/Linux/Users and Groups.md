# Linux users and groups management

* `RUID` Real user id
* `EUID` Effective user id

## User information

```shell
whoami           # print effective user id `EUID` name
who              # print real user id `RUID` names of current logged users
last             # print login log

id alex          # show user id, primary group id, secondary groups
groups alex      # show groups user belongs to

less /etc/passwd # user list
```


## Become/run as user

```shell
sudo su                # become super user ("superuser do" "substitute user")
sudo su alex           # become user `alex` ("superuser do" "substitute user")

sudo -u www-data bash
```


## Create user

```shell
useradd alex             # to create user with login `alex`
useradd -m alex          # to create user with login `alex` with home directory named by his login
useradd -G sudo,adm alex # to create user with login `alex` and add to sudo, adm groups

passwd alex              # to set password for user `alex`. Some users don't have a password (`root`) and you cannot log in unless the password set
```

## Change/delete user

```shell
usermod                                # has the same options as `useradd` has
usermod -G developers,managers alex    # to change user groups. It will rewrite current secondary groups
usermod -aG sudo alex                  # to add user to groups (`-a` option)

userdel -r alex                        # to remove user `alex` with home directory (`-r` option)
```

##  Group management

```shell
groupadd developers             # to create a new group
groupmod -n new-name old-name   # to rename group
groupdel developers             # to delete group
```

---

## Superuser

* `sudo` is a superuser group for `debian` based distribution
* `wheel` is a superuser group for `CentOs` based distribution


## /etc/passwd
`/etc/passwd` stores information about each user in a system

`alex:x:1000:1000:Alex,,,:/home/alex:/bin/bash`
1. `alex` login in system
2. `x` means the password was saved in `/etc/shadow`. If empty, then the user doesn't have to enter login
3. `1000` User ID
4. `1000` primary Group ID
5. `Alex,,,` comment
6. `/home/alex` home directory
7. `/bin/bash` default shell. If set to `/usr/sbin/nologin` or `/bin/false` then user is not allowed to log in to shell (system user)

## /etc/shadow
`/etc/shadow` stores encrypted users passwords and some related information

`alex:$6$5aFI/6Mo9npR9ad.4hOKDiq0QGf:18564:0:99999:7:::`
1. `alex` login
2. `$6$5aFI/6Mo9npR9ad.4hOKDiq0QGf` password. If `!` or `*` user will not be able to login
3. rest of fields represents some dates related to password (expirations etc)

## /etc/group
`/etc/group` store secondary groups of users
User can be belong to two types of groups:
1. `Primary group` id stored in `/etc/passwd` and group name in `/etc/group`
2. `Secondary group` stored in `/etc/group`

`adm:x:4:syslog,alex`
