### Init

```
export TUTORIAL_HOME=.
```

### Deploy Confluent for Kubernetes
```
helm repo add confluentinc https://packages.confluent.io/helm

helm upgrade --install operator confluentinc/confluent-for-kubernetes

kubectl get pods
```

### Provide Root Certificate
```
openssl genrsa -out $TUTORIAL_HOME/ca-key.pem 2048
```
```
openssl req -new -key $TUTORIAL_HOME/ca-key.pem -x509 \
-days 1000 \
-out $TUTORIAL_HOME/ca.pem \
-subj "/C=US/ST=CA/L=MountainView/O=Confluent/OU=Operator/CN=TestCA"
```
### Provide credentials
```
kubectl create secret generic cloud-plain \
--from-file=plain.txt=$TUTORIAL_HOME/creds-client-kafka-sasl-user.txt

kubectl create secret generic cloud-sr-access \
--from-file=basic.txt=$TUTORIAL_HOME/creds-schemaRegistry-user.txt

kubectl create secret generic control-center-user \
--from-file=basic.txt=$TUTORIAL_HOME/creds-control-center-users.txt
```

### Provide custom TLS secret

#### 1- Download the Let's Encrypt root CA in PEM format
```
curl -o letsencrypt-root.pem https://letsencrypt.org/certs/isrgrootx1.pem
```
#### 2- Concatenate certificates
```
cat isrgrootx1.pem ca.pem > custom-ca.pem
```
#### 3-Create a Certificate Signing Request
```
openssl req -new -key ca-key.pem -out server.csr \                    
  -subj "/CN=connect.confluent.svc.cluster.local"
```
#### 4-Sign the certificate withe CA
```
openssl x509 -req -in server.csr -CA custom-ca.pem -CAkey ca-key.pem \
-CAcreateserial -out server.pem -days 365
```
#### 5-Verify
```
openssl verify -CAfile custom-ca.pem server.pem
```
#### 6- Create custom secret
```
kubectl create secret generic custom-secret \
--from-file=fullchain.pem=server.pem \
--from-file=cacerts.pem=custom-ca.pem \
--from-file=privkey.pem=ca-key.pem \
--namespace confluent
```
### Deploy Confluent Platform
```
kubectl apply -f $TUTORIAL_HOME/confluent-platform.yaml
kubectl get pods
```

### Check endpoint
```
# Control center
kubectl port-forward controlcenter-0 9021:9021 -n confluent
```
```
#Log into confluent center with usename admin and password Developer1
https://localhost:9021
```
# Connect
```
kubectl port-forward connect-0 8083 -n confluent
```
```
https://localhost:9021
```

### Create connector
```
kubectl apply -f $TUTORIAL_HOME/connector.yaml

# Check connector 
kubectl get connector -n confluent
```

### Destroy resources
```
kubectl delete -f $TUTORIAL_HOME/connector.yaml
kubectl delete -f $TUTORIAL_HOME/confluent-platform.yaml
```