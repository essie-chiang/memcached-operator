export GOROOT=/usr/local/go
export PATH=$GOROOT/bin:$PATH 

operator-sdk init --domain example.com --repo github.com/essie-chiang/memcached-operator
operator-sdk create api --group cache --version v1alpha1 --kind Memcached --resource --controller

make docker-build docker-push IMG="quay.io/jysjiangwork/memcached-operator:v0.0.2"

make bundle IMG="quay.io/jysjiangwork/memcached-operator:v0.0.3"
make bundle-build bundle-push BUNDLE_IMG="quay.io/jysjiangwork/memcached-operator-bundle:v0.0.1"
