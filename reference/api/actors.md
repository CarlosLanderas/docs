# Actors

Dapr provides native, cross-platform and cross-language virtual actor capabilities.
Besides the language specific Dapr SDKs, a developer can invoke an actor using the API endpoints below.

## Specifications for user service code calling to Dapr

### Invoke Actor Method

Invokes a method on an actor.

#### HTTP Request

```http
POST/GET/PUT/DELETE http://localhost:<daprPort>/v1.0/actors/<actorType>/<actorId>/method/<method>
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
daprPort | The Dapr port.
actorType | The actor type.
actorId | The actor ID.
method | The name of the method to invoke.

> Example of invoking a method on an actor:

```shell
curl -X POST http://localhost:3500/v1.0/actors/stormtrooper/50/method/shoot \
  -H "Content-Type: application/json"
```

> Example of invoking a method on an actor with a payload:

```shell
curl -X POST http://localhost:3500/v1.0/actors/x-wing/33/method/fly \
  -H "Content-Type: application/json"
  -d '{
        "destination": "Hoth"
      }'
```

> The response from the remote endpoint will be returned in the request body.

### Actor State Changes - Transaction

Persists the changed to the state for an actor as a multi-item transaction.

***Note that this operation is dependant on a state store that supports multi-item transactions.***

#### HTTP Request

`POST/PUT http://localhost:<daprPort>/v1.0/actors/<actorType>/<actorId>/state`

#### HTTP Response Codes

Code | Description
---- | -----------
201  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
daprPort | The Dapr port.
actorType | The actor type.
actorId | The actor ID.

```shell
curl -X POST http://localhost:3500/v1.0/actors/stormtrooper/50/state \
  -H "Content-Type: application/json"
  -d '[
       {
         "operation": "upsert",
         "request": {
           "key": "key1",
           "value": "myData"
         }
       },
       {
         "operation": "delete",
         "request": {
           "key": "key2"
         }
       }
      ]'
```

### Get Actor State

Gets the state for an actor using a specified key.

#### HTTP Request

```http
GET http://localhost:<daprPort>/v1.0/actors/<actorType>/<actorId>/state/<key>
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
daprPort | The Dapr port.
actorType | The actor type.
actorId | The actor ID.
key | The key for the state value.

```shell
curl http://localhost:3500/v1.0/actors/stormtrooper/50/state/location \
  -H "Content-Type: application/json"
```

> The above command returns the state:

```json
{
  "location": "Alderaan"
}
```

### Create Actor Reminder

Creates a persistent reminder for an actor.

#### HTTP Request

```http
POST,PUT http://localhost:<daprPort>/v1.0/actors/<actorType>/<actorId>/reminders/<name>
```

Body:

The following specifies a `dueTime` of 3 seconds and a period of 7 seconds.
```json
{
  "dueTime":"0h0m3s0ms",
  "period":"0h0m7s0ms"
}
```

A `dueTime` of 0 means to fire immediately.  The following body means to fire immediately, then every 9 seconds.
```json
{
  "dueTime":"0h0m0s0ms",
  "period":"0h0m9s0ms"
}
```

To configure the reminder to fire once only, the period should be set to empty string.  The following specifies a `dueTime` of 3 seconds with a period of empty string, which means the reminder will fire in 3 seconds and then never fire again. 
```json
{
  "dueTime":"0h0m3s0ms",
  "period":""
}
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
daprPort | The Dapr port.
actorType | The actor type.
actorId | The actor ID.
name | The name of the reminder to create.

```shell
curl http://localhost:3500/v1.0/actors/stormtrooper/50/reminders/checkRebels \
  -H "Content-Type: application/json"
-d '{
      "data": "someData",
      "dueTime": "1m",
      "period": "20s"
    }'
```

### Get Actor Reminder

Gets a reminder for an actor.

#### HTTP Request

```http
GET http://localhost:<daprPort>/v1.0/actors/<actorType>/<actorId>/reminders/<name>
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
daprPort | The Dapr port.
actorType | The actor type.
actorId | The actor ID.
name | The name of the reminder to get.

```shell
curl http://localhost:3500/v1.0/actors/stormtrooper/50/reminders/checkRebels \
  "Content-Type: application/json"
```

> The above command returns the reminder:

```json
{
  "dueTime": "1s",
  "period": "5s",
  "data": "0",
}
```

### Delete Actor Reminder

Deletes a reminder for an actor.

#### HTTP Request

```http
DELETE http://localhost:<daprPort>/v1.0/actors/<actorType>/<actorId>/reminders/<name>
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
daprPort | The Dapr port.
actorType | The actor type.
actorId | The actor ID.
name | The name of the reminder to delete.

```shell
curl http://localhost:3500/v1.0/actors/stormtrooper/50/reminders/checkRebels \
  -X "Content-Type: application/json"
```

### Create Actor Timer

Creates a timer for an actor.

#### HTTP Request

```http
POST,PUT http://localhost:<daprPort>/v1.0/actors/<actorType>/<actorId>/timers/<name>
```

Body:

The following specifies a `dueTime` of 3 seconds and a period of 7 seconds.
```json
{
  "dueTime":"0h0m3s0ms",
  "period":"0h0m7s0ms"
}
```

A `dueTime` of 0 means to fire immediately.  The following body means to fire immediately, then every 9 seconds.
```json
{
  "dueTime":"0h0m0s0ms",
  "period":"0h0m9s0ms"
}
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
daprPort | The Dapr port.
actorType | The actor type.
actorId | The actor ID.
name | The name of the timer to create.

```shell
curl http://localhost:3500/v1.0/actors/stormtrooper/50/timers/checkRebels \
    -H "Content-Type: application/json"
