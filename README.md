Indigo-dc.galaxycloud-refdata
=============================

Reference data ansible role for indigo-dc.galaxycloud.
The role provider reference data and the corresponding galaxy configuration.

Three reference data source are supported
- onedata ------> Onedata space with reference data is mounted
- cvmfs --------> CernVM-FS repository with reference data is mounted
- download -----> Reference data download

1. Onedata space: https://groundnuty.gitbooks.io/onedata-documentation/content/index.html
   A space hosting the reference data is mounted exploiting onedata.

   onedata_repository: true
   cvmfs_repository: false
   download: false

2. A CernVM-FS server is used to provide reference data: http://cvmfs.readthedocs.io/en/stable/index.html
   
   onedata_repository: false
   cvmfs_repository: true
   download: false

3. All the reference file are downloaded on '/refdata' (this needs >100GB free space on /refdata directory).

   onedata_repository: false
   cvmfs_repository: false
   download: true

Requirements
------------

By default the role use onedata to provide reference data. To do that 'oneclient' needs to be installed on your system.
The role depends on indigo-dc.oneclient role and install it automatically.

When a CernVM-FS server is used, the role run indigo-dc.cvmfs-client is automatically run to install cvmfs.

Role Variables
--------------

galaxy_flavor: "galaxy-no-tools" -> if different from 'galaxy-no-tools' check if all tools installed using https://github.com/indigo-dc/ansible-galaxy-tools have been correctly installed

get_refdata: true -> Enable reference data configuration.

refdata_repository_name: 'repo_name' -> Onedata space, CernVM-FS repository name or subdirectory download dir.

refdata_provider_type: 'onedata' (default)
1. onedata ------> Onedata space with reference data is mounted
2. cvmfs --------> CernVM-FS repository with reference data is mounted
3. download -----> Reference data download

refdata_provider: 'provider' -> Set onedata provider 

refdata_token: 'access_token' -> Set onedata access token

refdata_cvmfs_server_url: 'url' -> Set CernVM-FS server (stratum 0 or Replica) address without 'http://' string, e.g. single ip address.

refdata_cvmfs_repository_name: '{{ reponame }}' -> You can set a different cvmfs repository name, overwriting the default option, which point to refdata_repository_name.

refdata_cvmfs_key_file: '{{ repokey }}' -> SSH public key to mount the repository

refdata_cvmfs_proxy_url: '{{ url }}' -> proxy address

refdata_cvmfs_proxy_port: 80 -> Proxy port

Dependencies
------------
```
dependencies:
  - { role: indigo-dc.oneclient, when: refdata_provider_type == 'onedata' }
  - { role: mtangaro.cvmfs-client, server_url: '{{ refdata_cvmfs_server_url }}', repository_name: '{{ refdata_cvmfs_repository_name }}', cvmfs_public_key: '{{ refdata_cvmfs_key_file }}', proxy_url: '{{ refdata_cvmfs_proxy_url }}', proxy_port: '{{ refdata_cvmfs_proxy_port }}', cvmfs_mountpoint: '{{ refdata_dir }}', when: refdata_provider_type == 'cvmfs' }
```
Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

1. Run the role with default options: Onedata
```
    - hosts: servers
      roles:
        - role: indigo-dc.galaxycloud-refdata
          galaxy_flavor: "galaxy-no-tools"
          get_refdata: true
```
2. Onedata configuration:
```
    - hosts: servers
      roles:
        - role: indigo-dc.galaxycloud-refdata
          get_refdata: true
          refdata_repository_name: 'onedata_space_name'
          refdata_provider_type: 'onedata'
          refdata_provider: 'oneprovider'
          refdata_token: 'access_token'
```
3. CernVM-FS configuration:
```
    - hosts: servers
      roles:
        - role: indigo-dc.galaxycloud-refdata
          get_refdata: true
          refdata_repository_name: 'cvmfs_server_name'
          refdata_provider_type: 'cvmfs'
          refdata_cvmfs_server_url: '{{ ip }}'
          refdata_cvmfs_repository_name: '{{ reponame }}'
          refdata_cvmfs_key_file: '{{ repokey }}'
          refdata_cvmfs_proxy_url: '{{ ip }}'
          refdata_cvmfs_proxy_port: 80
```
4. Download configuration:
```
    - hosts: servers
      roles:
        - role: indigo-dc.galaxycloud-refdata
          get_refdata: true
          refdata_repository_name: 'subdir'
          refdata_provider_type: 'download'
```
License
-------

Apache Licence v2 [2]
http://www.apache.org/licenses/LICENSE-2.0

References
-------

https://galaxyproject.org/

Author Information
------------------

Marco Tanagaro (ma.tangaro_at_ibbe.cnr.it)
