## Questions

1. How much data your publisher program will send to the message broker in one run?

In a single execution, the publisher program sends a total of five events to the message broker. These events are structured as UserCreatedEventMessage objects, specifically containing the user IDs "1" through "5" along with their corresponding names (Amir, Budi, Cica, Dira, and Emir). Each call to the publish_event method transmits one individual data packet, and because the code executes five such calls in succession, the RabbitMQ broker receives five distinct messages in the "user_created" queue to be held for processing.

2. The url of: “amqp://guest:guest@localhost:5672” is the same as in the subscriber program, what does it mean?

The connection URL is identical in both programs because it serves as the shared address for the central RabbitMQ message broker that facilitates communication between the independent programs. This URL, amqp://guest:guest@localhost:5672, defines the specific protocol (AMQP), the default administrative credentials (guest/guest), and the network location (localhost) where the broker is listening for incoming traffic on port 5672. By pointing both the publisher and the subscriber to the exact same endpoint, you ensure they are operating within the same system; the publisher knows exactly where to deposit the events, and the subscriber knows exactly which broker to query to pick up that specific data for processing.

## Running RabbitMQ
<img width="1582" height="959" alt="Screenshot 2026-05-11 at 12 25 32" src="https://github.com/user-attachments/assets/2975b916-a7b8-4624-a0a1-7c5bdc6e4e7f" />
<img width="1582" height="959" alt="Screenshot 2026-05-11 at 12 25 47" src="https://github.com/user-attachments/assets/da14f37e-b8e7-44a3-a963-f2567ce569bf" />
<img width="1582" height="959" alt="Screenshot 2026-05-11 at 12 25 58" src="https://github.com/user-attachments/assets/cc7d731a-fb32-4e5a-8884-154da19a2575" />
<img width="1382" height="832" alt="Screenshot 2026-05-11 at 12 26 59" src="https://github.com/user-attachments/assets/47e5f282-eb2f-4191-b033-7c3d445bbe10" />

## Sending and Processing Event
<img width="985" height="166" alt="Screenshot 2026-05-11 at 12 35 32" src="https://github.com/user-attachments/assets/68d5bd41-e39b-466c-bee7-9e50ea65a49b" />
<img width="1126" height="294" alt="Screenshot 2026-05-11 at 12 35 19" src="https://github.com/user-attachments/assets/645cc330-6d79-476e-ac6c-f8adf7a505a3" />

When you execute cargo run in the publisher directory, the program creates five distinct UserCreatedEventMessage objects and sends them to the RabbitMQ message broker at "localhost:5672". These events are not sent directly to the subscriber. Instead, RabbitMQ acts as a middleman, placing these messages into a queue named user_created. The spikes in the RabbitMQ "Message rates" chart represent the moments the publisher "published" these batches of data to the exchange. On the other side of the system, the subscriber program is running a continuous loop, listening for any new messages that appear in that specific queue. As soon as RabbitMQ receives the events from the publisher, it delivers them to the connected subscriber. The subscriber then triggers the handle function, which prints the confirmation message to the terminal console for each of the five events received. This demonstrates a successful asynchronous communication flow where the publisher and subscriber operate independently through a broker.
