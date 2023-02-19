# Celery

Celery is a distributed system to process vast amount of messages. In other words, it allows us to run python functions/tasks asyncronously. Celery needs to be connected to a message broker like (RMQ, redis,etc) and it then picks up tasks from the broker. Celery is similar to `asyncio` library where you connect to the message broker e.g. RabbitMQ, and it reads and sends messages to RMQ.

So, to send tasks to Celery:
* you first need to start up the Celery service
* Then use the attribute `.delay` on your task which tells Celery to forward this task to the distributed message queue sofware of choice, e.g. RMQ
* When this is done, you can see the results of the task in Celery, but if you want to see the result somehwere in your app, you have to use a backend. Celery is just a service that runs, and after the run, it forgets about what it did before. Therefore, if you want to see the results of the task and save their status, you need a backend to store these information. This could be sqlite or redis for instance.
* If we use a database as the backend like sqlite, it will use sqlalchemy to interact with the database
* After this, it stores the results in the backend in the form of a BLOB (Binary Large Object). But you can see that the results are there, and Celery can access the results if it needs it, and remembers what it has done in the past.

> BLOB stands for a “Binary Large Object,” a data type that stores binary data. Binary Large Objects (BLOBs) can be complex files like images or videos, unlike other data strings that only store letters and numbers. A BLOB will hold multimedia objects to add to a database; however, not all databases support BLOB storage.