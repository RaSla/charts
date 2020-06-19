# HELM-charts Repo
This is the HELM-charts repository.

## Create HELM-chart
I set up GitHub Pages to point to the `master branch /docs folder`.  
From there, I can create and publish docs like this:
```console
## Create chart-template
helm create src/tsdb
## Edit chart-files...
```

## Update HELM-repository
```console
## Make new chart-package
helm package src/tsdb
mv tsdb-0.2.0.tgz docs/

helm repo index docs --url https://rasla.github.io/charts

git add -i
git commit -m "helm: tsdb-0.2.0"
git push origin master
```

## Usage
```console
## Configure HELM

$ helm repo add ra https://rasla.github.io/charts
"ra" has been added to your repositories
$ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
"stable" has been added to your repositories

$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ra" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈

$ helm search repo tsdb
NAME    CHART VERSION  APP VERSION  DESCRIPTION                                       
ra/tsdb 0.2.0          0.7.0        The TimeSeries DataBase for AlteroUniversal (Cl...

## Configure & install CHART
$ wget https://github.com/RaSla/charts/raw/master/config-tsdb.example.yaml  -O config-tsdb.local.yaml
$ sed "s/dbStorage: 'clickhouse'/dbStorage: 'influxdb'/g" config-tsdb.local.yaml > config-tsdb-influxdb.local.yaml

$ kubectl create namespace ksvd
$ helm install au-tsdb ra/tsdb  -f config-tsdb.local.yaml  -n ksvd  --version 0.2.0
$ helm install influx ra/tsdb  -f config-tsdb-influxdb.local.yaml  

$ helm ls -A
NAME    NAMESPACE   REVISION    UPDATED                                  STATUS     CHART        APP VERSION
au-tsdb ksvd        1           2020-06-19 15:18:55.33723807 +0500 +05   deployed   tsdb-0.2.0   0.7.0      
influx  default     1           2020-06-19 15:19:25.066167106 +0500 +05  deployed   tsdb-0.2.0   0.7.0

$ kubectl get po -A | egrep "READY|tsdb"
NAMESPACE   NAME                     READY   STATUS    RESTARTS   AGE
ksvd        au-tsdb-clickhouse-0     1/1     Running   0          1m32s
default     influx-tsdb-influxdb-0   1/1     Running   0          1m2s
```
