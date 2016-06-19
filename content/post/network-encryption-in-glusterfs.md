+++
author = "Kaushal"
categories = ["gluster", "glusterfs", "ssl", "encryption", "howto", "setup", "tls"]
date = 2015-05-07T14:44:30Z
draft = false
slug = "network-encryption-in-glusterfs"
tags = ["gluster", "glusterfs", "ssl", "encryption", "howto", "setup", "tls"]
title = "Setting up network encryption in GlusterFS"

+++

## Introduction
[GlusterFS][1] has supported network encryption using TLS/SSL since GlusterFS-3.5. GlusterFS-3.5 supported encryption only on the I/O connections, ie. the connections between GlusterFS clients and servers. GlusterFS-3.6 introduced support for encrypting the manangement connections, the connections between GlusterDs within a trusted storage pool.

When using network encryption, GlusterFS also uses TLS/SSL for authentication and authorization, in place of the home grown auth framework used for normal connections.

Though encryption has been supported for quite some time, the number of users using it has been minimal. Originally, this was due to the lack of proper documentation. This was addressed recently by Jeff Darcy [2]. The linked document gives a pretty good introduction to TLS/SSL concepts and how it is used in GlusterFS.

The document is not particularly clear on how to set up network encryption for a cluster correctly, which has lead to several users encountering problems when setting up encryption. In this post I'd like provide clear instructions to get network encryption up and running on a GlusterFS storage pool.

> NOTE: This post assumes GlusterFS-3.6 or above is being used. Though most of the steps might be applicable to GlusterFS-3.5 as well, I've not tested them. It is recommended to use at least version 3.6.3, which has some important fixes for network encryption.

I'd recommend everyone to read the admin document [2] once before continuing.

## Files and options involved
Setting up network encryption involves the following files and  volume options.

- The files
	- `/etc/ssl/glusterfs.pem`
    - `/etc/ssl/glusterfs.key`
    - `/etc/ssl/glusterfs.ca`
    - `/var/lib/glusterd/secure-access`
- The options
	- `server.ssl`
    - `client.ssl`
    - `auth.ssl-allow`

### Files
==Every server and every client needs all the files to be present==, unless mentioned otherwise.

- `/etc/ssl/glusterfs.pem`
	This is the certificate file containing the systems unique, signed TLS certificate. This file is unique for each system and should not be shared with others.

- `/etc/ssl/glusterfs.key`
	This file contains the systems unique private key. This file should not be shared with others.
 
- `/etc/ssl/glusterfs.ca`
	This file contains the certificates of the Certificate Authorities (CA) who have signed the certificates. This file is not unique and should be the ==same on all servers== in the trusted storage pool. All the clients also should have the same file, but not necessarily the same one as the servers. GlusterFS does not use the global CA certs that come with the system.
    The CA file on the servers should contain the certificates of the signing CA for all the servers and all the clients. The CA file on the clients should contain the certificates of the signing CA for all the servers.  
    In case self-signed certificates are being used, the CA file for the servers is a concatenation of the certificate files `/etc/ssl/glusterfs.pem` of every server and every client. The client CA file is a concatenation of the certificate files of every server.

- `/var/lib/glusterd/secure-access`
	The presence of this file ==enables network encryption for the management connections==, ie. connections between GlusterD's and the connection between clients and GlusterDs used to fetch volfiles and notify the clients of changes to volfiles. This is just an empty file.  
    If management encryption is not required, it is not necessary to have this file. If management encryption is required, this file should be present on all the servers and all the clients. This is required on the clients as well, to indicate the mount command to use encrypted connection to retrieve the volfile.

### Volume options
- `server.ssl`,`client.ssl`
	These options are used to enable encryption on the I/O connections for a volume. ==Both of these options should be set and unset as a pair==, ie. neither option can be set without setting the other.
    
- `auth.ssl-allow`
	This option is used to authorize clients which are allowed to acces the volume, to be used in place of `auth.allow` when using I/O encryption. It should be set to a comma seprated list of *common names* of the clients which are to be allowed to access the volume. Keep in mind that the common-name is the name used when creating the signed certificates, not the address/hostname of the client (though in most cases, the hostname is used as the common name).  
    > NOTE: There is a slight behavioural difference of this option between GlusterFS-3.6 and GlusterFS-3.7. In GlusterFS-3.6 not setting `auth.ssl-allow` prevents all clients from accessing to the volume. So by default no client is authorized to access the volume. To allow access to all TLS authenticated clients, it was necessary to set the option to `'*'`. In GlusterFS-3.7, not setting `auth.ssl-allow` is the same as setting it to `'*'`. So by default all TLS authenticated clients are allowed to access the volume.  
    
    The ==common names of the servers in the storage pool *must* be present in this list, along with the clients==, to allow GlusterFS server side services like the NFS server, self-heal daemon, quota daemon etc., to access the volume (this is not needed with GlusterFS-3.7 if `auth.ssl-allow` is left empty).
    
## Setting it all up
The instructions to set up network encryption are pretty straight forward, with some variations depending on the state of the volume and cluster
### Common steps
These steps are commong to clients and servers, and need to be performed before any of the steps given further below.

1. Generate private key and a signed certificate for each system. Instructions for generation and signing certificates can be found easily by googling. It is recommended to use a unique common name for each certificate. Copy the generated private key to `/etc/ssl/glusterfs.key` and the signed certificate to `/etc/ssl/glusterfs.pem` .

