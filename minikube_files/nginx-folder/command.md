## to start minkube the single node kubernetes cluster run the caokamd below

minikube start
alias k=kubectl
k get nodes
k get pods 
k get po
k create namespace test
k apply -f deployment.yaml
k apply -f service.yaml 

# when you delete a deployment file you authomatically detes the pods
k delete -f deployment.yaml 
k delete -f service.yaml 
## create the file in test namaspace
k apply -f deployment.yaml -n test
k apply -f service.yaml -n test

#delete pods 
k delete pod <podname>

# check the pods created in that namespace
k get pods -n test

k get events

k describe svc nginx  -n test
k get svc -n test 

# # troubleshooting

k get nodes -o wide
k get pods -n test

k logs jenkins-6fb994cfc5-twnvn -n test

# to run nginx on the browser run the command within the test namespace

 minikube service nginx -n test


 ## =========================================================

 using the imperative command

 # if you are confused run the coamnnd
 k run --help

 # to create a pod in yaml before running itbut output 

 k run nginxweb1 --image nginx -o yaml >output.yaml


 # Dry run; print the corresponding API objects without creating them
  kubectl run nginx --image=nginx --dry-run=client

  kubectl run nginxweb2 --image=nginx --dry-run=client -o yaml >output3.yaml

  k get pods
NAME                                        READY   STATUS    RESTARTS       AGE
flask-app-deployment-7899dfddf8-6qm6m       1/1     Running   0              70m
flask-app-deployment-7899dfddf8-zprwn       1/1     Running   0              69m
nginxweb1                                   1/1     Running   0              20m
nginxweb2                                   1/1     Running   0              20s
prometheus-alertmanager-0                   1/1     Running   6 (3d3h ago)   49d
prometheus-prometheus-node-exporter-rs4fd   1/1     Running   7 (3d3h ago)   49d

# since the pod is running go inside the pod aND SHOW THE FOLDERS INSIDE IT

k exec -it nginxweb2 -- sh 

# show the folders

ls
cd dev 
ls


# Now carry out this above task in test namespace
kubectl run nginxweb2 --image=nginx -ns test --dry-run=client -o yaml >output4.yaml


   # Start a busybox pod and keep it in the foreground, don't restart it if it exits
  kubectl run -i -t busybox --image=busybox --restart=Never





