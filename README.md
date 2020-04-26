# cero
Cero will connect to remote hosts, and read domain names from the certificates they provided during TLS handshake. <br>
It is not limited to only HTTPS, since cero can grab certificates from any protocol over TLS (SMTPS, FTPS, etc.), just give it the right ports to connect to.

## Installation / Update
```bash
go get -u github.com/glebarez/cero
```

## Usage
Connect to remote host using its domain name and default port (443)
```bash
▶ cero yahoo.com
*.www.yahoo.com
*.yahoo.com
yahoo.com
*.amp.yimg.com
mbp.yimg.com
*.att.yahoo.com
add.my.yahoo.com
ca.my.yahoo.com
ca.rogers.yahoo.com
ddl.fp.yahoo.com
fr-ca.rogers.yahoo.com
hk.rd.yahoo.com
tw.rd.yahoo.com
```
Cero is fast and concurrent, you can pipe your inputs into it. The concurrency level can be set with -c flag:
```bash
cat myTargets.txt | cero -c 1000
```

Cero will accept bare IP as input:
```bash
cero 10.0.0.1
```

Or even entire CIDR range!
```bash
cero 10.0.0.1/22
```

By default, cero uses port 443 for initiating TLS connection, but you can use specific port for every target
```bash
cero use-specific-port.com:10443 use-default-port.com
```

And you can redefine default ports with -p option:
```bash
cat myTargets.txt | cero -p 443,10443
```

Port specification is even supported on CIDR ranges:
```bash
cero 192.1.1.1/16:8443
```

And here is mass-scraping example for popular TLS ports across CIDR range:
```
cero -p 443,8443,10443,30443 -c 1000 192.0.0.1/20
```

## Output control
By default, cero will only output successfully scraped domain names as simple list (to standard output), and the errors (if any)  will be suppressed.<br>
If you want to see detailed output for every host, use the -v flag. This will format output a little differently, and also write error messages to standard error.
```bash
▶ cero -v example.com example.com:80
example.com:80 -- tls: first record does not look like a TLS handshake
example.com:443 -- [www.example.org example.com example.edu example.net example.org www.example.com www.example.edu www.example.net]
```
For precise controls, use shell redirects:
```
▶ cero -v example.com example.com:80 2>/dev/null
example.com:443 -- [www.example.org example.com example.edu example.net example.org www.example.com www.example.edu www.example.net]
```

## Full option list
```bash
Usage of cero:
  -c int
        Concurrency level (default 100)
  -p string
        TLS ports to use, if not specified explicitly in host address. Use comma-separated list (default "443")
  -t int
        TLS Connection timeout in seconds (default 4)
  -v    Be verbose: 
        Output results as 'addr -- [result list]', 
        output errors to stderr as 'addr -- error message'
  ```