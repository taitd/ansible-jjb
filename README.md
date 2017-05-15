jjb
===

Ansible role which helps to install and run Jenkins Job Builder.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```
- name: Only install and configure JJB
  hosts: myhost
  roles:
    - jjb

- name: Install JJB and run projects
  hosts: myhost
  vars:
    jjb_projects:
      - src: https://github.com/openstack-infra/jenkins-job-builder.git
        # Where to clone repo
        dest: /opt/jjb/jenkins-job-builder
        # File from the clonned repo which contains the JJB definitions
        file: tests/cmd/fixtures/cmd-001.yaml
      - src: https://github.com/openstack-infra/jenkins-job-builder.git
        dest: /opt/jjb/jenkins-job-builder
        file: tests/cmd/fixtures/cmd-002.yaml
        # Do not try to update the repo if it exists
        pull: no
      - src: https://gist.github.com/cfc1fd6137a486816bac.git
        dest: /opt/jjb/ci-workflow
        file: ci-workflow-defaults.yml
        # Set directory permissions
        owner: jenkins
        group: jenkins
        mode: "0750"
        # Clone the project but do not run it
        run: no
      - src: https://github.com/openstack-infra/jenkins-job-builder.git
        dest: /opt/jjb/jenkins-job-builder
        file: tests/cmd/fixtures/cmd-002.yaml
        # Delete all jobs from this project
        state: absent
  roles:
    - jjb
```


Role variables
--------------

```
# Whether to install EPEL YUM repo
jjb_epel_install: "{{ yumrepo_epel_install | default(true) }}"

# EPEL YUM repo URL
jjb_epel_yumrepo_url: "{{ yumrepo_epel_url | default('https://dl.fedoraproject.org/pub/epel/$releasever/$basearch/') }}"

# EPEL YUM repo GPG key
jjb_epel_yumrepo_gpgkey: "{{ yumrepo_epel_gpgkey | default('https://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-$releasever') }}"

# Additional EPEL params
jjb_epel_yumrepo_params: "{{ yumrepo_epel_params | default({}) }}"


# Default dependencies
jjb_pkgs__default:
  - git
  - python-pip

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


# Path to the JJB config
jjb_config_path: /etc/jjb.cfg

# Default option values of the default job_builder section
jjb_config_job_builder_ignore_cache: yes
jjb_config_job_builder_keep_descriptions: no
jjb_config_job_builder_include_path: .:scripts:~/git/
jjb_config_job_builder_recursive: no
jjb_config_job_builder_exclude: .*:manual:./development
jjb_config_job_builder_allow_duplicates: no

# Default options of the job_builder section
jjb_config_job_builder__default:
  ignore_cache: "{{ jjb_config_job_builder_ignore_cache }}"
  keep_descriptions: "{{ jjb_config_job_builder_keep_descriptions }}"
  include_path: "{{ jjb_config_job_builder_include_path }}"
  recursive: "{{ jjb_config_job_builder_recursive }}"
  exclude: "{{ jjb_config_job_builder_exclude }}"
  allow_duplicates: "{{ jjb_config_job_builder_allow_duplicates }}"

# Cutom options of the job_builder section
jjb_config_job_builder__custom: {}

# Final job_builder section
jjb_config_job_builder: "{{
  jjb_config_job_builder__default.update(jjb_config_job_builder__custom) }}{{
  jjb_config_job_builder__default }}"


# Default option values of the default jenkins section
jjb_config_jenkins_user: admin
jjb_config_jenkins_password: admin
jjb_config_jenkins_url: http://localhost:8080
jjb_config_jenkins_query_plugins_info: no

# Default options of the jenkins section
jjb_config_jenkins__default:
  user: "{{ jjb_config_jenkins_user }}"
  password: "{{ jjb_config_jenkins_password }}"
  url: "{{ jjb_config_jenkins_url }}"
  query_plugins_info: "{{ jjb_config_jenkins_query_plugins_info }}"

# Custom options of the jenkins section
jjb_config_jenkins__custom: {}

# Final jenkins section
jjb_config_jenkins: "{{
  jjb_config_jenkins__default.update(jjb_config_jenkins__custom) }}{{
  jjb_config_jenkins__default }}"


# Final config
jjb_config:
  jenkins: "{{ jjb_config_jenkins }}"
  job_builder: "{{ jjb_config_job_builder }}"


# List of projects to install (user must define this variable)
jjb_projects: []
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)
- [`jenkins_master`](https://github.com/jtyr/ansible-jenkins_master) (optional)


License
-------

MIT


Authors
-------

Dennis Tait, Jiri Tyr
