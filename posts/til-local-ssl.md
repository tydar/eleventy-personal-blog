---
title: "TIL: Self-signed SSL for lab environment"
date: 2022-01-04
tags:
  - til
  - ssl
  - devops

layout: layouts/post.njk
---

I was recently looking into SSL options for local development. I started with the function signature from Go's `net/http` stdlib:

```go
func ListenAndServeTLS(addr, certFile, keyFile string, handler Handler) error
```

Like the godoc says, "files containing a certificate and matching private key for the server must be provided." I have used Let's Encrypt and certbot in the past, but that seemed like a hassle for development servers.

So I started looking and and stumbled on [this Stack Overflow post](https://stackoverflow.com/a/68854352).

This process creates a self-signing CA. This means the certificates can all be created from your computer without any networking, but they are not generally trusted and the root cert must manually be added to the certificate store. 

The first step is to create the signing certificate and private key:

```shell-session
$ openssl req -x509 -days 365 -newkey rsa:4096 -keyout ca_pk.pem -out ca_cert.pem
```

`openssl req -x509` tells OpenSSL to generate a CA certificate (referencing the [X.509 standard](https://en.wikipedia.org/wiki/X.509)). `-days 365` sets its expiration for one year from today. `-newkey rsa:4096` tellls OpenSSL to generate and use a new 4096-bit RSA private key for the certificate. `-keyout` and `-out` set the output file names for the private key and certificate.

To me, the only really opaque flag was `-x509`. Here's more from `man openssl req`:

> The req command primarily creates and processes certificate requests in PKCS#10 format. It can additionally create self signed certificates for use as root CAs for example.
> ...
> *-x509*
>
> > This option outputs a self signed certificate instead of a certificate request. This is typically used to generate a test certificate or a self signed root CA. The extensions added to the certificate (if any) are specified in the configuration file. Unless specified using the set_serial option, a large random number will be used for the serial number.
> >
> > If existing request is specified with the -in option, it is converted to the self signed certificate otherwise new request is created.

Next, we need to create a request for a certificate to be signed and its accompanying private key:

```shell-session
$ openssl req -new -newkey rsa:4096 -keyout app_pk.pem -out app_cert_req.pem
```

This command is nearly identical to the first command we used to generate the CA key and cert, except it is missing the `-x509` flag. Again, this flag just tells the `openssl req` command to create a signed certificate instead of a request, so we don't want it for this step.

Finally, we use `openssl x509` to fulfill the request we just created as `app_cert_req.pem`:\

```shell-session
$ openssl x509 -req -in app_cert_req.pem -days 365 -CA ca_cert.pem -CAkey ca_pk.pem -CAcreateserial -out app_signed_cert.pem
```

Most of the flags on this command are straighforward as well. Two that stood out from `man openssl x509`:

> The x509 command is a multi purpose certificate utility. It can be used to display certificate information, convert certificates to various forms, sign certificate requests like a "mini CA" or edit certificate trust settings.
> ...
> *-req*
>
> > By default a certificate is expected on input. With this option a certificate request is expected instead.
> ...
> *-CAcreateserial*
>
> > With this option the CA serial number file is created if it does not exist: it will contain the serial number "02" and the certificate being signed will have the 1 as its serial number. If the -CA option is specified and the serial number file does not exist a random number is generated; this is the recommended practice.

Also seem simple enough. I haven't looked into the reason `-CAcreateserial` would or would not be used. Since this certificate will be used entirely for local development, I am not terribly concerned, but would look into this more before running through this process for any purpose other than homelabbing.

Finally I followed the steps outlined [in this blog post](https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/#installing-root-cert) to add the new self-signing CA as a trusted root certificate on both my Windows desktop and my Debian development server.

Finally to test that this all was working properly, I put together a minimal Go webapp using the `http.ListenAndServeTLS`, my signed certificate `app_signed_cert.pem`, and its key `app_pk.pem`:

```go
package main

import (
	"io"
	"net/http"
)

func handleReq(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "<html><body><h1>Hello, TLS!</h1></body></html>")
}

func main() {
	http.HandleFunc("/", handleReq)

	err := http.ListenAndServeTLS(":8443", "app_signed.pem", "app_pk.pem", nil)
	if err != nil {
		panic(err)
	}
}
```

However, when I ran this small server, I got an error: `panic: tls: failed to parse private key`. This is because I set a passphrase for the PK. Running this openssl command created an unencrypted key in a new file:

```shell-session
$ openssl rsa -in app_pk.pem -out app_pk.unenc.pem -passin <...>
```

Updating the name of the private key file in my test program got me a result! Go does provide the tools to [use an encrypted key directly](https://stackoverflow.com/a/56131169) as well--a much better method to use in general.
