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
