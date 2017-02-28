# Ubiquiti-USG-DHCP-Option-66
How to add DHCP option 66 to Ubiquiti USG


SSH into USG using device username and password (found in Unifi Controller > Settings > Site)

```configure```
Use show command to find out your shared-network-name
```show
set service dhcp-server shared-network-name VoIP_10.1.1.0-24 subnet 10.1.1.0/24 tftp-server-name http://cloudpbx.locklinnetworks.com
commit;save;exit

mca-ctrl -t dump-cfg 
```
```
{
	"service": {
		"dhcp-server": {
            "VoIP_10.1.1.0-24": {
                    "authoritative": "enable",
                    "description": "vlan20",
                    "subnet": {
                            "10.1.1.0/24": {
                                    "default-router": "10.1.1.1",
                                    "dns-server": [
                                            "10.1.1.1"
                                    ],
                                    "lease": "86400",
                                    "start": {
                                            "10.1.1.6": {
                                                    "stop": "10.1.1.254"
                                            }
                                    },
                                    "tftp-server-name": "http://cloudpbx.locklinnetworks.com/xml/locklin"
                            		}
                    			}
            			}
    			}
		}
}
```

SSH into Unifi Controller

``` cd /usr/lib/unifi/data/sites/<site-name> ```

You can find the site name by looking at the URL of the Unifi Controller webpage for the site

```nano config.gateway.json```

<Paste in the config extracted from the USG>
Save

