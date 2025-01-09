---  
layout: default  
title: "How to Add a User to RabbitMQ in a Docker Container"  
---  
   
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


apptainer exec --bind rabbitmq_data:/var/lib/rabbitmq/mnesia docker://rabbitmq:4.0-management rabbitmq-server  - to start on gpu cluster
apptainer exec --bind rabbitmq_data:/var/lib/rabbitmq/mnesia docker://rabbitmq:4.0-management rabbitmqctl add_user rabbitmq_user pass
apptainer exec docker://rabbitmq:4.0-management rabbitmqctl set_user_tags rabbitmq_user administrator 
apptainer exec docker://rabbitmq:4.0-management rabbitmqctl set_permissions -p / rabbitmq_user ".*" ".*" ".*"

apptainer exec --bind rabbitmq_data:/etc/rabbitmq docker://rabbitmq:4.0-management rabbitmq-plugins enable rabbitmq_management

cd $HOME
cat .erlang.cookie, now copy the key it should be the same for all the nodes inside the cluster
PBLTKGGVZASAUHQUTDVF
PBLTKGGVZASAUHQUTDVF

next add ip and host name within /etc/hosts

apptainer exec docker://rabbitmq:4.0-management rabbitmqctl join_cluster rabbit@rng-dl01-w085

<img width="1597" alt="image" src="https://github.com/user-attachments/assets/b0eb4910-4ec0-4fb5-a27c-337ae21b99ac" />


```
import pika

RABBIT_USER = "rabbitmq_user"
RABBIT_PASS = "pass"

HEAD_IP = "rng-dl01-w085"
RABBIT_PORT = 5672


class RabbitBuffer:
    def __init__(self, queue_name: str) -> None:
        self.queue_name = queue_name

        self.credentials = pika.PlainCredentials(RABBIT_USER, RABBIT_PASS)

        self.connection = pika.BlockingConnection(pika.ConnectionParameters(HEAD_IP, RABBIT_PORT, "/", self.credentials))

        self.channel = self.connection.channel()
        self.queue = self.channel.queue_declare(queue=self.queue_name, durable=True)

    def produce(self, messages):
        for message in messages:
            self.channel.basic_publish(
                exchange="",
                routing_key=self.queue_name,
                body=message,
                properties=pika.BasicProperties(delivery_mode=2),  # make messages persistent
            )

    def consume(self, num_messages: int):
        messages = []
        for _ in range(num_messages):
            method_frame, header_frame, body = self.channel.basic_get(queue=self.queue_name)
            if method_frame:
                messages.append(body)
                self.channel.basic_ack(method_frame.delivery_tag)
        return messages
```

```
buffer = RabbitBuffer('testqueue')
buffer.produce(["hello", "world"]*200000)
```
<img width="1588" alt="image" src="https://github.com/user-attachments/assets/3e9a5d4d-1c3e-4afe-86dd-a57d1b3c1d8f" />

```
for start in range(1, 200000, 10000):  
        print(f"Consuming messages from {start} to {start + 10000}")  
        buffer.consume(10000)
```
Consuming messages from 1 to 10001
Consuming messages from 10001 to 20001
...
Consuming messages from 190001 to 200001

<img width="1590" alt="image" src="https://github.com/user-attachments/assets/cd65e4a2-f575-4187-8885-7b51c1a829de" />



apptainer pull docker://vllm/vllm-openai:v0.6.6  


## Conclusion  
   
You have successfully added a new user to your RabbitMQ instance running in a Docker container. By following these steps, you can manage user access and permissions efficiently. RabbitMQ’s flexibility, combined with Docker’s convenience, allows you to build scalable and secure messaging solutions.
