# Prerequisites 
```shell
apt install mysql-client -y

wget https://github.com/vitessio/vitess/releases/download/v17.0.2/vitess-17.0.2-96ac0a6.tar.gz
tar zxvf vitess-17.0.2-96ac0a6.tar.gz && cp vitess-17.0.2-96ac0a6/bin/vtctldclient /usr/local/bin/
```

# Vitess Operator
```shell
git clone https://github.com/webees/k3s-vitess
cd k3s-vitess

# Install the Operator
kubectl create namespace vitess
kubectl apply -n vitess -f operator.yaml
kubectl apply -n vitess -f initial.yaml

# Get Keyspaces
vtctldclient GetKeyspaces --server=$(kubectl get svc -n vitess | grep vitess-vtctld | awk '{print $3}'):15999

# Show databases
mysql -h $(kubectl get svc -n vitess | grep vitess-vtgate | awk '{print $3}') -P 3306 -u vitess -pvitess -e "show databases"

# Delete
kubectl delete -n vitess -f operator.yaml
kubectl delete -n vitess -f initial.yaml
```
