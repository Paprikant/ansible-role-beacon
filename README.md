paprikant.beacon
================

![lint](https://github.com/paprikant/ansible-role-beacon/actions/workflows/lint.yml/badge.svg) ![test](https://github.com/paprikant/ansible-role-beacon/actions/workflows/test.yml/badge.svg)

An ansible role that sets up a running instance of [beacon-python](https://beacon-python.readthedocs.io/en/latest/), with an accompaning PostgreSQL database. Both services are exposed via network - refer to the _Role Variables_ section for default ports and credentials.

This role is using the official [postgres](https://hub.docker.com/_/postgres) and [beacon-python](https://hub.docker.com/r/cscfi/beacon-python/) docker images. Hence, docker is installed as a requirement to run beacon-python.  

Requirements
------------

The role uses the community.docker collection, which can be installed from ansible galaxy:

    ansible-galaxy collection install community.docker

Role Variables
--------------

Available variables are listed below, along with default values (see defaults/main.yml):

```yaml
shared_network_name: beacon-python
```

Name of a docker network that is shared between postgres and beacon-python containers. There is not much reason to change this.

```yaml
postgres_container_name: beacon-python-db
postgres_version: 13
```

PostgreSQL container name and version. The version may be any valid tag of the [official PostgreSQL image](https://hub.docker.com/_/postgres)

```yaml
postgres_external_binding: 5432
```
The port on which to expose the PostgreSQL database. You may also give an ip:port combination to expose the database on a specific network interface only.

```yaml
postgres_user: beacon
postgres_pass: change_me!
```
Credentials of the database user. The specified user is created on first start - for more information checḱ out the `POSTGRES_USER` and `POSTGRES_PASSWORD` environment variables in the docs of the PostgreSQL image. It is recommended to (at least) change the password.

> :warning: **Do not change these credentials after the first playbook execution**: Ansible will not drop the beacon database and as a result no other user will get access. (unless you manually create a new user using the old credentials first)

```yaml
postgres_database_name: beacon
```
Name of the database used by beacon-python. Will be created on initial playbook execution - for more information checḱ out the `POSTGRES_DATABASE` environment variables in the docs of the PostgreSQL image.

```yaml
postgres_data_dir: /var/lib/postgresql/data
```
Where to store PostgreSQL database files.

```yaml
postgres_init_dir: /var/lib/postgresql/init
```
Ansible will put a `.sql` file here that is used to create the tables of the beacon database.


```yaml
bp_container_name: beacon-python
bp_version: latest
```
Container name and version for beacon-python. Use any [tag of the beacon-python docker image](https://hub.docker.com/r/cscfi/beacon-python/tags) as bp_version.

```yaml
bp_external_binding: 5050
```
The port on which to expose the beacon API. You may also give an ip:port combination to expose the API on a specific network interface only. The protocol will be HTTP since beacon-python does not support HTTPS yet.

Dependencies
------------

None.

Example Playbook
----------------

Minimal plabook (since changing the database password is recommended in most use cases):

```yaml
- hosts: all
  tasks:
    - name: "Include paprikant.beacon"
      include_role:
        name: "paprikant.beacon"
      vars:
        postgres_pass: please_use_a_secure_password
```

License
-------

BSD
