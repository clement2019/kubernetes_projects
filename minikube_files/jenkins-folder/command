kubectl create -f jenkins-deployment.yaml --namespace jenkins

kubectl get pods -n jenkins

#create jenkis service
kubectl create -f jenkins-service.yaml --namespace jenkins

#get service

kubectl get services --namespace jenkins

# accessing jenkins Ui

## to dspol,ay jenkins on the browser run 

minikube service jenkins

kubectl get nodes -o wide
kubectl get pods -n jenkins

kubectl logs jenkins-6fb994cfc5-twnvn -n jenkins

ou might need to scroll up or down to find the password:

Running from: /usr/share/jenkins/jenkins.war
webroot: EnvVars.masterEnvVars.get("JENKINS_HOME")
. . .

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

your_jenkins_password

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword
. . .