2. a. Collect the certificates of the CAs of both servers and clients and concatenate them into a single file. Place this file at `/etc/ssl/glusterfs.ca` on all the servers in the trusted storage pool. If you are using self-signed certificates, the `/etc/ssl/glusterfs.pem` files from every system (server/client) need to be collected, concatenated and distributed. 
b. For the clients, collect and concatenate the signing CA certificates for the servers and place it at `/etc/ssl/glusterfs.ca`.

### Enabling I/O encryption for volumes
#### New volumes
1. Create the volume, but don't start it.
2. Set the list of common names for clients allowed to access the volume. Remember to include the common names of all the servers.  
	`# gluster volume set <VOLNAME> auth.ssl-allow 'server1,server2,server3,client1,client2,client3'`
3. Enable `{client,server}.ssl` on the volume.
	`# gluster volume set <VOLNAME> client.ssl on server.ssl on`
4. Start the volume and mount from clients.

#### Existing volumes
1. Unmount all clients and stop the volume.
2. Follow steps 2-4 from above.

The break in service is required, because GlusterFS cannot change between unencrypted and encrypted connections dynamically.

### Enabling management encryption for volumes
Though GlusterFS can run just I/O encryption without using management encryption, it is nice have it as well.

#### Fresh installation
I'm defining a fresh installation, as a cluster in which GlusterFS has only been installed on the servers and the clients, but has never been run.

**On servers**  

1. Create the `/var/lib/glusterd/secure-access` file.
`# touch /var/lib/glusterd/secure-access`
2. Start GlusterD and setup the trusted storage pool by running appropriate `peer probe` commands.

**On clients**

1. Create the `/var/lib/glusterd/secure-access` file.
`# touch /var/lib/glusterd/secure-access`
2. Perform the mount

#### Existing installation
On an existing installation, with running servers and clients, enabling management encryption will also involve a break in service.

1. Unmount all clients and stop all volumes
2. Stop GlusterDs on all servers.
3. Create the `/var/lib/glusterd/secure-access` file on all servers and clients.
`# touch /var/lib/glusterd/secure-access`
4. Start all GlusterDs.
5. Start the volumes and mount from the clients.

As mentioned above, GlusterFS cannot currently change between unencrypted and encrypted connections dynamically. If bricks and other local services on the servers, and clients would not be able to recieve notifications from GlusterD if they continue to run when the switch to management encryption is made.

### Expanding the trusted storage pool
#### Certificate signed with a known/common CA
Addition of a new server into the storage pool is really easy if not using self-signed certificates for each system. If a common CA is used to all the certificates of the servers, the steps to add a new peer into the are,

1. Generate a private key and signed certificate for the new server, and place them into `/etc/ssl/glusterfs.key` and `/etc/ssl/glusterfs.pem`.
2. Copy over `/etc/ssl/glusterfs.ca` from one of the existing servers.
3. If using management encryption, create `/var/lib/glusterd/secure-access`.
4. Add the common name of the new server to the `auth.ssl-allow` list for all volumes which have encryption enabled.
5. Probe the new server into the cluster.

#### Self-signed certificates and certificates signed by new CA
Using self-signed certificates would require a break in service to add a new server into the cluster, as the CA list cannot be dynamically reloaded. To add a new server in this case the steps are,

1. Generate the private key and self-signed certificate on the new server.
2. a. Obtain a `/etc/ssl/glusterfs.ca` file from one of the other servers, append the new servers certificate to it, and distribute the new CA file on all servers, including the new one.
b. Obtain a `/etc/ssl/glusterfs.ca` file from one of the clients, append the new servers certificate to it, and distribute the new CA file to all the clients.
3. Touch the `/var/lib/glusterd/secure-access` file if required.
4. Add the common name of the new server to the `auth.ssl-allow` list for all volumes which have encryption enabled
5. Restart all the glusterfs processes on existing servers and clients. The best way to do it,
a. Unmount all clients.
b. Stop all volumes.
c. Restart all GlusterDs.
d. Start volumes.
e. mount volumes on clients.
6. Now, probe the new server into the cluster.


### Authorizing a new client
#### Certificate signed with a known/common CA
Authorizing access to a volume for a new client is simple if the client has a certificate signed by a CA already present in the GlusterFS CA file.

1. Generate the private key and signed certificate for the new server, and place them in the appropriate locations.
2. Copy over `/etc/ssl/glusterfs.ca` from another client.
3. Touch `/var/lib/glusterd/secure-access` if required.
4. Add the common-name of the new client to the `auth.ssl-allow` list.
> NOTE: The `gluster volume set` command doesn't append to existing values of options. To append the new name to the list, get the existing list using `gluster volume get` or `gluster volume info`, append the new name to the list and set the option again with `gluster volume set`.

5. Mount the volume from the client.

#### Self-signed certificates and certificates signed by new CA
A break in the service is required in this case. The following steps need to be followed.

1. Generate the private key and certificate for the client, and place them at the appropriate locations on the client.
2. Get the CA file from one of the existing clients, and add it to the new client.
3. Touch `/var/lib/glusterd/secure-access` if required
4. Add the new CA certificate (the generated cert for the client in case of self-signed certificate) with the CA file of the servers.
5. Add the common name of the new client to the `auth.ssl-allow` list
6. Restart the volume
7. Mount the volume from the new client

## Closing remarks
Successfully using network encryption in GlusterFS requires some effort from the user. But once set up, it works fine. If you find any bugs or hit any trouble, drop a mail on the [gluster-users][4] mailing list or drop in to `#gluster` on freenode.

[1]: https://gluster.org/
[2]: https://gluster.readthedocs.org/en/latest/Administrator%20Guide/SSL/
[3]: http://www.akadia.com/services/ssh_test_certificate.html
[4]: https://www.gluster.org/mailman/listinfo/gluster-users