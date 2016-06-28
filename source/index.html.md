---
title: orbita public documentation

language_tabs:
  - shell
  - ruby
  - javascript

toc_footers:
  - <a href='http://getorbita.io'>Follow orbita</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

# Orbita
This repository will be used to contain everything that needs to be shared between all the services/repositories, in particular

* This repository's wiki will hold the documentation about: API, domain concepts, shared data, etc...
* Setup process and project automation scripts
* Integration and smoke tests

This projects is based on microservices and exchange [domain events](DomainEvents) on a [event bus](EventBus).

The actual services are:

* OAuth service - The repository is [here](https://git.coders51.com/coders51/orbita-oauth/tree/master). It's **_write a little description here_**.
* Networking service - The repository is [here](https://git.coders51.com/coders51/orbita-networking/tree/master). This service maps the relationships between users and other entities. It responds to events like `"FriendshipRequested"` and similar events that may involve creating of, updating of and querying by relationships. In the case of a `"FriendshipRequested"` event, a `FriendRequest` between two users is saved in its data store. Later on, this `friend_request` may be accepted when this service receives `"FriendshipAccepted"` event that involves the same two users. By default, this runs in `localhost:4001` in development.
* Notification service - The repository is [here](https://git.coders51.com/coders51/orbita-notification/tree/master). It's **_write a little description here_**.

## Summary
- [General Architecture Details](General-Architecture-Details)
- [HTTP API Documentation](http-api)
- [Authentication and Authorization](Authentication-and-Authorization)
- [Orbita Components API](Orbita-Components-API)
- [Authentication JWT Format & Authentication API](jwt)
- [Domain Events](Domain-Events)
- [How To Run Local](how-to-run-local)
- [Seed Users](seed-users)
- [How to create new application](how-to-create-new-application)
- [Orbita Credentials](orbita-credentials)
- [How to store user data in Orbita Bucket service](how-to-make-requests-to-oauth-for-storing-user-data-in-bucket)

## Deployment and Server Configuration
- [How to Setup A New Orbita Instance](how-to-setup-orbita-instance)
- [Staging Environment And Deploy](Staging-Environment-And-Deploy)
- [Provisioning Vagrant](provisioning-vagrant)
- [Provisioning Staging Server](provisioning-staging)
- [How to Deploy Services](how-to-deploy-services)


# Dev Machine Setup

1. Install Ansible and Vagrant
2. Go this repo's root directory
3. `ansible-galaxy install -r requirements.yml` try `sudo` if the command file with permission error.
4. `vagrant up` - This will take a long time as it will provision your vagrant machine with all the dependencies of Orbita's services.
5. `vagrant ssh` - Logs into your Orbita dev machine
6. Run `./setup.sh` to initialize all the repositories (`orbita-networking`, `orbita-notifications`, and so on)
7. `foreman start` - Start all services

* Note that all the Orbita repositories must exist in your computer. They need to be located in the same directory as this Orbita repo.
* Make sure that the forwarded ports found in the Vagrantfile are open in your host computer. `vagrant up` will fail if those ports aren't available.
* If you want to start services separately, refer to the Vagrantfile to see how to start them.

# Authentication

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve
