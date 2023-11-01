## Ansible role - Registry

### General informations

Ansible role to install and configure the Docker Registry (using binary installation).

**Table of Contents**

- [General informations](#general-informations)
- [Role variables](#role-variables)
- [Examples](#examples)
- [Install and use this role](#install-and-use-this-role)

**Supported Platforms**

This role only supports Ubuntu Server LTS platforms:

  - Ubuntu 22.04 *Jammy Jellyfish*

**Requirements**

  - None

**References**

  - Docker Registry : https://docs.docker.com/registry/
  - Distribution : https://github.com/distribution/distribution

**Implementation notes**
  
  - **Binary installation** : this roles installs the Registry using the official binary, no other methods will be supported (such as Docker container).

### Role variables

| Name                              | Default                      | Description                                                      |
| :-------------------------------- | :--------------------------- | :--------------------------------------------------------------- |
| `registry_version`                | `2.8.3`                      | Defines the version of the Registry to install                   |
| `registry_install_dir`            | `/usr/local/bin`             | Defines the Registry binary installation directory               |
| `registry_config_dir`             | `/etc/registry`              | Defines the Registry configuration directory                     |
| `registry_config_path`            | `{{ registry_config_dir }}/config.yaml`|  Defines the absolute path to the Registry configuration file |
| `registry_config_template`        | `{{ registry_config_dir }}/config.yaml`|  Defines the path to the default template used to render custom Registry configuration |
| `registry_data_dir`               | `/var/lib/registry`          | Defines the data directory where all registry files are stored   |
| `registry_logs_dir`               | `/var/log/registry`          | Defines the logs directory of the Registry                       |
| `registry_bin_archive_url`        | see [vars](vars/main.yml)    | Defines the URL where to download the Registry binary archive    |
| `registry_bin_archive_checksum_url`| see [vars](vars/main.yml)    | Defines the Registry binary archive remote checksum file        |
| `registry_bin_update`             | `false`                      | If set to `true`, force the Registry binary update               |
| `registry_configuration`          | see [defaults](defaults/main.yml) | Defines a YAML dict containing the Registry configuration (see [examples](€examples) below |

### Examples

By default, the configuration of the Registry is pretty simple, it's a YAML dictionnary:

```YAML
registry_configuration:
  version: 0.1  # Required
  log:
    level: info
  storage:      # Required
    filesystem:
      rootdirectory: "{{ registry_data_dir }}"
    maintenance:
      uploadpurging:
        enabled: true
        age: 168h
        interval: 24h
        dryrun: true
  http:
    addr: 0.0.0.0:5000
    host: https://my.registry.internal:5000
  # Configure the registry as pull-through cache for the Docker Hub
  proxy:
    remoteurl: https://registry-1.docker.io
    username: "{{ registry_username }}"
    password: "{{ registry_password }}"
```

More informations about configuration options here: https://distribution.github.io/distribution/about/configuration/ 

***Note***: you can also override the default template provided by the role to bring your custom template for your own needs using the `registry_config_template` variable.

### Install and use this role

* Install the role using the command-line :

  ```shell
  $ ansible-galaxy role install git+https://github.com/f-bn/ansible-role-registry.git
  ```

* You can also install the role in your projects using a `requirements.yml` file and `ansible-galaxy` command-line :

  ```YAML
  $ cat requirements.yml
  ---
  roles:
    - name: registry
      src: https://github.com/f-bn/ansible-role-registry.git
      scm: git
      version: '1.0.0'

  $ ansible-galaxy install-f -r requirements.yml
  ```

* Once the role is installed, you can use it in your playbooks :

  ```yaml
  - name: Deploy
    hosts: <hosts>
    roles:
      - role: registry
  ```
