# Deployed Kong as an Ingress Controller in GCP
```
kubectl apply -f <dbless kong yaml file>
```


# Create a service and deployment for ingress

```
kubectl create deployment hello-world-service-blue --image=gcr.io/google-samples/hello-app:1.0

kubectl create deployment hello-world-service-red  --image=gcr.io/google-samples/hello-app:1.0

kubectl expose deployment hello-world-service-blue --port=4343 --target-port=8080 --type=ClusterIP

kubectl expose deployment hello-world-service-red  --port=4242 --target-port=8080 --type=ClusterIP
```

# Test the path based ingress
```
kubectl apply -f path-ingress.yml

curl http://<Kong proxy Loadbalancer IP>/red  --header 'Host: path.example.com'

curl http://<<Kong proxy Loadbalancer IP>/blue  --header 'Host: path.example.com'
```

# Create the tls certificate and key and create secret for that tls
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout tls.key -out tls.crt -subj "/C=IN/ST=KARNATAKA/L=BENGALURU/O=IT/OU=IT/CN=tls.example.com"

kubectl create secret tls tls-secret --key tls.key --cert tls.crt
```

# Reference the secret name in the tls based ingress
```
kubectl apply -f tls-ingress.yml
``` 

# Test the tls based ingress
```
curl https://tls.example.com:443 --resolve tls.example.com:443:<Kong proxy Loadbalancer IP> --insecure --verbose
```