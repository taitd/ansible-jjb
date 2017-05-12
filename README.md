JJB
===========

Ansible role which helps to install and configure Jenkins Job Builder

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```
- name: Default JJB installation
  hosts: myhost1
  roles:
    - jjb

# Execute jenkins job builder file to create jenkins jobs
- name: Run Jenkins Job Builder project
  command: >
    jenkins-jobs
    --conf "{{ jjb_config_path }}"
    --user "{{ jjb_config_jenkins_user | default('admin') }}"
    --password "{{ jjb_config_jenkins_password | default('admin') }}"
    update "{{ item.config }}"
  args:
    chdir: "{{ item.dest }}"

```


Role variables
--------------

```
# Whether to install EPEL YUM repo
jjb_epel_install: "{{ yumrepo_epel_install | default(true) }}"

# EPEL YUM repo URL
jjb_epel_yumrepo_url: "{{ yumrepo_epel_url | default('https://dl.fedoraproject.org/pub/epel/' + ansible_distribution_major_version + '/' + ansible_userspace_architecture + '/') }}"

# EPEL YUM repo GPG key
jjb_epel_yumrepo_gpgkey: "{{ yumrepo_epel_gpgkey | default('https://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-$releasever') }}"

# Additional EPEL params
jjb_epel_yumrepo_params: "{{ yumrepo_epel_params | default({}) }}"


# Default dependencies
jjb_pkgs__default:
  - python-setuptools
  - python-devel
  - libyaml-devel
  - git
  - curl
  - python-pip
  - python-yaml

# Custom dependencies
jjb_pkgs__custom: []

# Final list of dependencies
jjb_pkgs: "{{
  jjb_pkgs__default +
  jjb_pkgs__custom }}"


# Default Python PIP dependencies
jjb_pip_pkgs__default:
  - jenkins-job-builder

# Custom Python PIP dependencies
jjb_pip_pkgs__custom: []

# Final list of Python PIP dependencies
jjb_pip_pkgs: "{{
  jjb_pip_pkgs__default +
  jjb_pip_pkgs__custom }}"


# PyPI source
jjb_pypi_host: pypi.python.org
jjb_pypi_url: https://"{{ jjb_pypi_host }}/simple/"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)
- [`jenkins_master`](https://github.com/jtyr/ansible-jenkins_master) (optional)
- [`oracle_java`](https://github.com/jtyr/ansible-oracle_java) (optional)


License
-------

MIT


Authors
-------

Dennis Tait, Jiri Tyr
