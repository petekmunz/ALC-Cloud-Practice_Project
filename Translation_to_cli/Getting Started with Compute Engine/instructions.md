# Getting Started with Compute Engine

Getting through the hands-on qwiklab just using the cli.

## Create Virtual Machines

1. Display a list of all the zones in the region your project is assigned to, we use the default `us-central1` region

    ```shell
    gcloud compute zones list | grep us-central1
    ```

2. From the displayed zones, you can choose & set a default zone for your compute engine, in this case we use `us-central1-a`

    ```shell
    gcloud config set compute/zone us-central1-a
    ```

3. Create a Virtual Machine instance called `my-vm-1` in that zone

    ```shell
    gcloud compute instances create "my-vm-1" \
    --machine-type "n1-standard-1" \
    --image-project "debian-cloud" \
    --image "debian-9-stretch-v20190213" \
    --subnet "default" \
    --tags "http-server"
    ```

4. Create another Virtual Machine called `my-vm-2` in a zone other than the default we earlier specified

    ```shell
    gcloud compute instances create "my-vm-2" \
    --zone "us-central1-b" \
    --machine-type "n1-standard-1" \
    --image-project "debian-cloud" \
    --image "debian-9-stretch-v20190213" \
    --subnet "default"
    ```

5. Get the external IP address of `my-vm-1`. We will need this later, note it down

    ```shell
    gcloud compute instances describe my-vm-1 \
    --format='get(networkInterfaces[0].accessConfigs[0].natIP)'
    ```

## Connect between VM instances

1. Connect to `my-vm-2` instance via ssh where `PROJECT_ID` is your project-id on the [Google Cloud Console](https://console.cloud.google.com/)

    ```shell
    gcloud compute ssh --project PROJECT_ID --zone "us-central1-b" my-vm-2
    ```

2. Use the ping command to confirm that `my-vm-2` can reach `my-vm-1` over the network

    ```shell
    ping my-vm-1
    ```

3. Satisfied there is a connection use **Ctrl+C** to abort the ping command

4. Connect via ssh to `my-vm-1`

    ```shell
    gcloud compute ssh --project PROJECT_ID --zone "us-central1-a" my-vm-1
    ```

5. Once connected, install the Nginx web-server

    ```shell
    sudo apt-get install nginx-light -y
    ```

6. Use the nano text editor to add a custom message to the home page of the web server

    ```shell
    sudo nano /var/www/html/index.nginx-debian.html
    ```

7. Use the arrow keys to move the cursor to the line just below the h1 header. Add text like this, and replace YOUR_NAME with your name

    ```shell
    Hi from YOUR_NAME
    ```

8. Press **Ctrl+O** and then press **Enter** to save your edited file, and then press **Ctrl+X** to exit the nano text editor

9. Confirm the web-server is live by running the following command on the `my-vm-1` instance

    ```shell
    curl http://localhost/
    ```

    The response will be the HTML source of the web server's home page, including your line of custom text

10. Exit the `my-vm-1` instance by executing this command

    ```shell
    exit
    ```

    You will return to the command prompt on `my-vm-2`, if not reconnect to the `my-vm-2` instance

11. To confirm that `my-vm-2` can reach the web server on `my-vm-1`, at the command prompt on `my-vm-2`, execute this command

    ```shell
    curl http://my-vm-1/
    ```

    The response will again be the HTML source of the web server's home page, including your line of custom text

12. From [create virtual machines - step 5](##create-virtual-machines) copy and paste the address obtained into a browser tab and you should see the server home page with your custom text 😀
