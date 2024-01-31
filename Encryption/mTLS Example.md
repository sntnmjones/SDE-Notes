## mTLS Proof of Concept

### Create keys, csr, certs

bash

```
# Create server private key
openssl genrsa -out server.key 4096

# Create self signed cert for server
openssl req -new -x509 -days 3650 -key server.key -out server.crt
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:US
    State or Province Name (full name) [Some-State]:NV
    Locality Name (eg, city) []:Reno
    Organization Name (eg, company) [Internet Widgits Pty Ltd]:RootCA
    Organizational Unit Name (eg, section) []:
    Common Name (e.g. server FQDN or YOUR name) []:localhost
    Email Address []:

# Create client private key
openssl genrsa -out client.key 2048

# Create client csr
openssl req -new -key client.key -out client.csr
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:US
    State or Province Name (full name) [Some-State]:CA
    Locality Name (eg, city) []:Sac
    Organization Name (eg, company) [Internet Widgits Pty Ltd]:Client
    Organizational Unit Name (eg, section) []:
    Common Name (e.g. server FQDN or YOUR name) []:localhost
    Email Address []:
    
    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:

# Create client cert by signing with the server's private key and marking the number of certs signed by this CA as 01
openssl x509 -req -in client.csr -CA server.crt -CAkey server.key -set_serial 01 -out client.crt -days 3650 -sha256
    Certificate request self-signature ok
    subject=C=US, ST=CA, L=Sac, O=Client, CN=localhost
```

### Create a local server

#### setup

bash

```
npx tsc --init
```

#### package.json

```
{
    "name": "localserver",
    "version": "1.0.0",
    "description": "",
    "scripts": {},
    "keywords": [],
    "author": "",
    "license": "MIT",
    "devDependencies": {
        "@types/node": "^20.11.11",
        "typescript": "^5.0.0"
    }
}
```

#### server.js

```
import * as https from "https";
import * as fs from "fs";

const options = {
  key: fs.readFileSync('./server.key'),
  cert: fs.readFileSync('./server.crt'),
  ca: [
    fs.readFileSync('./server.crt')
  ],
  requestCert: true,
};

const server = https.createServer(options, (req, res) => {
  res.writeHead(200, {});     // response header
  res.write('Hello World!');  // response body
  res.end();                  // response footer
});

const port = 8443;
server.listen(port, 'localhost', () => {
  console.log(`Server is running on port ${port}`);
});
```

#### Start server

bash

```
npm tsc && node server.js
```

### cURL server

bash

```
curl -v --key client.key --cert client.crt --cacert server.crt https://localhost:8443
*   Trying [::1]:8443...
* Connected to localhost (::1) port 8443
* ALPN: curl offers h2,http/1.1
* (304) (OUT), TLS handshake, Client hello (1):
*  CAfile: server.crt
*  CApath: none
* (304) (IN), TLS handshake, Server hello (2):
* (304) (IN), TLS handshake, Unknown (8):
* (304) (IN), TLS handshake, Request CERT (13):
* (304) (IN), TLS handshake, Certificate (11):
* (304) (IN), TLS handshake, CERT verify (15):
* (304) (IN), TLS handshake, Finished (20):
* (304) (OUT), TLS handshake, Certificate (11):
* (304) (OUT), TLS handshake, CERT verify (15):
* (304) (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / AEAD-AES256-GCM-SHA384
* ALPN: server accepted http/1.1
* Server certificate:
*  subject: C=US; ST=NV; L=Reno; O=RootCA; CN=localhost
*  start date: Jan 31 14:23:11 2024 GMT
*  expire date: Jan 28 14:23:11 2034 GMT
*  common name: localhost (matched)
*  issuer: C=US; ST=NV; L=Reno; O=RootCA; CN=localhost
*  SSL certificate verify ok.
* using HTTP/1.1
> GET / HTTP/1.1
> Host: localhost:8443
> User-Agent: curl/8.4.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Date: Wed, 31 Jan 2024 14:24:17 GMT
< Connection: keep-alive
< Keep-Alive: timeout=5
< Transfer-Encoding: chunked
<
* Connection #0 to host localhost left intact
Hello World!%
```

