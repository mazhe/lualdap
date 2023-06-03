# TESTING

Except the `tests/smoke.lua`, all tests require a LDAP server (by default available on localhost).

Except the `tests/smoke.lua`, there is no test running on Windows.

With old releases (ie. without `smoke.lua`), the following command could be a minimal smoke test :

```
$ LUA_CPATH="./src/?.so" lua -l lualdap -v
```

## setup a LDAP server

You could install/setup `slapd` (the OpenLDAP server) in your environment,
or run a Docker image containing `slapd`

[openshift/openldap-2441-centos7](https://hub.docker.com/r/openshift/openldap-2441-centos7)
is an image on-the-shelf which was built from source available on <https://github.com/openshift/openldap>.

```
# docker pull openshift/openldap-2441-centos7

# docker image ls
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
openshift/openldap-2441-centos7   latest              9bae1ab605d5        2 years ago         355MB
```

```
# docker run -d --rm --name openldap -p 389:389 -p 636:636 openshift/openldap-2441-centos7
3d557fc1136fffe91def479ee5cd445da1b974a4809520795a79e1836e62f23e

# docker ps
CONTAINER ID        IMAGE                             COMMAND                    CREATED              STATUS              PORTS                                        NAMES
3d557fc1136f        openshift/openldap-2441-centos7   "/usr/local/bin/run-..."   About a minute ago   Up 56 seconds       0.0.0.0:389->389/tcp, 0.0.0.0:636->636/tcp   openldap

# docker kill openldap
```

## tests.old/test.lua

This is the original test suite coming from the Kepler Project.

It is a pure lua script without any dependency.

The following command:

```
$ LUA_CPATH="./src/?.so" tests.old/test.lua localhost:389 dc=example,dc=com cn=person,dc=example,dc=com cn=Manager,dc=example,dc=com admin
```

gives:

```
basic checking ..........................
Warning!  Couldn't connect with TLS.  Trying again without it.................. OK !
checking compare operation ................. OK !
checking basic search operation ........................................... OK !
checking add operation .............. OK !
checking modify operation ................. OK !
checking advanced search operation .............. OK !
checking rename operation .................. OK !
checking delete operation ............... OK !
closing everything ... OK !
```

## tests/test.lua

This is a port of the original test suite on the top of the framework [busted](http://olivinelabs.com/busted/).

`busted` could generate a JUnit XML output which allows nice integration with CI.

Previously, the parameters were passed by argument of the command line,
now there are passed with environment variables (see `test.env`)

The following command:

```
$ tests/openshift/setup.sh   # one time, after starting the docker openshift

$ . tests/openshift/test.env && tests/test.lua
```

gives:

```
154 successes / 0 failures / 0 errors / 0 pending : 0.460882 seconds
```

Or via the `Makefile`, just `make check`.
