# HELM-charts Repo

This is the HELM-charts repository.

## Create HELM-chart

I set up GitHub Pages to point to the `master branch /docs folder`.  
From there, I can create and publish docs like this:

```console
## Create chart-template
$ helm create src/tsdb
## Edit chart-files...
```

More detailed example of HELM-chart creation you can see at:
<https://github.com/RaSla/docker-magic/tree/develop/3-kubernetes/06-helm>

## Update HELM-repository
```console
## Make new chart-package
$ helm package src/tsdb
Successfully packaged chart and saved it to: /home/rasla/git/charts/tsdb-0.2.0.tgz
$ mv tsdb-0.2.0.tgz docs/

$ helm repo index docs --url https://rasla.github.io/charts

$ git add -i
$ git commit -m "helm: tsdb-0.2.0"
$ git push origin master
```

## Usage
### Configure HELM
```console
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
```

### Configure & install CHART
```console
$ wget https://github.com/RaSla/charts/raw/master/config-tsdb.example.yaml      -O config-tsdb.local.yaml
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

### Upgrade CHART
```console
$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ra" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈

$ helm search repo tsdb
NAME    CHART VERSION  APP VERSION  DESCRIPTION                                       
ra/tsdb 0.2.1          0.7.0        The TimeSeries DataBase for AlteroUniversal (Cl...

$ helm upgrade influx ra/tsdb  -f config-tsdb-influxdb.local.yaml
Release "influx" has been upgraded. Happy Helming!

$ helm ls -A
NAME    NAMESPACE   REVISION    UPDATED                                  STATUS     CHART        APP VERSION
au-tsdb ksvd        1           2020-06-19 15:18:55.33723807 +0500 +05   deployed   tsdb-0.2.0   0.7.0      
influx  default     2           2020-06-19 16:43:04.294110636 +0500 +05  deployed   tsdb-0.2.1   0.7.0
```

### Rollback CHART
if something goes wrong after chart's upgrade - you can do `rollback`
```console
$ kubectl get po -A | egrep "READY|tsdb"
NAMESPACE   NAME                     READY   STATUS            RESTARTS  AGE
ksvd        au-tsdb-clickhouse-0     1/1     Running           0         91m
default     influx-tsdb-influxdb-0   0/1     ImagePullBackOff  0         1m2s

$ helm history influx
REVISION   UPDATED                    STATUS      CHART        APP VERSION  DESCRIPTION     
1          Fri Jun 19 15:19:25 2020   superseded  tsdb-0.2.0   0.7.0        Install complete
2          Fri Jun 19 16:43:04 2020   superseded  tsdb-0.2.1   0.7.0        Upgrade complete
3          Fri Jun 19 16:49:34 2020   deployed    tsdb-0.2.1   0.7.0        Upgrade complete
$ helm rollback influx 2
Rollback was a success! Happy Helming!

$ helm ls -A
NAME    NAMESPACE   REVISION    UPDATED                                  STATUS     CHART        APP VERSION
au-tsdb ksvd        1           2020-06-19 15:18:55.33723807 +0500 +05   deployed   tsdb-0.2.0   0.7.0      
influx  default     4           2020-06-19 16:54:29.725217406 +0500 +05  deployed   tsdb-0.2.1   0.7.0

$ helm history influx
REVISION   UPDATED                    STATUS      CHART        APP VERSION  DESCRIPTION     
1          Fri Jun 19 15:19:25 2020   superseded  tsdb-0.2.0   0.7.0        Install complete
2          Fri Jun 19 16:43:04 2020   superseded  tsdb-0.2.1   0.7.0        Upgrade complete
3          Fri Jun 19 16:49:34 2020   superseded  tsdb-0.2.1   0.7.0        Upgrade complete
4          Fri Jun 19 16:54:29 2020   deployed    tsdb-0.2.1   0.7.0        Rollback to 2

$ kubectl get po -A | egrep "READY|tsdb"
NAMESPACE   NAME                     READY   STATUS    RESTARTS  AGE
ksvd        au-tsdb-clickhouse-0     1/1     Running   0         99m
default     influx-tsdb-influxdb-0   1/1     Running   0         16s
```

### Uninstall CHART
```console
$ helm uninstall influx
release "influx" uninstalled
$ helm uninstall -n ksvd au-tsdb
release "au-tsdb" uninstalled
```
