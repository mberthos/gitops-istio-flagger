#Adding the Flux Repo
helm repo add fluxcd https://charts.fluxcd.io

#Installing the HelmRelease CRD
kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/master/deploy/crds.yaml

#Create the namespace for flux Installation
kubectl create namespace flux

#This would create the kubernetes secret for flux to communicate with GitHub
kubectl create secret generic flux-git-deploy --from-file=identity=/home/marcelo/.ssh/id_rsa -n flux --dry-run=client -o yaml | kubectl apply -f -

#Install fluxcd deployment
helm install flux fluxcd/flux --set git.url=git@github.com:mberthos/gitops-istio-flagger.git --set git.branch=master --set git.secretName="flux-git-deploy" --set git.user=flux-user --set git.path=helm-releases --namespace flux

#Install helm-operator deployment
helm upgrade -i helm-operator fluxcd/helm-operator --set git.ssh.secretName=flux-git-deploy --namespace flux

#Create a namespace to deploy our HelmRelease
kubectl create ns fluxcd-demo

#
