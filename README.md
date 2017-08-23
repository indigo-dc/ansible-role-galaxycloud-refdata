Indigo-dc.galaxycloud-refdata
=============================
Reference data ansible role for indigo-dc.galaxycloud.
The role provider reference data and the corresponding galaxy configuration.

Three reference data source are supported:
1. cvmfs: CernVM-FS repository is used to provide reference data. It is mounted to ``/refdata``.
2. onedata: Onedata space hosting reference data is mounted to ``/refdata``.
3. download: Reference data are downloaded in ``/refdata`` (requires >100GB free space available on /refdata directory).

Moreover, this role, exploiting the python library Ephemeris, is able to check which tools have been installed through indigo-dc.galaxy-tools ansible role, and returns

- the list of installed tools stored in ``/var/log/galaxy/galaxy-installed-tool-list.yml`` in yaml format
- the list of missing tools stored in ``/var/log/galaxy/galaxy-missing-tool-list.yml`` in yaml format

 This option has been introduced for galaxy tools automatic deployment. If you need to install and configure reference data, you can disable it using ``galaxy_flavor: "galaxy-no-tools``.

Requirements
------------
When a CernVM-FS server is used, the role run the indigo-dc.cvmfs-client ansible role as dependency to install and configure the cvmfs client.

If the role use onedata to provide reference data, onedata command line tool ``oneclient`` needs to be installed on your system.
In this case,the role is going to depend on indigo-dc.oneclient role and it will install oneclient automatically.

Finally, if the download option is selected, the role exploits a python script to download the reference data, which depends on python-pycurl (which is automatically installed).

Role Variables
--------------
```yaml
  galaxy_flavor: "galaxy-no-tools" 
```
If different from 'galaxy-no-tools' the role will check if all tools installed using https://github.com/indigo-dc/ansible-galaxy-tools have been correctly installed. Possible ``galaxy_flavor`` value with the correspinding recipes are reported here: :doc:`feat_galaxy_tools`.

```yaml
  get_refdata: true
```
Enable reference data configuration. If set to ``false`` this variable disable reference data configuration.

```yaml
refdata_provider_type: 'cvmfs'
```
Takes three possible values:

1. ``cvmfs``: CernVM-FS repository with reference data is mounted
2. ``onedata``: Onedata space with reference data is mounted
3. ``download``: Reference data download

```yaml
refdata_repository_name: '<repo_name>'
```
Onedata space, CernVM-FS repository name or subdirectory to download local reference data.

### cvmfs variables ###

```yaml
refdata_cvmfs_server_url: '90.147.102.186'
```
Set CernVM-FS server (stratum 0 or Replica) address without 'http://' string, e.g. single ip address.

```yaml
refdata_cvmfs_repository_name: 'refdata-italy.galaxy.refdata'
```
You can set a different cvmfs repository name, overwriting the default option, which point to refdata_repository_name.

```yaml
refdata_cvmfs_key_file: 'repository_key.pub'
```
SSH public key to mount the repository

```yaml
refdata_cvmfs_proxy_url: 'DIRECT'
```
Proxy address (default ``DIRECT``).

```yaml
refdata_cvmfs_proxy_port: 80
```
Proxy port (default ``80``).

### onedata variable ###

```yaml
refdata_provider: 'oneprovider2.cloud.ba.infn.it'
```
Set reference data oneprovider

```yaml
refdata_token: 'MDAxNWxvY2F00aW9uIG9uZXpvbmUKMDAzYmlkZW500aWZpZXIgeExqMi00xdFN3YVp1VWIxM1dFSzRoNEdkb2x3cXVwTnpSaGZONXJSN2tZUQowMDFhY2lkIHRpbWUgPCAxNTI1MzM00NzgyCjAwMmZzaWduYXR1cmUgIOzeMtypO75nZvPJdAocInNbgH9zvJi6ifgXDrFVCr00K'
```
Set reference data access token.

```yaml
refdata_space: '{{ refdata_repository_name }}'
```
Set reference data space name.

### download ###

```yaml
at10: false # A. thaliana (TAIR 10)
at9: false # A. thaliana (TAIR 9)
dm2: false # D. melanogaster (dm2)
dm3: false # D. melanogaster (dm3)
hg18: false # H. sapiens (hg18)
hg19: false # H. sapiens (hg19)
hg38: false # H. sapeins (hg38)
mm10: false # M. musculus (mm10)
mm8: false # M. musculus (mm9)
mm9: false # M. musculus (mm8)
sacCer1: false # S. cerevisiae (sacCer1)
sacCer2: false # S. cerevisiae (sacCer2)
sacCer3: true # S. cerevisiae (sacCer3)
```
Select which reference data genome has to be downloaded.  

Dependencies
------------
For cvmfs server reference data providere, the role depends on indigo-dc.cvmfs-client role, which takes as input parameters the CernVM-FS server location details (stratum 0 address, public key and mount point).

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

For onedata reference data provider, the role depends on indigo-dc.oneclient role:

::

  - hosts: servers
    roles:
      - role: indigo-dc.oneclient
        when: refdata_provider_type == 'onedata'

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

Apache Licence v2

http://www.apache.org/licenses/LICENSE-2.0

References
-------

https://galaxyproject.org/

Author Information
------------------

Marco Tanagaro (ma.tangaro_at_ibbe.cnr.it)
