# Prerequisites 
```shell
apt install mysql-client -y

wget https://github.com/vitessio/vitess/releases/download/v17.0.2/vitess-17.0.2-96ac0a6.tar.gz
tar zxvf vitess-17.0.2-96ac0a6.tar.gz && cp vitess-17.0.2-96ac0a6/bin/vtctldclient /usr/local/bin/
```

# Vitess Operator for Kubernetes
```shell
git clone https://github.com/vitessio/vitess
cd vitess/examples/operator

#
kubectl create namespace vite

# Install the Operator
kubectl apply -n vitess -f operator.yaml

# Bring up an initial cluster
cat 101_initial_cluster.yaml | sed -e 's/example/vitess/g' \
                                   -e 's/http:\/\/localhost:14001/https:\/\/vitess.dev.run/g' \
                             | kubectl apply -n vitess -f -

# Apply schema and vschema
vtctldclient ApplySchema --server=$(kubectl get svc -n vitess | grep vitess-vtctld | awk '{print $3}'):15999 --sql-file="create_commerce_schema.sql" commerce
vtctldclient ApplyVSchema --server=$(kubectl get svc -n vitess | grep vitess-vtctld | awk '{print $3}'):15999 --vschema-file="vschema_commerce_initial.json" commerce

#
vtctldclient GetKeyspaces --server=$(kubectl get svc -n vitess | grep vitess-vtctld | awk '{print $3}'):15999

# Insert and verify data
mysql -h $(kubectl get svc -n vitess | grep vitess-vtgate | awk '{print $3}') -P 3306 -u vitess -pvitess -e "show databases"
mysql -h $(kubectl get svc -n vitess  | grep zone1-vtgate | awk '{print $3}') -P 3306 -u vitess -pvitess --table < ../common/insert_commerce_data.sql
mysql -h $(kubectl get svc -n vitess  | grep zone1-vtgate | awk '{print $3}') -P 3306 -u vitess -pvitess --table < ../common/select_commerce_data.sql

# Down cluster
kubectl delete -n vitess -f operator.yaml
kubectl delete -n vitess -f 101_initial_cluster.yaml
```
