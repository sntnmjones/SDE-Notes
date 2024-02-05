# mTLS PrivateCA Proof of Concept

Mutual authentication requires both the client and the server to have certificates signed by a common trusted entity. In this example, PrivateCA will be that entity. PrivateCA will sign both client and server certificate signing requests (CSR), resulting in signed certificate output. These new client and server signed certificates will be used by each identity respectively, in addition to a copy of the PrivateCA certificate to ascertain both entities are referencing the same Root of Trust.

## Create keys, csr, certs

**Note**: In all certificates be certain the ‘Common Name’ matches the server that you will be querying with cURL and with the argument `--cacert CARoot.crt`. Without these, you will see `curl: (60) SSL certificate problem: self signed certificate` error from cURL. In this example, the Common Name is “localhost”.


### Create PrivateCA

[Image: image.png]
### Create server and client keys and certs

bash

```
# Create CA private key
openssl genrsa -out server.key 4096

# Create server csr
openssl req -new -key server.key -out server.csr
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
    Organization Name (eg, company) [Internet Widgits Pty Ltd]:MockSPS
    Organizational Unit Name (eg, section) []:
    Common Name (e.g. server FQDN or YOUR name) []:localhost
    Email Address []:
    
# Sign server csr with AWS Private CA
aws acm-pca issue-certificate \
--certificate-authority-arn arn:aws:acm-pca:us-west-2:504668607727:certificate-authority/235a3a9b-4cc9-4ed7-bdfb-3e4226b31ee1 \
--signing-algorithm SHA256WITHRSA \
--csr $(cat server.csr | base64) \
--template-arn arn:aws:acm-pca:::template/EndEntityServerAuthCertificate/V1 \
--validity Value=3,Type="DAYS" \
--profile jnesztr --region us-west-2 --no-cli-pager 
    {
        "CertificateArn": "arn:aws:acm-pca:us-west-2:504668607727:certificate-authority/235a3a9b-4cc9-4ed7-bdfb-3e4226b31ee1/certificate/84a6572b67ba6bb2888fe55e750eed17"
    }
    
# Get signed certificate and copy and paste into file
# server.crt
aws acm-pca get-certificate \
--certificate-authority-arn arn:aws:acm-pca:us-west-2:504668607727:certificate-authority/235a3a9b-4cc9-4ed7-bdfb-3e4226b31ee1 \
--certificate-arn arn:aws:acm-pca:us-west-2:504668607727:certificate-authority/235a3a9b-4cc9-4ed7-bdfb-3e4226b31ee1/certificate/84a6572b67ba6bb2888fe55e750eed17 \
--profile jnesztr --region us-west-2 --no-cli-pager | jq -r '.Certificate, .CertificateChain'
    -----BEGIN CERTIFICATE-----
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
    -----END CERTIFICATE-----
    
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
    
# Sign client csr with AWS Private CA
aws acm-pca issue-certificate \
--certificate-authority-arn arn:aws:acm-pca:us-west-2:504668607727:certificate-authority/235a3a9b-4cc9-4ed7-bdfb-3e4226b31ee1 \
--signing-algorithm SHA256WITHRSA \
--csr $(cat client.csr | base64) \
--template-arn arn:aws:acm-pca:::template/EndEntityClientAuthCertificate/V1 \
--validity Value=3,Type="DAYS" \
--profile jnesztr --region us-west-2 --no-cli-pager
    {
        "CertificateArn": "arn:aws:acm-pca:us-west-2:504668607727:certificate-authority/235a3a9b-4cc9-4ed7-bdfb-3e4226b31ee1/certificate/12f31478cdbaad93fae38703dad1f1e0"
    }
    
# Get signed certificate and copy and paste into file
# client.crt
aws acm-pca get-certificate \
--certificate-authority-arn arn:aws:acm-pca:us-west-2:504668607727:certificate-authority/235a3a9b-4cc9-4ed7-bdfb-3e4226b31ee1 \
--certificate-arn arn:aws:acm-pca:us-west-2:504668607727:certificate-authority/235a3a9b-4cc9-4ed7-bdfb-3e4226b31ee1/certificate/12f31478cdbaad93fae38703dad1f1e0 \
--profile jnesztr --region us-west-2 --no-cli-pager | jq -r '.Certificate, .CertificateChain'
    -----BEGIN CERTIFICATE-----
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
    -----END CERTIFICATE-----
```

### Download RootCA.crt from PrivateCA

[Image: image.png]
## Create a local server

### setup

bash


```
npx tsc --init
```

### tsconfig.json

```
{
  "compilerOptions": {
    "target": "es2016",                                  /* Set the JavaScript language version for emitted JavaScript and include compatible library declarations. */
    "module": "commonjs",                                /* Specify what module code is generated. */
    "types": ["node"],                                      /* Specify type package names to be included without being referenced in a source file. */
    "esModuleInterop": true,                             /* Emit additional JavaScript to ease support for importing CommonJS modules. This enables 'allowSyntheticDefaultImports' for type compatibility. */
    "forceConsistentCasingInFileNames": true,            /* Ensure that casing is correct in imports. */
    "strict": true,                                      /* Enable all strict type-checking options. */
    "skipLibCheck": true                                 /* Skip type checking all .d.ts files. */
  }
}
```

### package.json

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



### server.js

```
import * as https from "https";
import * as fs from "fs";

const options = {
  key: fs.readFileSync('./server.key'),
  cert: fs.readFileSync('./server.crt'),
  ca: [
    fs.readFileSync('./CARoot.crt')
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



### Start server

bash

```
npm tsc && node server.js
```



## cURL server

bash

```
curl -v --key client.key --cert client.crt --cacert CARoot.crt https://localhost:8443
*   Trying [::1]:8443...
* Connected to localhost (::1) port 8443
* ALPN: curl offers h2,http/1.1
* (304) (OUT), TLS handshake, Client hello (1):
*  CAfile: CARoot.crt
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
*  subject: C=US; ST=NV; L=Reno; O=MockSPS; CN=localhost
*  start date: Feb  1 13:11:07 2024 GMT
*  expire date: Feb  4 14:11:07 2024 GMT
*  common name: localhost (matched)
*  issuer: C=US; O=MockSPS; ST=NV; CN=localhost; L=Reno
*  SSL certificate verify ok.
* using HTTP/1.1
> GET / HTTP/1.1
> Host: localhost:8443
> User-Agent: curl/8.4.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Date: Thu, 01 Feb 2024 14:30:29 GMT
< Connection: keep-alive
< Keep-Alive: timeout=5
< Transfer-Encoding: chunked
<
* Connection #0 to host localhost left intact
Hello World!%
```



## References

* https://www.digitalocean.com/community/tutorials/typescript-new-project
* https://builtin.com/software-engineering-perspectives/mutual-tls-tutorial
* https://aws.amazon.com/blogs/compute/introducing-mutual-tls-authentication-for-amazon-api-gateway/
* https://www.cs.toronto.edu/~arnold/427/19s/427_19S/tool/ssl/notes.pdf

* * *
*--wiki-sync: "*[*Users/jnesztr/poc/mtls*](https://w.amazon.com/bin/view/Users/jnesztr/poc/mtls)*"--*
