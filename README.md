Indigo-dc.galaxycloud-refdata
=============================

Reference data ansible role for indigo-dc.galaxycloud. The role provides reference data using the [CernVM File System](https://cernvm.cern.ch/portal/filesystem) and the corresponding galaxy configuration.

Requirements
------------
To correctly configure the CernVM File System the role run the indigo-dc.cvmfs-client ansible role as dependency to install and configure the CVMFS client.

```
indigo-dc.cvmfs-client
```

Role Variables
--------------
``galaxy_flavor``: if different from 'galaxy-no-tools' the role will check if all tools installed using https://github.com/indigo-dc/ansible-galaxy-tools have been correctly installed. Possible *galaxy_flavor* values with the correspinding recipes are reported here: :doc:`feat_galaxy_tools` (default: ``galaxy-no-tools``).

``get_refdata``: enable reference data configuration. If set to ``false`` this variable disable reference data configuration (default: ``true``).

### cvmfs variables ###

``refdata_cvmfs_server_url``: set CernVM-FS server (stratum 0 or Replica) address without 'http://' string, e.g. single ip address.

``refdata_cvmfs_repository_name``: set a different cvmfs repository name, overwriting the default option, which point to refdata_repository_name (e.g. ``elixir-italy.galaxy.refdata``).

``refdata_cvmfs_key_file``: SSH public key to mount the repository

``refdata_cvmfs_proxy_url``: proxy address (default ``DIRECT``).

``refdata_cvmfs_proxy_port``: proxy port (default ``80``).

Dependencies
------------
For cvmfs server reference data provider, the role depends on indigo-dc.cvmfs-client role, which takes as input parameters the CernVM-FS server location details (stratum 0 address, public key and mount point).

```yaml
- hosts: servers
  roles:
    - role: indigo-dc.cvmfs-client
      server_url: '90.147.102.186'
      repository_name: 'elixir-italy.galaxy.refdata'
      cvmfs_public_key: 'elixir-italy.galaxy.refdata.pub'
      proxy_url: 'DIRECT'
      proxy_port: '80'
      cvmfs_mountpoint: '/refdata'
      when:  refdata_provider_type == 'cvmfs'
```

Example Playbook
----------------

- Configure Galaxy with CernVM-FS reference data volume.
```yaml
- hosts: servers
  roles:
    - role: indigo-dc.galaxycloud-refdata
      galaxy_flavor: 'galaxy-no-tools'
      get_refdata: true
      refdata_cvmfs_server_url: '90.147.102.186'
      refdata_cvmfs_repository_name: 'elixir-italy.galaxy.refdata'
      refdata_cvmfs_key_file: 'elixir-italy.galaxy.refdata.pub'
      refdata_cvmfs_proxy_url: 'DIRECT'
```

- Configure Galaxy with CernVM-FS reference data volume with preconfigured config.d files
```yaml
- hosts: servers
  roles:
    - role: indigo-dc.galaxycloud-refdata
      galaxy_flavor: 'galaxy-no-tools'
      get_refdata: 'true'
      refdata_dir: '/cvmfs'
      refdata_cvmfs_configuration: 'cvmfs_preconfigured'
      refdata_cvmfs_repository_name: 'elixir-italy.galaxy.refdata'
      refdata_cvmfs_key_file: 'elixir-italy.galaxy.refdata.pub'
```

License
-------

Apache Licence v2

http://www.apache.org/licenses/LICENSE-2.0

References
----------

Galaxy project: https://galaxyproject.org

CernVM-FS: http://cvmfs.readthedocs.io/en/stable/index.html

Author Information
------------------

Marco Tanagaro (ma.tangaro_at_ibiom.cnr.it)
