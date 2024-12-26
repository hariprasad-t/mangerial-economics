# How to Add a User to RabbitMQ in a Docker Container  
   
RabbitMQ is a powerful message broker that is widely used for building scalable and reliable applications. Running RabbitMQ in a Docker container is a convenient way to manage your messaging infrastructure. This guide will walk you through the process of adding a new user to your RabbitMQ instance running inside a Docker container.  
   
## Prerequisites  
   
Before you begin, ensure you have the following:  
   
- Docker installed and running on your system.  
- A RabbitMQ Docker container running with the management plugin enabled.  
   
## Step-by-Step Guide  
   
### Step 1: Verify Your RabbitMQ Container  
   
First, ensure that your RabbitMQ container is up and running. You can list all running containers using:  
   
```bash  
docker ps  
```  
   
Look for a container using the `rabbitmq:management` image, as it includes the management plugin necessary for web-based administration.  
   
### Step 2: Access the RabbitMQ Container  
   
You will use `docker exec` to run commands inside your RabbitMQ container. The following steps will guide you through adding a new user.  
   
### Step 3: Add a New User  
   
Run the following command to add a new user. Replace `<username>` and `<password>` with your desired credentials:  
   
```bash  
sudo docker exec -it rabbitmq rabbitmqctl add_user <username> <password>  
```  
   
Example:  
```bash  
sudo docker exec -it rabbitmq rabbitmqctl add_user rabbitmq_user pass  
```  
   
### Step 4: Assign User Tags  
   
To give your new user administrative rights, assign the `administrator` tag:  
   
```bash  
sudo docker exec -it rabbitmq rabbitmqctl set_user_tags <username> administrator  
```  
   
Example:  
```bash  
sudo docker exec -it rabbitmq rabbitmqctl set_user_tags rabbitmq_user administrator  
```  
   
### Step 5: Set User Permissions  
   
Define the permissions for your new user on the default virtual host (`/`). This command grants full permissions:  
   
```bash  
sudo docker exec -it rabbitmq rabbitmqctl set_permissions -p / <username> ".*" ".*" ".*"  
```  
   
Example:  
```bash  
sudo docker exec -it rabbitmq rabbitmqctl set_permissions -p / rabbitmq_user ".*" ".*" ".*"  
```  
   
### Step 6: Verify User Creation  
   
To confirm that the user has been added successfully, list all users with:  
   
```bash  
sudo docker exec -it rabbitmq rabbitmqctl list_users  
```  
   
This should display your newly created user along with any existing ones.  
   
## Conclusion  
   
You have successfully added a new user to your RabbitMQ instance running in a Docker container. By following these steps, you can manage user access and permissions efficiently. RabbitMQ’s flexibility, combined with Docker’s convenience, allows you to build scalable and secure messaging solutions.
