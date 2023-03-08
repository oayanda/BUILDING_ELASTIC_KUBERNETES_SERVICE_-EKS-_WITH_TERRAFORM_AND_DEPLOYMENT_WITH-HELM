# BUILDING ELASTIC KUBERNETES SERVICE (EKS) WITH TERRAFORM AND DEPLOYMENT WITH HELM

View Terraform EKS cluster configuration files here

Install Helm on a Ubuntu OS

```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null

sudo apt-get install apt-transport-https --yes

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list

sudo apt-get update
sudo apt-get install helm
```

![helm install](./images/1.png)

Get Jekins helm chart from [Artifact Hub](https://artifacthub.io/packages/helm/jenkinsci/jenkins)

```bash
# Add the repository to helm so that you can easily download and deploy
helm repo add jenkins https://charts.jenkins.io

# Update helm repo
helm repo update
```

![helm install](./images/2.png)

```bash
# Install Jenkins chart
helm install [RELEASE_NAME] jenkins/jenkins --kubeconfig [kubeconfig file]
```
![helm install](./images/3.png)

Check the Helm deployment

```bash
helm ls --kubeconfig [kubeconfig file]

# Check the pods
kubectl get pods --kubeconfig [kubeconfig file]
```

![helm install](./images/4.png)

Deescribe the pod

```bash
kubectl describe po --kubeconfig kubeconfig
```
![helm install](./images/5.png)

For Multi cluster management merge all the kubeconfig files together using a kubectl plugin kofig.

- Install Kubectl plugin manager [krew](https://krew.sigs.k8s.io/docs/user-guide/setup/install/)



Install the konfig plugin

```bash
kubectl krew install konfig
```

![helm install](./images/6%20.png)

Import the kubeconfig into the default kubeconfig file. 

```bash
# Merge kubeconfig file into the default config file
  kubectl konfig import --save  [kubeconfig file]

  # Show all the contexts
  k config get-contexts

  # Set the current context to use for all kubectl and helm commands
  kubectl config use-context [name of EKS cluster]
```

![helm install](./images/7.png)

Test that it is working without specifying the --kubeconfig flag

```bash
# Display pods
k get pod

# Display the current context
k config current-context
```

![helm install](./images/8.png)

Get the password to the admin user using the instructions displayed when jenkins chart was installed See snapshoot above - *Install Jenkins chart*

```bash
# Get password
kubectl exec --namespace default -it svc/jenkins-server -c jenkins -- /bin/cat /run/secrets/additional/chart-admin-password && echo

#  Get the Jenkins URL 
kubectl --namespace default port-forward svc/jenkins-server 8080:8080
```
![helm install](./images/10.png)
![helm install](./images/9.png)