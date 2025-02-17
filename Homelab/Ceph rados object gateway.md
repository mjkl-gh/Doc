#homelab #radosgw

Taken from https://base64.co.za/enable-amazon-s3-interface-for-ceph-inside-proxmox/
### Step 1

Start by creating the keyring on balaur:
```bash
ceph-authtool --create-keyring /etc/ceph/ceph.client.radosgw.keyring
```

### Step 2

Now generate the keys and add them to the keyring created above:

```bash
ceph-authtool /etc/ceph/ceph.client.radosgw.keyring -n client.radosgw.balaur --gen-key
ceph-authtool /etc/ceph/ceph.client.radosgw.keyring -n client.radosgw.cerberus --gen-key
ceph-authtool /etc/ceph/ceph.client.radosgw.keyring -n client.radosgw.chimera --gen-key
```
### Step 3

Next, add the capabilities to each of the keys:

```bash
ceph-authtool -n client.radosgw.balaur --cap osd 'allow rwx' --cap mon 'allow rwx' /etc/ceph/ceph.client.radosgw.keyring
ceph-authtool -n client.radosgw.cerberus --cap osd 'allow rwx' --cap mon 'allow rwx' /etc/ceph/ceph.client.radosgw.keyring
ceph-authtool -n client.radosgw.chimera --cap osd 'allow rwx' --cap mon 'allow rwx' /etc/ceph/ceph.client.radosgw.keyring
```

### Step 4

Now add the keys to the cluster:

```bash
ceph -k /etc/pve/priv/ceph.client.admin.keyring auth add client.radosgw.balaur -i /etc/ceph/ceph.client.radosgw.keyring
ceph -k /etc/pve/priv/ceph.client.admin.keyring auth add client.radosgw.cerberus -i /etc/ceph/ceph.client.radosgw.keyring
ceph -k /etc/pve/priv/ceph.client.admin.keyring auth add client.radosgw.chimera -i /etc/ceph/ceph.client.radosgw.keyring
```

run on balaur

### Step 5

Copy the keyring in to the Proxmox ClusterFS:

```bash
cp /etc/ceph/ceph.client.radosgw.keyring /etc/pve/priv
```

run on balaur

### Step 6

Edit **/etc/ceph/ceph.conf** and paste the text below in this file. Be sure to change _s3.dragundo.net_ to the domain you want to use to access the S3 interface.

```bash
[client.radosgw.balaur]
        host = balaur
        keyring = /etc/pve/priv/ceph.client.radosgw.keyring
        log file = /var/log/ceph/client.radosgw.$host.log
        rgw_dns_name = s3.dragundo.net

[client.radosgw.cerberus]
        host = cerberus
        keyring = /etc/pve/priv/ceph.client.radosgw.keyring
        log file = /var/log/ceph/client.radosgw.$host.log
        rgw_dns_name = s3.dragundo.net

[client.radosgw.chimera]
        host = chimera
        keyring = /etc/pve/priv/ceph.client.radosgw.keyring
        log file = /var/log/ceph/client.rados.$host.log
        rgw_dns_name = s3.dragundo.net
```

run on balaur

### Step 7

Next, login to each of the nodes and install the radosgw package:

```bash
apt install radosgw
```

run on balaur, cerberus, and chimera

### Step 8

Next, start the gateway on each of the nodes:

```bash
systemctl start ceph-radosgw@radosgw.balaur
```

run on balaur

```bash
systemctl start ceph-radosgw@radosgw.cerberus
```

run on cerberus

```bash
systemctl start ceph-radosgw@radosgw.chimera
```

run on chimera

### Step 9

Now enable the following on balaur:

```bash
ceph osd pool application enable .rgw.root rgw
ceph osd pool application enable default.rgw.control rgw
ceph osd pool application enable default.rgw.data.root rgw
ceph osd pool application enable default.rgw.gc rgw
ceph osd pool application enable default.rgw.log rgw
ceph osd pool application enable default.rgw.users.uid rgw
ceph osd pool application enable default.rgw.users.email rgw
ceph osd pool application enable default.rgw.users.keys rgw
ceph osd pool application enable default.rgw.buckets.index rgw
ceph osd pool application enable default.rgw.buckets.data rgw
ceph osd pool application enable default.rgw.lc rgw
```

Note: some of these pools showed up only when I needed them, such as creating a user, so I may need to go back and rerun this command with any newly created pools

### Step 10

Create the admin user:

```bash
radosgw-admin user create --uid=testuser --display-name="Test User" --email=test.user@example.net
```

run on balaur

By default radosgw creates a default pool which might not be desired. In this case you can set the default pool which is useful if you have dedicated SSD and HDD pools. Assuming the pool you want S3 to use is called **hdd_pool** with a placement group called **hddgroup** and index pool called **hddgroupindex** you can run the following commands to set the default pool:

```bash
radosgw-admin zonegroup placement add --rgw-zonegroup default --placement-id hddgroup
radosgw-admin zone placement add --rgw-zone default --placement-id hddgroup --data-pool pool_hdd --index-pool hddgroupindex --data-extra-pool default.rgw.temporary.non-ec
radosgw-admin zonegroup placement default --rgw-zonegroup default --placement-id hddgroup
```

