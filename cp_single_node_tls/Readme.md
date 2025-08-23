### Generete CA
```
openssl genrsa -out ca.key 4096

openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 \
  -subj "/CN=confluent-ca" \
  -out ca.crt
```

### Create server Key & CSR with SANs that match Kafka services

````
openssl genrsa -out kafka.key 2048

openssl req -new -key kafka.key -out kafka.csr -config csr.conf
````

### Sign the server cert with your CA
```
openssl x509 -req -in kafka.csr -CA ca.crt -CAkey ca.key -CAcreateserial \
  -out kafka.crt -days 825 -sha256 -extfile ca.ext
```
### 

```
JKS_PASS=changeit

keytool -importcert -noprompt \
  -alias cluster-ca -file ca.crt \
  -keystore kafka.truststore.jks -storepass "${JKS_PASS}"
  
openssl pkcs12 -export \
  -in kafka.crt -inkey kafka.key \
  -name broker-tls \
  -out kafka.p12 -password pass:"${JKS_PASS}"

keytool -importkeystore \
  -deststorepass "${JKS_PASS}" \
  -destkeypass  "${JKS_PASS}" \
  -destkeystore kafka.keystore.jks \
  -srckeystore  kafka.p12 \
  -srcstoretype PKCS12 \
  -srcstorepass "${JKS_PASS}" \
  -alias broker-tls
```