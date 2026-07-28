# devops-notes

# kubernetes
# Day 1
# how to make kubernetes cluster in amazon EKS ?
steps : 1) go to cluster - create cluster 
2) custom configuration
3) off auto EKS mode 
4) give cluster name 
5) create new role ( just click on new role it will create auto configration then just create role ) then NEXT 
6) in specify networking : add security group then NEXT 
7) other configration all default : CREATE
9) launch ec2 instance of c7i
10) use kubernetes repo from rohit sirs git and run given commands . and run them on ec2 run commands upto aws configure.
11) go to cluster and in compute (config) then add node group
12) give name to that group
13) create new role just click auto genrate then click create then NEXT
14) then NEXT - NEXT at last create 
15) then go to kubenets repo and run remaning command provided by rohit sir 

# Day 2
# How to create service and pods ?
1)first create a yaml file for pod:
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80

this is example file for pod creation.
create this file on ec2
run command "kubectl apply -f <filename>"
for checking pods kubectl get pods
for more datailed info : kubectl pods -o wide
2) create services for pods :
there are 3 important main types of services 1) clusterIP 2) nodeport 3)loadbalancer
create yaml file for each of them 1) clusterIP : with in the cluster , internally , pod to pod communication .
                                  2) NodePort : internally and externally , 30000-32767= port range , use for dev and test .
                                  3) loadbalancer : need cloud provider , internally and externally .
3)create yaml file for clusterIP :
  apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
this for creating clusterIP service
then run command "kubectl apply -f <yamlfile>"

4)create yaml file for nodeport :
apiVersion: v1
kind: Service
metadata:
  name: nodeport
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
    - protocol: TCP
      port: 80
      nodePort: 31000
  this is for creating nodeport services 
  then run command kubectl apply -f <yamlfile>
  then run it on browser with take node server ip and port 

5)create yaml file for loadbalancer :
  apiVersion: v1
kind: Service
metadata:
  name: loadbalancer
spec:
  selector:
    app: nginx
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80

  this is for creating loadbalancer service 
  then run command kubectl apply -f <yamlfile>
  then copy dns on browser.

# commands of kubernetes 
