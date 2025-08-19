

```
kubectl create secret generic plain --from-file=basic.txt=./creds-client-kafka-sasl-user.txt --namespace confluent
```

```
kubectl create secret generic plain-jaas --from-file=plain-jaas.conf=plain-jaas.conf -n confluent
```