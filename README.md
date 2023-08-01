# nss-external

## About

Linux nss module to create some users when requesting info about them
(on ssh, id, etc.).

If user matches suffix, then user is created with no password
(not empty but password not allowed) and assign them to specified group.
A home directory is also created.

Reads its configuration from `nss_external.conf`

As no password is set, login as user must be allowed/forbidden by PAM
modules/config (ssh key, custom auth, ...)

This library has been created to learn about nss/pam while doing some tests
with OpenSSH. Indeed, even with custom PAM modules, OpenSSH requires to get
an existing user (with nss stuff) before calling PAM modules to check for
auth, set session etc... So it means that users cannot be created
dynamically on SSH session setup.

I yoinked and updated this to be used with SSH certificate auth, which has the
issue of not working at all (not even calling PAM!) if the user doesn't exist.

## Install

`wget 'https://github.com/TheToddLuci0/nss-external/releases/latest/download/libnss_external.so.2' -o /usr/lib/x86_64-linux-gnu/libnss_external.so.2`



## Config

Setup in /etc/nss_external.conf

    users: []
    nss:
    prefix:
        - "@elixir-europe.org"
    groupid: 1000
    minuid: 10000
    bash: "/bin/bash"
    home: "/home/external/%s"

Update /etc/nsswitch.conf

    passwd:         compat external
    group:          compat
    shadow:         compat external

## Build

`CGO_CFLAGS="-g -O2 -D __LIB_NSS_NAME=external" go build --buildmode=c-shared -o libnss_external.so.2 nss-external.go`

## License

Apache 2.0
