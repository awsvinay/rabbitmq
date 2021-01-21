
## Namespace

kubectl create ns rabbits


## Storage Class

kubectl get storageclass
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
standard (default)   rancher.io/local-path   Delete          WaitForFirstConsumer   false                  84s


## Deployment

kubectl apply -n rabbits -f rabbit-rbac.yaml
kubectl apply -n rabbits -f rabbit-secret.yaml
kubectl apply -n rabbits -f rabbit-configmap.yaml
kubectl apply -n rabbits -f rabbit-statefulset.yaml


kubectl -n rabbits get pods

kubectl -n rabbits get pvc

## Access the UI


kubectl -n rabbits port-forward rabbitmq-0 8080:15672


Go to 
http://localhost:8080
Username and pwd -  guest





# Automatic Synchronization

https://www.rabbitmq.com/ha.html#unsynchronised-mirrors

kubectl -n rabbits get pods 
kubectl -n rabbits exec -it rabbitmq-0 bash

rabbitmqctl set_policy ha-fed \
    ".*" '{"federation-upstream-set":"all", "ha-sync-mode":"automatic", "ha-mode":"nodes", "ha-params":["rabbit@rabbitmq-0.rabbitmq.rabbits.svc.cluster.local","rabbit@rabbitmq-1.rabbitmq.rabbits.svc.cluster.local","rabbit@rabbitmq-2.rabbitmq.rabbits.svc.cluster.local"]}' \
    --priority 1 \
    --apply-to queues


kubectl -n rabbits delete pods rabbitmq-0