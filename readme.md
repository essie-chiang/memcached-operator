export GOROOT=/usr/local/go
export PATH=$GOROOT/bin:$PATH 

operator-sdk init --domain example.com --repo github.com/essie-chiang/memcached-operator
operator-sdk create api --group cache --version v1alpha1 --kind Memcached --resource --controller

make docker-build docker-push IMG="quay.io/jysjiangwork/memcached-operator:v0.0.2"

make bundle IMG="quay.io/jysjiangwork/memcached-operator:v0.0.3"
make bundle-build bundle-push BUNDLE_IMG="quay.io/jysjiangwork/memcached-operator-bundle:v0.0.1"

operator-sdk run bundle quay.io/jysjiangwork/memcached-operator-bundle:v0.0.1

make deploy IMG="quay.io/jysjiangwork/memcached-operator:v0.0.3"

# make deploy IMG="quay.io/jysjiangwork/memcached-operator:v0.0.3"
/root/essie_dir/memcached-operator/bin/controller-gen rbac:roleName=manager-role crd webhook paths="./..." output:crd:artifacts:config=config/crd/bases
cd config/manager && /root/essie_dir/memcached-operator/bin/kustomize edit set image controller=quay.io/jysjiangwork/memcached-operator:v0.0.3
/root/essie_dir/memcached-operator/bin/kustomize build config/default | kubectl apply -f -
namespace/memcached-operator-system created
customresourcedefinition.apiextensions.k8s.io/memcacheds.cache.example.com created
serviceaccount/memcached-operator-controller-manager created
role.rbac.authorization.k8s.io/memcached-operator-leader-election-role created
clusterrole.rbac.authorization.k8s.io/memcached-operator-manager-role created
clusterrole.rbac.authorization.k8s.io/memcached-operator-metrics-reader created
clusterrole.rbac.authorization.k8s.io/memcached-operator-proxy-role created
rolebinding.rbac.authorization.k8s.io/memcached-operator-leader-election-rolebinding created
clusterrolebinding.rbac.authorization.k8s.io/memcached-operator-manager-rolebinding created
clusterrolebinding.rbac.authorization.k8s.io/memcached-operator-proxy-rolebinding created
configmap/memcached-operator-manager-config created
service/memcached-operator-controller-manager-metrics-service created
deployment.apps/memcached-operator-controller-manager created


oc get all
NAME                                                         READY   STATUS    RESTARTS   AGE
pod/memcached-operator-controller-manager-7d66bb454c-7dbkm   2/2     Running   0          47m

NAME                                                            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/memcached-operator-controller-manager-metrics-service   ClusterIP   172.21.95.208   <none>        8443/TCP   47m

NAME                                                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/memcached-operator-controller-manager   1/1     1            1           47m

NAME                                                               DESIRED   CURRENT   READY   AGE
replicaset.apps/memcached-operator-controller-manager-7d66bb454c   1         1         1       47m