-d '{
      "data": "someData",
      "dueTime": "1m",
      "period": "20s",
      "callback": "myEventHandler"
    }'
```

### Delete Actor Timer

Deletes a timer for an actor.

#### HTTP Request

```http
DELETE http://localhost:<daprPort>/v1.0/actors/<actorType>/<actorId>/timers/<name>
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
daprPort | The Dapr port.
actorType | The actor type.
actorId | The actor ID.
name | The name of the timer to delete.

```shell
curl http://localhost:3500/v1.0/actors/stormtrooper/50/timers/checkRebels \
  -X "Content-Type: application/json"
```

## Specifications for Dapr calling to user service code

### Get Registered Actors

Gets the registered actors in Dapr.

#### HTTP Request

```http
GET http://localhost:<appPort>/dapr/config
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed

#### URL Parameters

Parameter | Description
--------- | -----------
appPort | The application port.

> Example of getting the registered actors:

```shell
curl -X GET http://localhost:3000/dapr/config \
  -H "Content-Type: application/json"
```

> The above command returns the config (all fields are optional):


Parameter | Description
----------|------------
entities  | The actor types this app supports.
actorIdleTimeout | Specifies how long to wait before deactivating an idle actor.  An actor is idle if no actor method calls and no reminders have fired on it.
actorScanInterval | A duration which specifies how often to scan for actors to deactivate idle actors.  Actors that have been idle longer than the actorIdleTimeout will be deactivated.
drainOngoingCallTimeout | A duration used when in the process of draining rebalanced actors.  This specifies how long to wait for the current active actor method to finish.  If there is no current actor method call, this is ignored.
drainRebalancedActors | A bool.  If true, Dapr will wait for `drainOngoingCallTimeout` to allow a current actor call to complete before trying to deactivate an actor.  If false, do not wait.

```json
{
  "entities":["actorType1", "actorType2"],
  "actorIdleTimeout": "1h",
  "actorScanInterval": "30s",
  "drainOngoingCallTimeout": "30s",
  "drainRebalancedActors": true
}
```

### Activate Actor

Activates an actor.

#### HTTP Request

```http
POST http://localhost:<appPort>/actors/<actorType>/<actorId>
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
appPort | The application port.
actorType | The actor type.
actorId | The actor ID.

> Example of activating an actor:

```shell
curl -X POST http://localhost:3000/actors/stormtrooper/50 \
  -H "Content-Type: application/json"
```

### Deactivate Actor

Deactivates an actor.

#### HTTP Request

```http
DELETE http://localhost:<appPort>/actors/<actorType>/<actorId>
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
appPort | The application port.
actorType | The actor type.
actorId | The actor ID.

> Example of deactivating an actor:

```shell
curl -X DELETE http://localhost:3000/actors/stormtrooper/50 \
  -H "Content-Type: application/json"
```

### Invoke Actor method

Invokes a method for an actor.

#### HTTP Request

```http
PUT http://localhost:<appPort>/actors/<actorType>/<actorId>/method/<methodName>
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
appPort | The application port.
actorType | The actor type.
actorId | The actor ID.
methodName | The name of the method to invoke.

> Example of invoking a method for an actor:

```shell
curl -X POST http://localhost:3000/actors/stormtrooper/50/method/performAction \
  -H "Content-Type: application/json"
```

### Invoke Reminder

Invokes a reminder for an actor.

#### HTTP Request

```http
PUT http://localhost:<appPort>/actors/<actorType>/<actorId>/method/remind/<reminderName>
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
appPort | The application port.
actorType | The actor type.
actorId | The actor ID.
reminderName | The name of the reminder to invoke.

> Example of invoking a reminder for an actor:

```shell
curl -X POST http://localhost:3000/actors/stormtrooper/50/method/remind/checkRebels \
  -H "Content-Type: application/json"
```

### Invoke Timer

Invokes a timer for an actor.

#### HTTP Request

```http
PUT http://localhost:<appPort>/actors/<actorType>/<actorId>/method/timer/<timerName>
```

#### HTTP Response Codes

Code | Description
---- | -----------
200  | Request successful
500  | Request failed
404  | Actor not found

#### URL Parameters

Parameter | Description
--------- | -----------
appPort | The application port.
actorType | The actor type.
actorId | The actor ID.
timerName | The name of the timer to invoke.

> Example of invoking a timer for an actor:

```shell
curl -X POST http://localhost:3000/actors/stormtrooper/50/method/timer/checkRebels \
  -H "Content-Type: application/json"
```

## Querying Actor State Externally

In order to promote visibility into the state of an actor and allow for complex scenarios such as state aggregation, Dapr saves actor state in external databases.

As such, it is possible to query for an actor state externally by composing the correct key or query.
The state namespace created by Dapr for actors is composed of the following items:

* Dapr ID - Represents the unique ID given to the Dapr application.
* Actor Type - Represents the type of the actor.
* Actor ID - Represents the unique ID of the actor instance for an actor type.
* Key - A key for the specific state value. An actor ID can hold multiple state keys.

The following example shows how to construct a key for the state of an actor instance under the `myapp` Dapr ID namespace:
`myapp-cat-hobbit-food`

In the example above, we are getting the value for the state key `food`, for the actor ID `hobbit` with an actor type of `cat`, under the Dapr ID namespace of `myapp`.
