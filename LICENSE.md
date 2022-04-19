# Root CA

## Root Key

**Attention:** this is the key used to sign the certificate requests, anyone holding this can sign certificates on your behalf. So keep it in a safe place!


openssl genrsa -out rootCA.key 4096


## Self sign the Root Certificate


openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.crt


Here we used our root key to create the root certificate that needs to be distributed in all the computers that have to trust us.


# Certificate (Done for each server)

This procedure needs to be followed for each server/appliance that needs a trusted certificate from our CA

## The certificate key


openssl genrsa -out oriemaharaj.org.key 2048


## The signing  (csr)

The certificate signing request is where you specify the details for the certificate you want to generate.
This request will be processed by the owner of the Root key (you in this case since you create it earlier) to generate the certificate.

**Important:** Please mind that while creating the signign request is important to specify the `Common Name` providing the IP address or domain name for the service, otherwise the certificate cannot be verified.

I will describe here two ways to generate 

### Method A (Interactive)

If you generate the csr in this way, openssl will ask you questions about the certificate to generate like the organization details and the `Common Name` (CN) that is the web address you are creating the certificate for, e.g `mydomain.com`.


openssl req -new -key oriemaharaj.org.key -out oriemaharaj.org.csr


### Method B (One Liner)

This method generates the same output as Method A but it's suitable for use in your automation :) .


openssl req -new -sha256 -key oriemaharaj.org.key -subj "/C=US/ST=CA/O=MyOrg, Inc./CN= oriemaharaj.org" -out oriemaharaj.org.csr


If you need to pass additional config you can use the `-config` parameter, here for example I want to add alternative names to my certificate.


openssl req -new -sha256 \
    -key oriemaharaj.org.com.key \
    -subj "/C=US/ST=CA/O=MyOrg, Inc./CN= oriemaharaj.org" \
    -reqexts SAN \
    -config <(cat /etc/ssl/openssl.cnf \
        <(printf "\n[SAN]\nsubjectAltName=DNS:oriemaharaj.org,DNS:oriemaharaj,DNS:oriemaharajfdn,DNS=OMFF,DNS=omff,DNS=oriemaharajfamilyfoundation,DNS=oriemaharajfdn,DNS=omfamilyfoundation,DNS=omfamilyfdn")) \
    -out oriemaharaj.org.csr



## Verified the csr content


openssl req -in oriemaharaj.org.csr -noout -text


## Generating the certificate using the `oriemaharaj` csr and key along with the CA Root key


openssl x509 -req -in oriemaharaj.org.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out oriemaharaj.org.crt -days 500 -sha256


## Verified certificate's content


openssl x509 -in oriemaharaj.org.crt -text -noout
