

```
kubectl create secret generic plain --from-file=basic.txt=./creds-client-kafka-sasl-user.txt --namespace confluent
```
```
kubectl create secret generic plain-connect --from-file=connect-client-plain.txt=./creds-client-kafka-sasl-user.txt --namespace confluent
```
```
kubectl create secret generic plain-controlcenter --from-file=controlcenter-client-plain.txt=./creds-client-kafka-sasl-user.txt --namespace confluent
```
```
kubectl create secret generic plain-jaas --from-file=plain-jaas.conf=kafka.conf -n confluent
```
