## Experiment on AWS Process
1. Changing localhost to AWS Public IP Address

Transitioning the connection from 'localhost' to the Public IPv4 (in this case, 54.236.28.248) shifts the system's communication from an internal loopback that never leaves to a global broadcast across the internet. While 'localhost' acts like a private conversation the computer has with itself, the Public IP provides the specific "Global GPS coordinate" required for the Rust programs to navigate through multiple internet routers and reach the virtual machine in the AWS cloud. By updating this address, we bridge the gap between the local development environment and the remote RabbitMQ broker, enabling the CrosstownBus library to perform a successful network handshake through the AWS Security Group on port 5672. This change is the technical glue that allows a publisher on our desk to deposit messages into a mailbox hosted in a remote data center, effectively turning two isolated programs into a synchronized, distributed event-driven system.

2. The Remote AWS Setup Process

Moving the message broker to the cloud involves transitioning from a local environment to a distributed one. First, an EC2 instance is provisioned on AWS, serving as a virtual server in the sky. To make this server accessible, we must configure the AWS Security Group (a virtual firewall) to allow incoming traffic on port 5672 for the AMQP protocol and port 15672 for the web dashboard. Once the server is open, Docker is installed to run the RabbitMQ service in a containerized environment, ensuring the broker stays isolated and stable.

3. Enabling Remote Guest Access

By default, RabbitMQ has a security feature that prevents the "guest" user from connecting from any address other than localhost. To allow our device to connect to AWS using these default credentials, we have to modify the broker's configuration file. By running a command inside the Docker container to set loopback_users = none, we tell RabbitMQ to stop restricting the guest account to local connections only. After restarting the container to apply this change, we must also ensure the guest user is tagged as an administrator and has full permissions (.* for configure, write, and read).

## Sending and Processing Event
<img width="1124" height="177" alt="Screenshot 2026-05-12 at 10 58 59" src="https://github.com/user-attachments/assets/6487774e-6ca0-4dcd-b782-30d8ec42162b" />
<img width="1113" height="336" alt="Screenshot 2026-05-12 at 10 58 48" src="https://github.com/user-attachments/assets/5fe9340c-11f8-4058-af15-4906b3788490" />
<img width="1582" height="961" alt="Screenshot 2026-05-12 at 10 58 38" src="https://github.com/user-attachments/assets/1b8f6e59-3c3e-4c4c-91e7-6ed6e6b94e7b" />

In the publisher terminal, we see the program being compiled and executed on my local device without any network or credential errors. This successful "Running" status indicates that the publisher established a secure handshake with the remote broker to deposit the event batch. The subscriber terminal demonstrates that the Rust application is successfully consuming events from the remote queue hosted on AWS. These logs confirm the receipt of five distinct user creation messages, each displaying the specific test names and my student identification number (2406453606). The RabbitMQ management dashboard provides the visual proof of this transaction through a clear spike in the message rate chart. This peak represents the high speed transfer where the five messages were published to the exchange and then instantly delivered to the active subscriber across the internet.
