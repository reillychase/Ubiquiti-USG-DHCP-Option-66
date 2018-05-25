# UPDATE 5/14/2018 - It looks like Ubiquiti has added the ability to configure DHCP options from UniFi on the latest version! Under Settings > Services > DHCP, you can enable option 150 which should work for most VoIP phones

# Ubiquiti-USG-DHCP-Option-66
How to add DHCP option 66 to Ubiquiti USG

# https://hostifi.net
**SUMMARY** In this example I am configuring Option 66 for 2 different VLANs aka LAN and VoIP. Option 66 will point to the DNS name of the PBX where the phones will download their configuration files. The reason I am applying option 66 to the LAN as well as VoIP VLAN is because a factory reset phone will not know the VoIP VLAN, it will get DHCP from the LAN. Then it will download its configuration file, which will give it its correct VLAN.


1 SSH into USG using device username and password (found in Unifi Controller > Settings > Site)

```
configure
```
Use show command to find out your shared-network-name
```
show
set service dhcp-server shared-network-name VoIP_10.1.1.0-24 subnet 10.1.1.0/24 tftp-server-name http://cloudpbx.locklinnetworks.com/xml/locklin
set service dhcp-server shared-network-name LAN_192.168.1.0-24 subnet 192.168.1.0/24 tftp-server-name http://cloudpbx.locklinnetworks.com/xml/locklin
commit;save;exit

mca-ctrl -t dump-cfg 
```
2 Copy this section of that output:

```
{
"service": {
        "dhcp-server": {
                "shared-network-name": {
                        "LAN_192.168.1.0-24": {
                                "subnet": {
                                        "192.168.1.0/24": {
                                                "tftp-server-name": "http://cloudpbx.yoursite.com/app/provision/?mac=$MA"
                                        }
                                }
                        },
                        "VoIP_10.1.1.0-24": {
                                "subnet": {
                                        "10.1.1.0/24": {
                                                "tftp-server-name": "http://cloudpbx.yoursite.com/app/provision/?mac=$MA"
                                        }
                                }
                        }
                }
        }
}
}
```
NOTE: For FusionPBX use "http://cloudpbx.yoursite.com/app/provision/?mac=$MA" for Cisco SPA phones, or use "http://cloudpbx.yoursite.com/app/provision/" for Yealink phones

3 SSH into Unifi Controller

```
cd /usr/lib/unifi/data/sites/<site-name>
```

You can find the site name by looking at the URL of the Unifi Controller webpage for the site

```
nano config.gateway.json
```

\<Paste in the config extracted from the USG\>

Save

