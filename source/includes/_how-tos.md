# How to receive orbita notification
All the orbita notifications are sent through the exchange **orbita_exchange** of type [topic][1]. There are two main routing key in orbita one for receive notification and one for send command. To receive notification you should create a new queue with routing key of type `#.evt.#` see this exampel in ruby using [bunny][2] (There are a lot of client in other [languages][3]):

```ruby
connection = Bunny.new(amqp_opts)
connection.start
channel = connection.create_channel
orbita_exchange = channel.topic('orbita_exchange')
queue = channel.queue('my_app_queue', durable: true, auto_delete: false)
queue.bind(exchange, routing_key: '#.evt.#')
```

The first part of routing key is reserved for future use but the second part, after `evt`, should be specialized to receive only event from oauth, or from my_app. For example if you want receive all event from connect you should bind your queue in this way:

```ruby
connection = Bunny.new(amqp_opts)
connection.start
channel = connection.create_channel
orbita_exchange = channel.topic('orbita_exchange')
queue = channel.queue('my_app_queue', durable: true, auto_delete: false)
queue.bind(exchange, routing_key: '#.evt.oauth.#')
```

# How to send orbita command
Sending a command is very similar to receive an event. The application should publish a message on 'orbita_exchange' with the appropiate routing key. The routing key is composed by a first part that should be fixed `0.0.cmd.` and a second part that identify the _type_ of the message. For example you could send a generic notification tha cna be dispatched via WS and mobile sending a message of this type:

```json
message = {
  "id": "2cb56d59-ed41-4aad-9a7d-765fa89daa4b",
  "type": "GenericNotification",
  "source": "my_app",
  "emitted_at": "2016-09-22T16:24:13Z",
  "payload": {
    "from": 23,
    "to": 23,
    "link": "#",
    "text": "This is a generic notification"
  }
}
```
with routing key eqaul to `0.0.cmd.notification`. Publishing this message on `orbita_exchange` will bring up a notification on the bar of the user with id 23.

# Format of the message

The format of the events and the commands is very similar:

```json
{
  "id": "a75fbf6e-4c36-48fb-9051-33a2f9600e9a",
  "emited_at": "2016-09-22T16:08:15Z",
  "type": "GenericNotification",
  "source": "my_app",
  "payload": ...
}
```

- `id` is a classic uuid that can used to differentiate all the messages
- `emitted_at` is the time of the server in UTC iso8601 format. This field should be used to reorder messages sent form the same source. *This time can NOT be used to apply a gloabal order between messages*
- `type` is the type of teh message that can categorize the message.
- `payload` is something that the application should use to describe the event and/or the command

**Warning**: sometime some messages can contain other fields but it's cant be used directly by other aplication because can be removed or changed. In general only this fields should be considered always present and every fileds related to particular message should be put in the payload.


[1]: https://www.rabbitmq.com/tutorials/tutorial-five-python.html
[2]: http://rubybunny.info/
[3]: https://www.rabbitmq.com/clients.html
