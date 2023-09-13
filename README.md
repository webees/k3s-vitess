# Prerequisites 
```shell
apt install mysql-client -y
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
#
kubectl delete -n vitess -f operator.yaml
kubectl delete -n vitess -f 101_initial_cluster.yaml
```
