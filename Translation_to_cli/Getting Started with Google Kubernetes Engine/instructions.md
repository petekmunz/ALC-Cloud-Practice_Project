# Getting Started with Google Kubernetes Engine (GKE)

Getting through the hands-on qwiklab just using the cli. We will be using the [cloud shell](https://cloud.google.com/shell) provided by Google Cloud Console.

## Confirm that the needed APIs are enabled

We need to confirm that:

* Kubernetes Engine API
* Container Registry API

are enabled in our project.

1. To view the services enabled for your project, run the following command

    ```shell
    gcloud services list
    ```

2. If `container.googleapis.com` and/or `containerregistry.googleapis.com` are not among the list, run the following command to enable them

    ```shell
    gcloud services enable container.googleapis.com containerregistry.googleapis.com
    ```

## Start a Kubernetes Engine cluster

1. For convenience purposes we set the zone assigned to us by qwiklabs into an environment variable called `MY_ZONE`, in this case we use `us-central1-a` as an example

    ```shell
    export MY_ZONE=us-central1-a
    ```

2. Start a Kubernetes cluster managed by Kubernetes Engine. Name the cluster webfrontend and configure it to run 2 nodes

    ```shell
    gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2
    ```

3. After the cluster is created, check your installed version of Kubernetes using the `kubectl version` command

    ```shell
    kubectl version
    ```

    The `gcloud container clusters create` command automatically authenticated `kubectl` for you

4. To view your running nodes (A node in GCP is a provisioned Virtual Machine instance), execute the following command

    ```shell
    gcloud compute instances list
    ```

## Run and deploy a container

1. Launch a single instance of the nginx container

    ```shell
    kubectl create deploy nginx --image=nginx:1.17.10
    ```

    In Kubernetes, all containers run in pods. This use of the kubectl create command caused Kubernetes to create a deployment consisting of a single pod containing the nginx container. A Kubernetes deployment keeps a given number of pods up and running even in the event of failures among the nodes on which they run. In this command, you launched the default number of pods, which is 1.

2. View the pod running the nginx container

    ```shell
    kubectl get pods
    ```

3. Expose the nginx container to the internet

    ```shell
    kubectl expose deployment nginx --port 80 --type LoadBalancer
    ```

    Kubernetes created a service and an external load balancer with a public IP address attached to it. The IP address remains the same for the life of the service. Any network traffic to that public IP address is routed to pods behind the service: in this case, the nginx pod.

4. View the new service

    ```shell
    kubectl get services
    ```

5. Once the **External-IP** field is populated for your service, open a web browser tab & paste the address into the address bar and the default home page of the Nginx browser is displayed

6. Scale up the number of pods running in your service

    ```shell
    kubectl scale deployment nginx --replicas 3
    ```

7. Confirm that Kubernetes has updated the number of pods

    ```shell
    kubectl get pods
    ```

8. Return to the web browser tab in which you viewed your cluster's external IP address. Refresh the page to confirm that the nginx web server is still responding. (It should still be working) 😀
