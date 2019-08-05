# Virgo CVMFS client installation instructions

## 1. Client installation
The Virgo CVMFS client is the same software which is packed with the standard CVMFS distribution.
In order to get a functional environment please follow the official CVMFS documentation found at [link](https://cernvm.cern.ch/portal/filesystem/quickstart), up to the (experiment-specific) steps requiring creating and editing of the `default.local` file.

<!--
</br>
**Linux**

To install, proceed according to the following steps:

1. Install the CernVM-FS packages. With yum, run

	```
	sudo yum install cvmfs cvmfs-config-default
	```

	If yum does not show the latest packages, clean the yum cache by yum clean all. On Ubuntu, use
	
	```
	sudo apt-get install cvmfs cvmfs-config-default
	```

	If apt does not show the latest packages, run `sudo apt-get update` before.

2. For the base setup, run `cvmfs_config setup`. If you migrate from a previous version of CernVM-FS, check the release notes if there is anything special to do for migration.
3. Create `/etc/cvmfs/default.local` and open the file for editing.
4. Select the desired repositories by setting `CVMFS_REPOSITORIES=repo1,repo2,...`. For Virgo, for instance, set

	```CVMFS_REPOSITORIES=virgo.ego-gw.it```

	Specify the HTTP proxy servers on your site with

	```CVMFS_HTTP_PROXY="http://myproxy1:port|DIRECT"```

5. Check if CernVM-FS mounts the specified repositories by `cvmfs_config probe`. If the probe fails, try to restart autofs with `sudo service autofs restart`.-->

## 2. Public keys installation
The public keys needed for the access to the `virgo.ego-gw.it` CVMFS repository can be installed using this `.rpm` package contained in this repo at [link](https://github.com/gabrielefronze/cvmfs-client-virgo/raw/master/cvmfs-virgo.ego-gw.it-pub-key.rpm).
Download the linked file and install it via:

```
	rpm -i <path-to-the-rpm-file>/cvmfs-virgo.ego-gw.it-pub-key.rpm
```

The installed keys can be found at `/etc/cvmfs/keys/virgo.ego-gw.it.pub`.

To uninstall them it is enough to run:

```
	rpm -e cvmfs-virgo.ego-gw.it-pub-key
```


## 3. Virgo specific settings
Once the CVMFS client and the public keys `.rpm` have been correctly installed one can proceed with the creation of the file `/etc/cvmfs/config.d/virgo.ego-gw.it.conf`.

The file must include several lines:

1. ```CVMFS_SERVER_URL=http://cvmfs-virgo.cr.cnaf.infn.it/cvmfs/virgo.ego-gw.it```: to define the CNAF stratum-1 endpoint
2. ```CVMFS_PUBLIC_KEY=/etc/cvmfs/keys/virgo.ego-gw.it.pub```: to point to the public keys installed with the `.rpm`
3. ```CVMFS_HTTP_PROXY="http://local.proxy:port"```: to specify the local proxy to be used. Note that for machines external to a CC, hence not backed with a proxy, the option `"|DIRECT"` can be appended to connect to the stratum-1 directly.


## 4. Test
In order to test the setup and automatically mount the configured repository, please perform the following operations.

```
# cvmfs_config setup
# cvmfs_config chksetup
OK
# service autofs restart
Redirecting to /bin/systemctl restart autofs.service
# cvmfs_config probe virgo.ego-gw.it
Probing /cvmfs/virgo.ego-gw.it... OK
```

At that point you should be able to perform an `ls` of the repo mountpoint obtaining similar output:

```
# ls /cvmfs/virgo.ego-gw.it/
software  tests
```