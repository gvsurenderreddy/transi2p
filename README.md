Provides address mapping and transparent proxying for i2p, Tor, and clearnet.

# Running

Installation and testing:

```
pip install transi2p
twistd -n transi2p
```

Running as a service:

```
sudo ./rules.sh
sudo iptables-save > /etc/iptables/iptables.rules
sudo mkdir /etc/transi2p
sudo cp config.json /etc/transi2p/config.json
sudo useradd -m -s /usr/sbin/nologin -d /var/lib/transi2p transi2p
sudo -u transi2p twistd -d /var/lib/transi2p --pidfile=/var/lib/transi2p/twistd.pid --logfile /dev/null transi2p -c /etc/transi2p/config.json 
```

# Tor setup

Optionally, configure transparent proxying in your Tor daemon:

```
DNSPort 5353
VirtualAddrNetworkIPv4 10.192.0.0/16
AutomapHostsOnResolve 1
TransPort 9040
```

Otherwise, change your resolver to use a clearnet resolver (or none at all for i2p-only!).

# SAM port configuration

Go here and enable the SAM port: http://127.0.0.1:7657/configclients

# Mappings

To configure static mappings, set the default_mappings dictionary in config.json to the mappings that you need:

```
{
    "addr_map": "10.18.0.0",
    "default_mappings": {
        "10.18.0.1": "stats.i2p"
    },
    "dns_port": 5354,
    "listen": "127.0.0.1",
    "resolvers": [
        [
            "8.8.8.8",
            53
        ]
    ],
    "trans_port": 7679
}
```

Static maps are great for whitelisting services, e.g. you could only allow access to 1.1.1.1 preventing access to any other sites, i2p, tor, clearnet, or otherwise.

Combine with whonix and qubes for best results. :)

# Example

Experience luxury!

```
user@cloud1:~$ curl http://stats.i2p/ -svo /dev/null
* Hostname was NOT found in DNS cache
*   Trying 10.18.0.1...
* Connected to stats.i2p (10.18.0.1) port 80 (#0)
> GET / HTTP/1.1
> User-Agent: curl/7.38.0
> Host: stats.i2p
> Accept: */*
> 
< HTTP/1.1 200 OK
< Date: Sun, 24 Jan 2016 22:58:18 GMT
< Vary: Accept-Encoding
< Accept-Ranges: bytes
< Cache-Control: max-age=3600,public
< Content-Type: text/html
< Content-Length: 13181
< Last-Modified: Thu, 22 Oct 2015 14:50:34 GMT
< Connection: close
< 
{ [data not shown]
* Closing connection 0
user@cloud1:~$ curl http://google.com/ -svo /dev/null
* Hostname was NOT found in DNS cache
*   Trying 173.194.65.113...
* Connected to google.com (173.194.65.113) port 80 (#0)
> GET / HTTP/1.1
> User-Agent: curl/7.38.0
> Host: google.com
> Accept: */*
> 
< HTTP/1.1 302 Found
< Cache-Control: private
< Content-Type: text/html; charset=UTF-8
< Location: http://www.google.cz/?gfe_rd=cr&ei=FVelVoHgOYqHOqC6o_gF
< Content-Length: 256
< Date: Sun, 24 Jan 2016 22:58:29 GMT
* Server GFE/2.0 is not blacklisted
< Server: GFE/2.0
< 
{ [data not shown]
* Connection #0 to host google.com left intact
user@cloud1:~$ curl http://3g2upl4pq6kufc4m.onion/ -svo /dev/null
* Hostname was NOT found in DNS cache
*   Trying 10.192.183.80...
* Connected to 3g2upl4pq6kufc4m.onion (10.192.183.80) port 80 (#0)
> GET / HTTP/1.1
> User-Agent: curl/7.38.0
> Host: 3g2upl4pq6kufc4m.onion
> Accept: */*
> 
< HTTP/1.1 200 OK
* Server nginx is not blacklisted
< Server: nginx
< Date: Sun, 24 Jan 2016 22:58:46 GMT
< Content-Type: text/html; charset=UTF-8
< Content-Length: 5196
< Connection: keep-alive
< ETag: "56a54bc6-144c"
< Expires: Sun, 24 Jan 2016 22:58:45 GMT
< Cache-Control: no-cache
< Accept-Ranges: bytes
< 
{ [data not shown]
* Connection #0 to host 3g2upl4pq6kufc4m.onion left intact
user@cloud1:~$ 
```
