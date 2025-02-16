### Generate Payment Stubs
    
    protoc -I ./payment --go_out ./golang/payment --go_opt paths=source_relative --go-grpc_out ./golang/payment --go-grpc_opt paths=source_relative ./payment/payment.proto

### Generate Order Stubs

    protoc -I ./order --go_out ./golang/order --go_opt paths=source_relative --go-grpc_out ./golang/order --go-grpc_opt paths=source_relative ./order/order.proto

### Generate Shipping Stubs

    protoc -I ./shipping --go_out ./golang/shipping --go_opt paths=source_relative --go-grpc_out ./golang/shipping --go-grpc_opt paths=source_relative ./shipping/shipping.proto


## Tag the commit


### Create the tag

    git tag v1.0

### Push tags

    git push origin v1.0

    OR 

    git push --tags

### List tags

    git tag


## Genrate OpenSSL certificate

### Public Key
    openssl req -x509 \
        -sha256 \
        -newkey rsa:4096 \
        -days 365 \
        -keyout ca-key.pem \
        -out ca-cert.pem \
        -subj "/C=PL/ST=EUROPE/L=POLAND/O=Software/OU=Microservices/CN=*.microservices.dev/emailAddress=myemail@gmail.com" \
        -nodes

- `/C` is used for country.
- `/ST` is the state information.
- `/L` states city information.
- `/O` means organization.
- `/OU` is for the organization unit to explain which department.
- `/CN` is used for the domain name, the short version of common name.
- `/emailAddress` is used for an email address to contact the certificate owner.

### Veirfy the generated self-certificate for the CA

    openssl x509 -in ca-cert.pem -noout -text

### Private Key

    openssl req \
        -newkey rsa:4096 \
        -keyout server-key.pem \
        -out server-req.pem \
        -subj "/C=PL/ST=EUROPE/L=POLAND/O=Micorservices/OU=PaymentService/CN=*.microservices.dev/emailAddress=myemail@gmail.com" \
        -nodes \
        -sha256

### Use CA's private key to sign the request

    openssl x509 \
        -req -in server-req.pem \
        -days 60 \
        -CA ca-cert.pem \
        -CAkey ca-key.pem \
        -CAcreateserial \
        -out server-cert.pem \
        -extfile server-ext.cnf \
        -sha256

- `server-ext.cnf:`
    
    subjectAltName=DNS:*.microservices.dev,DNS:*.microservices.dev,IP:0.0.0.0

### Verify the server's self-signed certificate
    
    openssl x509 -in server-cert.pem -noout -text

### Generate certificate for the client side for mTLS

    openssl req \
        -newkey rsa:4096 \
        -keyout client-key.pem \
        -out client-req.pem \
        -subj "/C=PL/ST=EUROPE/L=POLAND/O=Microservices/OU=OrderService/CN=*.microservices.dev/emailAddress=myemail@gmail.com" \
        -nodes \
        -sha256


### Sign the client certificate with CA's private key

    openssl x509 \
        -req -in client-req.pem \
        -sha256 \
        -days 60 \
        -CA ca.crt \
        -CAkey ca.key \
        -CAcreateserial \
        -out client.crt \
        -extfile client-ext.cnf

- `client-ext.cnf:`
    
    subjectAltName=DNS:*.microservices.dev,DNS:*.microservices.dev,IP:0.0.0.0
