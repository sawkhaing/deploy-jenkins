# Deploy Jenkins to AKS
## For Manual Intervation
#### Helm install
<pre><code>curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null</code></pre>
<pre><code>sudo apt-get install apt-transport-https --yes</code></pre>
<pre><code>echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list</code></pre>
<pre><code>sudo apt-get update</code></pre>
<pre><code>sudo apt-get install helm</code></pre>

#### Create Namespace
<pre><code>kubectl create namespace jenkins --dry-run=client -o yaml | kubectl apply -f -</code></pre>

#### Create Service Account
<pre><code>kubectl apply -f jenkins-sa.yaml</code></pre>
RBAC access to the cluster.

#### Install Jenkins with helm
If want to add more plugins in jenkins-values.yaml https://github.com/sawkhaing/deploy-jenkins/blob/bdbbdf7e9aa52f160c73d98e0e774ccc85e2df84/jenkins-values.yaml#L241-L248
<pre><code>helm repo add jenkinsci https://charts.jenkins.io</code></pre>
<pre><code>helm repo update</code></pre>
<pre><code>helm install jenkins -n jenkins -f jenkins-values.yaml jenkinsci/jenkins</code></pre>
Jenkins default username "**admin**".
After installed with helm, wait the jenkins pods to the running state.

#### For Jenkin Password
<pre><code>jsonpath="{.data.jenkins-admin-password}"
secret=$(kubectl get secret -n jenkins jenkins -o jsonpath=$jsonpath)
echo $(echo $secret | base64 --decode)</code></pre>

#### Jenkins URL
<pre><code>JENKINS_IP=$(kubectl get svc --selector=app.kubernetes.io/name=jenkins -n jenkins -o jsonpath="{.items[0].status.loadBalancer.ingress[0].ip}")</code></pre>
<pre><code>echo "Jenkins URL http://$JENKINS_IP:8080"</code></pre>

## For the Automation run from the Github Actions
Pass the environment variable to deploy jenkins in the desire AKS.
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_SUBSCRIPTION_ID
- ARM_TENANT_ID
- AZURE_CREDENTIALS
- RESOURCE_GROUP
- CLUSTER_NAME