# Container orchestration with Kubernetes

The goals of this lab are to:
- Install [Minikube](https://github.com/kubernetes/minikube)
- Learn to use `kubectl` by running a basic [Node.js](https://nodejs.org/) web app
- Learn to expose a Kubernetes service to the outside
- Learn to scale up and down a Kubernetes deployment
- Run a multiple pod application in Kubernetes

## Useful links

- [Minikube installation document](https://kubernetes.io/docs/tasks/tools/install-minikube/)

## Lab

## Prerequisites

Before you can start the lab, you have to:
1. Install [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) following the instructions depending on your OS.
2. Start Minikube with:
   ```
   minikube start
   ```
3. Verify that everything is OK with:
   ```
   minikube status
   ```
4. Clone the lab repository to your computer:
  ```
   git clone https://github.com/leopaul36/dsti-fall-devops-kubernetes.git
  ```

### Lab part 1

1. Open a terminal
2. Run a `deployment` with one `pod` with the following command:
   ```
   kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
   ```
   `gcr.io/google-samples/kubernetes-bootcamp:v1` is a Docker image of a basic Node.js web application.
3. List all the running pods with:
   ```
   kubectl get pods
   ```
   Wait until Pod readiness reaches 1/1 and save the Pod name somewhere
4. Display the pod logs with:
   ```
   kubectl logs $POD_NAME
   ```
5. Run a command inside the pod with:
   ```
   kubectl exec $POD_NAME -- cat /etc/os-release
   ```
6. Open a shell inside the pod with:
   ```
   kubectl exec -ti $POD_NAME bash
   ```
7. List the content of the directory you are in and try to find the JavaScript source code file
8. Make sure that the web app is responding inside the container by querying it with `curl`
   **Hint** the port on which the app responds is defined in the JavaScript file
9. Are you able to query the web app outside of the pod (from your local machine)?

### Lab Part 2

1. Expose the deployment you created in the first part of the lab with:
   ```
   kubectl expose deployments/$DEPLOYMENT_NAME --type="NodePort" --port $PORT_NUMBER
   ```
   **Hint** You need to replace `$DEPLOYMENT_NAME` with the actual name of the `deployment` as well as `$PORT_NUMBER`
2. Find out on which port the service has been attached to with:
   ```
   kubectl get services
   ```
3. Get the IP of your Minikube VM with:
   ```
   minikube ip
   ```
4. Using the answers of questions 2. and 3., open your web browser and try to reach the web app.

### Lab Part 3

1. Scale up your deployment to a total number of 5 pods with:
  ```
  kubectl scale deployments/kubernetes-bootcamp --replicas=5
  ```

2. Make sure that you have 5 pods running using one the commands we have seen the part 1 of the lab. Which command did you use?
3. Open the exposed service through your web browser again.
   Force refresh a couple times using `CTRL+F5`
   What is happening happen? Why?
4. Scale down again your deployment to 2 pods and confirm the other 3 are not running anymore.

### Lab Part 4

1. Prepare to hit `CTRL+F5` on your browser multiple times right after launching the following command:
2. Update the Docker image used by the `deployment` with:
   ```
   kubectl set image deployments/$DEPLOYMENT_NAME kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
   ```
3. What happened to the web page?
4. Update the Docker image used by the `deployment` again by setting the image to `jocatalin/kubernetes-bootcamp:v3`
5. List all the running pods, what is happening here?
6. Cancel the previous operation by running:
   ```
   kubectl rollout undo deployments/kubernetes-bootcamp
   ```
7. Rollback the service to the image we first chose in the part 1 of the lab.

### Lab Part 5

1. Clean up what you did in the previous part with:
   ```
   kubectl delete service $SERVICE_NAME
   kubectl delete deployment $DEPLOYMENT_NAME
   ```
2. Using the [deployment documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/), fill out the blank (`TO COMPLETE #1`) in `./deployment.yaml` to define a deployment based on the one we ran in part 1.
3. Once you completed the file, run:
   ```
   kubectl apply -f deployment.yaml
   ```
   Are the pods running?
4. Using the [service documentation](https://kubernetes.io/docs/concepts/services-networking/service/), fill out the blank in `service.yaml`
5. Once you completed the file, run:
   ```
   kubectl apply -f service.yaml
   ```
   Can you access the service through your web browser?
6. Fill out `TO COMPLETE #2` inside `deployment.yaml` to create 3 replicas your app.
7. Once you completed the file, run:
   ```
   kubectl apply -f deployment.yaml
   ```
   Force refresh on the browser a couple times. Are you hitting different replicas?

### Bonus part

1. Write your own Kubernetes [deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) that runs 3 replicas of the **Hello World Web App** from the [docker lab](https://github.com/leopaul36/dsti-fall-devops-docker).
2. Expose this deployment with a Kubernetes [service](https://kubernetes.io/docs/concepts/services-networking/service/) and open the web app with your web browser.