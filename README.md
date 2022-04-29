gitlab-runner
=============

This role installs, configures and registers GitLab Runners on a server
for shared use. It can also install Docker Machine, which is needed for
autoscaling runners that run on remote clouds.

The supported runner types are shell, docker and docker+machine. If the
docker runner is used, then a cron job is deployed to clean the docker
cache weekly.


Requirements
------------

 * Python PIP
 * python-gitlab (will be installed on client)
 * python2-toml (on Ansible control node)
 * TOML filter plugin
([toml.py](https://github.com/sivel/toiletwater/blob/master/plugins/filter/toml.py)) from sivel.toiletwater collection

Role Variables
--------------

    gitlab_runners_pkg:
      Name of GitLab Runner package (default: gitlab-runner).
    gitlab_runners_pkg_state:
      Install state of GitLab Runner package (default: latest).
    gitlab_repo_script:
      URL for the GitLab repository install script.
    gitlab_dockerm_url:
      URL to retrieving the docker machine binary.
    gitlab_host:
      Inventory name of parent GitLab host (optional), used to register
      the runner. If defined, gitlab_url and gitlab_token must also be
      defined.
    gitlab_url:
      URL for GitLab API.
    gitlab_token:
      API token for GitLab host.
    gitlab_runners_token:
      GitLab runner registration token; if not set, it will be retrieved
      from the Rails interface on the GitLab host if possible.
    gitlab_runners_cache_token:
      Whether to cache the GitLab runner registration token locally to
      save time on repeated runs (boolean, default: true).
    gitlab_runners_cache_file:
      File path for storing GitLab runner registration token (default:
      /root/.gitlab_registration_token).
    gitlab_runners_concurrent:
      Number of concurrent jobs across all runners (default: 1).
    gitlab_runners_session_server_listen:
      Listen address for the session server (optional).
    gitlab_runners_reconfig:
      Whether to update the runner configuration file anyway, even if no
      new runners are being registered (e.g. to change a setting for an
      existing runner). (Boolean, default: false)
    gitlab_runners:
      List of runners to register and configure, each containing:
      - name: "Name of runner" (must be unique and constant, and if this
          runner is already registered then the name must match)
        executor: shell|docker|docker+machine
        tags: (optional, list)
        access_level: ref_protected|not_protected (optional)
        cache_dir:
        limit: max no. of concurrent jobs (default: 1)
        cache_s3_serveraddress: hostname for S3 cache service (if used)
        cache_s3_accesskey:
        cache_s3_secretkey:
        cache_s3_bucket: name of S3 bucket
        docker_image:
        docker_privileged: (boolean, default: false)
        docker_volumes: (list, must include cache_dir if defined)
        machine_idlecount:
        machine_maxbuilds: (default: 1)
        machine_driver:
        machine_name: (-%s is appended by default)
        machine_options: (list)
        extra_config: insert arbitrary extra runner config here (block)
      N.B. Docker settings must be defined for both docker and dockerm
      executors.
    
Dependencies
------------

None

Example Playbook
----------------

``` yaml
    - hosts: runner_hosts
      vars:
        gitlab_host: 'mygit.example.com'
        gitlab_url: 'https://mygit.example.com/'
        gitlab_token: 'MyGitLabAPIToken'
        gitlab_runners:
        - name: 'GitLab Docker runner'
          executor: docker
          docker_image: alpine
      roles:
         - { role: gitlab-runner }
```

License
-------

BSD

Author Information
------------------

Ade Rixon  
Matthew Moore  
UITGB, Cardiff University  
