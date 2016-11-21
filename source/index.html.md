---
title: orbita public documentation

language_tabs:
  - shell
  - ruby
  - javascript
  - html

toc_footers:
  - <a href='http://getorbita.io'>Follow orbita</a>
  - documentation version v.0.1
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - how-tos

search: true
---


# Orbita

## Summary
- [Introduction](/orbita-doc/#introduction)
- [General Architecture Details](/orbita-doc/#general-architecture-details)
- [Quick reference for a setup of a production server](/orbita-doc/#quick-reference-for-setup-of-a-production-server)
- [HTTP API Documentation](/orbita-doc/#http-api-documentation)
- [Authentication and Authorization](/orbita-doc/#authentication-amp-authorization)
- [Orbita Components API](/orbita-doc/#orbita-components-api)
- [Verify JWT](/orbita-doc/#verify-jwt-token)
- [Domain Events](/orbita-doc/#domain-events)
- [How to create new application and use all orbita services](/orbita-doc/#how-to-create-new-application-and-use-all-orbita-services)
- [How to store user data in Orbita Bucket service](/orbita-doc/#how-to-store-user-data-in-orbita-bucket-service)

## Deployment and Server Configuration
- [How to Setup A New Orbita Instance](/orbita-doc/#how-to-setup-a-new-orbita-instance)
- [Provisioning Vagrant](/orbita-doc/#vagrant-provisioning)
- [Provisioning Staging Server](/orbita-doc/#provisioning-staging-server)
- [How to Deploy Services](/orbita-doc/#deploying-orbita-services)
- [Dev Machine Setup](/orbita-doc/#dev-machine-setup)


# Introduction

orbita is a product useful to:

- centralize all your user base on a unique database
- analyze their behaviour (misure their action and how they interact with you)
- have web components to integrate in all your apps
- have a set of API for your web and mobile apps to speed up development
- have landing page to mix your marketing analysis with real registration to your platform
- distribute your brand connection with plugin
- create a network of your users with a graph db
- have realtime notification between your different application
- store big data associated to your users
- have webhook with your mailchimp list

Using orbita you can reduce your time to market and your time to break even

orbita is a product by [coders51](http://coders51.com)

* This repository's wiki will hold the documentation about: API, domain concepts, shared data, etc...
* Setup process and project automation scripts
* Integration and smoke tests

This projects is based on microservices and exchange [domain events](#domain-events) on a [event bus](event-bus).

The actual services are:

* OAuth service - It's the core of orbita. All the application and service use api from oauth. You can have a link/button in all your app: CONNECT WITH MY BRAND easy as you make "connect with facebook".
* Networking service - This service maps the relationships between users and other entities. It responds to events like `"FriendshipRequested"` and similar events that may involve creating of, updating of and querying by relationships. In the case of a `"FriendshipRequested"` event, a `FriendRequest` between two users is saved in its data store. Later on, this `friend_request` may be accepted when this service receives `"FriendshipAccepted"` event that involves the same two users. By default, this runs in `localhost:4001` in development.
* Notification service - A realtime service for every kind of notification you need (web/desktop/mobile)
* Wordpress plugin - give to your marketing manager the power to make landing page with visual composer and add a link/button to connect to your **real** user base
* Bucket data service - store complex data different by application and user and use it to profile action and specific data
* Dashboard analytic - profilation and marketing tool with mailchimp webhook

# General Architecture Details

orbita is a microservice collaboration platform.

![Image of Yaktocat](/images/orbitaarch.png)

Some info about this kind of architecture:

* [Event Collaboration](http://www.martinfowler.com/eaaDev/EventCollaboration.html)
* [Domain Event](http://www.martinfowler.com/eaaDev/DomainEvent.html)
* [Event Sourcing](http://www.martinfowler.com/eaaDev/EventSourcing.html)


#Quick reference for setup of a production server

To install orbita on an existing server you need the following environment on your production server:

- RabbitMQ v 3.6 (we suggest 3.6.2)
- PostgreSQL v 9.4>, if you're in an Amazon server we suggest PostgresSQL RDS with multi-zone deploy and backup of a week.
- nginx v 1.9.3> (if you prefer it could be ok also Apache)
- nodejs v LTS 4.4.4
- package manager ruby RVM (if your prefer it's ok also rbenv)
- unicorn for integration between ruby and nginx/apache
- supervisor to manage runtime process
- orientdb [this is not mandatory at the beginning, you can choose to install it in a second moment]

Components are in ruby on rails and elixir, for some component we need:

- Erlang v 18.3, and Elixir 1.2X we suggest esl-erlang downloadable from this link [https://www.erlang-solutions.com/resources/download.html](https://www.erlang-solutions.com/resources/download.html) and [http://elixir-lang.org/install.html#unix-and-unix-like](http://elixir-lang.org/install.html#unix-and-unix-like)


# HTTP API Documentation
The documentation is written in markdown following the [API Blueprint Specification](https://github.com/apiaryio/api-blueprint)


## Notifications API
Notification service

### Group User Notifications
All these requests requires a JWT token to identify the user


## Unread Notifications [/users/{ID}/notifications/unread]
All the unread notifications addressed to the identified user. The `JWT_TOKEN` is given by the OAuth server and contains encrypted informations about the user.

+ Parameters
  + ID: 42 (required, string) - OAuth user identifier


### Retrieve Unread Notifications [GET]
Retrieve all the unread notifications of the user

+ Request
  + Headers

            Accept: application/json
            Authorization: Bearer {JWT_TOKEN}

+ Response 200 (application/json)
  + Body

            [
              {
                "id": 12,
                "message": "Rick has accepted your friendship request",
                "from": "7",
                "to": "8",
                "require_input": false
              }
            ]

+ Response 401

### Mark Notifications As Read [DELETE]
Mark all the unread notifications of the user as read

+ Request
  + Headers

            Authorization: Bearer {JWT_TOKEN}

+ Response 202
+ Response 401


## Unread Notification [/notifications/unread/{ID}]
Unread notification. The `JWT_TOKEN` is given by the OAuth server and contains encrypted informations about the user.

+ Parameters
  + ID: 77 (required, string) - Notification identifier

### Mark Notification As Read [DELETE]
Mark single notification as read. The authenticated user must be the sender or the receiver of the notification

+ Request
  + Headers

            Authorization: Bearer {JWT_TOKEN}

+ Response 202
+ Response 401
+ Response 404


## Notification [/notifications/{ID}]
Notification. The `JWT_TOKEN` is given by the OAuth server and contains encrypted informations about the user.

+ Parameters
  + ID: 77 (required, string) - Notification identifier

### Retrieve Notification [GET]
**[NOT IMPLEMENTED YET]** Retrieve single notification. The authenticated user must be the sender or the receiver of the notification

+ Request
  + Headers

            Accept: application/json
            Authorization: Bearer {JWT_TOKEN}

+ Response 200 (application/json)
  + Body

            {
              "id": 12,
              "message": "Rick has accepted your friendship request",
              "from": "7",
              "to": "8",
              "read": true
            }

+ Response 401

### Delete Notification [DELETE]
**[NOT IMPLEMENTED YET]** Delete the single notification.

+ Request
  + Headers

            Authorization: Bearer {JWT_TOKEN}

+ Response 202
+ Response 401

### Enable notifications [POST /users/:user/notifications/by/:media/enabled]

Enables a given notification media (e.g., email or websockets) for a given user.

+ Request
  + Headers

            Authorization: Bearer {JWT_TOKEN}
            Accept: application/json

+ Response: 200

### Disable notifications [POST /users/:user/notifications/by/:media/disabled]

Disables a given notification media (e.g., email or websockets) for a given user.

+ Request
  + Headers

            Authorization: Bearer {JWT_TOKEN}
            Accept: application/json

+ Response: 200

### Read notifications settings for a media [GET /users/:user/notifications/by/:media]

Tells whether notifications on the given `:media` are enabled for the given `:user`.

+ Request
  + Headers

            Authorization: Bearer {JWT_TOKEN}
            Accept: application/json

+ Response: 200
  + Body

            {"enabled": true}

### Read notifications settings for all media [GET /users/:user/notifications/by/:media]

Tells whether notifications are enabled/disabled for each media for the given `:user`.

+ Request
  + Headers

            Authorization: Bearer {JWT_TOKEN}
            Accept: application/json

+ Response: 200
  + Body

            {
              "email": true,
              "ws": false,
              ...
            }

### Group WebSocket

## WebSocket Library [/js/app.js]
The library to use to establish the WebSocket connection. See [the official documentation](http://www.phoenixframework.org/docs/channels) on how to use it client side.

### Retrieve WebSocket Library [GET]

+ Response 200


## WebSocket Handshake [/socket/websocket?jwt_token={JWT_TOKEN}]
To establish a WebSocket connection, the client sends a WebSocket handshake request, for which the server returns a WebSocket handshake response. The `JWT_TOKEN` is given by the OAuth server and contains encrypted informations about the user.

+ Parameters
  + JWT_TOKEN: "eyJ0eXAiOiJKV..." (required, string) - Authentication token

### WebSocket Handshake [GET]

+ Request
  + Headers

            Upgrade: websocket
            Connection: Upgrade
            Cache-Control: no-cache

+ Response 101


## How to generate HTML
Using [aglio](https://github.com/danielgtaylor/aglio) an HTML file can be generate starting from a Blueprint file

```shell
> To install, use this code:

$ npm -g install aglio
```


``` shell
> HTML Generation

$ aglio -i http-api-mta-notifications.md -o http-api-mta-notifications.html
```

FORMAT: 1A

## Networking API
Networking service. The endpoints on this API expose resources that represent relationships across different users and things.

## Group Friend Requests
The friend request resource represents a request to connect from one user to another. A friendship between users can only be created when a friendship request has been accepted. There are five(5) endpoints for this resource..

## List of Friend Requests [/friend_requests]
A resource representing all of a user's friend requests.

### Fetch all Friend Requests [GET]
Fetches all friend requests sent and received by a user. The user is identified through the `Authorization` header provided.

+ Request
  + Headers

            Accept: application/json
            Authorization: Bearer {JWT_TOKEN}
+ Response 200 (application/json)
  + Body

            [
              {
                "oauth_id": "12",
                "received": true,
                "sent": true
              }
            ]


### Create a Friend Request [POST]
It expects a `to` param which should be an oauth_id of a user to whom the request is sent to. An `Authorization` header is required to identify the user who sent the request. You may only create one (1) friend request between two (2) users.

+ Parameters
  + to: "77" (required, string) - User ID of the user to whom the request is addressed to.

+ Request
  + Headers

            Accept: application/json
            Authorization: Bearer {JWT_TOKEN}
+ Response 200 (application/json)
  + Body

            [
              {
                "oauth_id": "12",
                "sent": true
              }
            ]


## List of Friend Requests [/friend_requests/{id}]
A resource representing all of a user's friend requests.

### Deletes a Friend Request [DELETE]
An `Authorization` header is required to identify the user who sent the request.

+ Parameters
  + id: "77" (required, string) - User ID of the user to whom the request is addressed to.

+ Request
  + Headers

            Accept: application/json
            Authorization: Bearer {JWT_TOKEN}
+ Response 200 (text/plain)
  + Body

            Ok

## Accept Friend Request [/friend_requests/{from}/accept]
For accepting a friend request.

### Accept a Friend Request [PUT]
Deleting a friend request and creates a friend resource in its place. The user accepting the friend request is identified through the `Authorization` header. You can only accept a friend request once.

+ Parameters
  + from: "77" (required, string) - User ID of the user who sent the request.

+ Request
  + Headers

            Accept: application/json
            Authorization: Bearer {JWT_TOKEN}
+ Response 200 (text/plain)
  + Body

            Ok


## Refuse Friend Request [/friend_requests/{from}/refuse]
For refusing a friend request.

### Refuse a Friend Request [DELETE]
The user accepting the friend request is identified through the `Authorization` header.

+ Parameters
  + from: "77" (required, string) - User ID of the user who sent the request.

+ Request
  + Headers

            Accept: application/json
            Authorization: Bearer {JWT_TOKEN}
+ Response 200 (text/plain)
  + Body

            Ok

## All Friends [/friends]
The friend resource represents a friendship between two (2) users. A friendship is only created when a friend request has been accepted by a user.

### Fetch all Friends [GET]
Get all friends of a user. The user is identified through the Authorization header provided.

+ Request
  + Headers

            Accept: application/json
            Authorization: Bearer {JWT_TOKEN}
+ Response 200 (text/plain)
  + Body

            [
              {
                "oauth_id": "12"
              }
            ]

## A friend [/friends/{id}]

### Delete Friendship [DELETE]
An id param is expected which identifies the current user's friend. The current user is identified through the Authorization header provided.

+ Parameters
  + id: "77" (required, string) - User ID of the user who is a friend of current user.

+ Request
  + Headers

            Accept: application/json
            Authorization: Bearer {JWT_TOKEN}
+ Response 200 (text/plain)
  + Body

            Ok

## All Friends for Services [/private/friends]
Friends resource meant to be accessed by other MTA services.

### Fetch all Friends [GET]
Get all friends of a user. The user is identified through the `id` query parameter.
`AUTH_CODE` is the Base64-encoded, sha256 hash of a secret key shared across services and the url of the endpoint.

+ Parameters
  + id: "77" (required, string) - User ID of the user who you want to fetch friends for.

+ Request
  + Headers

            Accept: application/json
            Authorization: X-C51 {JWT_TOKEN}
+ Response 200 (text/plain)
  + Body

            true/false


## Friendship Check for Users [/friends-with/{id}]

### Check if Two (2) Users are Connected [GET]
The current user is identified through the Authorization header provided.

+ Parameters
  + id: "77" (required, string) - User ID

+ Request
  + Headers

            Authorization: Bearer {AUTH_CODE}
+ Response 200 (text/plain)
  + Body

            true/false


## Friendship Check for Services [/private/friends-with]

### Check if Two (2) Users are Connected [GET]
`AUTH_CODE` is the Base64-encoded, sha256 hash of a secret key shared across services and the url of the endpoint.

+ Parameters
  + id: "77" (required, string) - User ID
  + friend_id: "78" (required, string) - User ID

+ Request
  + Headers

            Authorization: X-C51 {AUTH_CODE}
+ Response 200 (text/plain)
  + Body

            true/false

# Authentication & Authorization

## Introduction
All services share a secret key

## Scenario 1 - Authentication/Authorization between services

1. Service **A** calls Service **B** (example: `http://foo.orbita.com/api/v1/users`)

2. Service **A** takes Service **B** URL (`http://foo.orbita.com/api/v1/users`) and encrypt it with SHA-256 using the shared key, encode it in base64 and put the resulting token in an header (example: `Authorization: X-C51 xxxxxxxxxxxx`).

3. Service **B** receives the call, checks if the header is present and do the same process of point 2. Than Service **B** checks if both token are equal and only in this case it responds with the requested resource.

If you want to try it in your shell, given `$URL` as the URL of the resource that needs authentication and given `$SECRET` the shared key:
```shell
$ curl -vvv $URL -H 'Accept: application/json' -H "Authorization: X-C51 `echo -n $URL | openssl dgst -sha256 -hex -hmac $SECRET | cut -d ' ' -f 2`"
```


## Scenario 2 - Authentication/Authorization between apps and services


**Login Process**

1. Application **A** calls **orbita-oauth** requesting a standard *OAuth 2.0* login process.
2. **orbita-oauth** responds back with a **access token** (in jwt format) containing this data:

```javascript
    	  {
        	user: {
	          id: 'xxxxxxxxxxx',
	          email: 'xxxxxxxxxxx',
	          expiration_date: 'xxxxxxxxxxx',
	          date: 'xxxxxxxxxxx'
	        }
	      }
```
and a **refresh_token**.

3. If Application **A** is an internal Orbita app it creates a cookie to share both tokens with **orbita-components**.
4. If Application **A** find that a cookie is already present it simply calls **orbita-oauth** to verify if the token is still valid. If the token is not valid Application **A** uses the `refresh_token ` to request to **orbita-oauth** another valid token.


**Resource Request Process**

1. Application **A** calls Service **B** (example: `http://foo.orbita.com/api/v1/users`) and include the jwt token in the header `Authorization: Bearer xxxxxxxxxxxxxx`
2. Service **B** calls **orbita-oauth** and check if the token is valid. If it's valid it responds with the requested resource

## General Considerations


In general keep in mind that:

- services should be able to ask each other almost every kind of information (example: complete orbita users list)
- apps should be able to ask to services **only informations about the logged user** (example: his own friends list, his own notifications, etc)

# Orbita Components API

### Setup
1. Add to your html file next scripts:
  * `<script src="<%= Rails.application.secrets.cdn_url %>/orbita_components.js"></script>`
  * `<script src="https://cdn.polyfill.io/v2/polyfill.js?features=Intl.~locale.en"></script>`
  * `<script src="https://cdn.polyfill.io/v2/polyfill.js?features=Intl.~locale.it"></script>`
2. Add styles:
   * `<link href="<%= Rails.application.secrets.cdn_url %>/orbita_components.css" rel="stylesheet">`

### API

* `orbitaComponents.init` renders components with params.
* `orbitaComponents.showFlashMessage` shows a flash message with the following params.
  * `text`: the text of the messagge
  * `cssClass`: the css class applied to the message (`info`, `warning`, `danger`)
  * `duration`: the flash message display time in ms.

example: `orbitaComponents.showFlashMessage('lorem ipsum', 'info', 2500)`

### Example:

```javascript
<div id="orbita_toolbar_wrapper">
</div>

<div id="orbita_body">
</div>

<div id="orbita_footer">
</div>
orbitaComponents.init({
  toolbar: {
    wrapperId: 'orbita_toolbar_wrapper', links: [
      {
        url: 'https://foo.com',
        it: 'Foo IT',
        en: 'Foo EN'
      },{
        url: 'http://boo.com',
        it: 'Boo IT',
        en: 'Boo EN'
      }],
      logoImage: "<%=asset_url('white_logo.png') %>",
      logoWidth: "165px",
      logoHeight: "36px"
  },
  footer: {wrapperId: 'orbita_footer'}
});
```

### Different links in toolbar - you can provide function for customize links:
```javascript
orbitaComponents.init({
  toolbar: {
    wrapperId: 'orbita_toolbar_wrapper', links: [
      {
        url: 'https://foo.com',
        it: 'Foo IT',
        en: 'Foo EN'
      },{
        url: 'http://boo.com',
        it: 'Boo IT',
        en: 'Boo EN'
      }, {
        url: '/settings',
        it: 'Settings',
        en: 'Settings',
        authorization: function(currentUser){ return currentUser }
      }],
      logoImage: "<%=asset_url('white_logo.png') %>",
      logoWidth: "165px",
      logoHeight: "36px"
  }
});
```

### Example with Friend Button

```javascript
 <div>
   <p>Alex Err with id 2</p>
   <div id="button_connect"></div>
 </div>
 <script type="text/javascript" charset="utf-8">
   orbitaComponents.renderFriendshipButton({wrapperId: 'button_connect', userId: 2});
</script>
```

### Adding custom links to user menu in the toolbar
```javascript
orbitaComponents.init({
  toolbar: {
    userMenu: {
      links: [
        {
          url: 'http://www.facebook.com',
          it: 'Faccebooke',
          en: 'Facebook'
        }
      ]
    }
  }
})
```

- The above example adds a link under the user menu.


# Verify JWT Token
* URL: `http://OAUTH-DOMAIN/api/v1/jwt/verify`
* Type: `GET`
* Format: `json`
* PARAMS:
  * `jwt_token`(string)
* Return json:
    * `id`(uuid) - user id
    * `email`(string) - user email
    * `first_name`(string) - user first name
    * `last_name`(string) - user last name
* Statuses:
    * `401` - if token invalid
    * `200` - if token valid

## Metadata
Every domain event will have the same metadata
* `id` (`UUID`): an `UUID` generated by the service that emitted the event
* `type` (`string`): the type of the event
* `source` (`string`): the name of the service that emitted the event
* `emitted_at` (`datetime`): the time at which the event is emitted
* `correlation_id` (`string`): an id that can be used to create relationship between events (ex. If I group friendship events using the `correlation_id` I will expect to see groups of events like [`FriendshipRequested`, `FriendshipAccepted`] or [`FriendshipRequested`, `FriendshipRefused`])
* `payload` (`object`): event specific data

# Domain Events

## FriendshipRequested

Emitted on channel `orbita-networking`

Emitted from the orbita-networking service when a user requests friendship from another user
* `from` (`UUID`): the `OAUTH` id of the user that requested the friendship
* `to` (`UUID`): the `OAUTH` id of the target user

Example:
```javascript
{
  "id": "95494570-BA22-488E-AF9A-E272589ACEA2",
  "type": "FriendshipRequested",
  "source": "networking",
  "emitted_at": "2015-09-17T13:55:02.930Z",
  "correlation_id": "80F2564A-0B14-4731-BF6A-B7D3CAA61E48",
  "payload": {
    "from": "C3B499C8-7F7F-45D0-82D7-75BFFF7417FD",
    "to": "5B5A541F-2E16-4DAA-9AA5-710EDF09E6E4"
  }
}
```

## FriendshipAccepted

Emitted on channel `orbita-networking`

Emitted from the orbita-networking service when a friendship request has been accepted by a user.
* `from` (`UUID`): the `OAUTH` id of the user that requested the friendship
* `to` (`UUID`): the `OAUTH` id of the target user
* `request_id` : the id of the friend request

## FriendshipRefused

Emitted on channel `orbita-networking`

Emitted from the orbita-networking service when a friendship request has been refused by a user.
* `from` (`UUID`): the `OAUTH` id of the user that requested the friendship
* `to` (`UUID`): the `OAUTH` id of the target user
* `request_id` : the id of the friend request

## UserCreated

Emitted on channel `orbita-oauth`

* `id` (`UUID`): an `UUID` generated by the service that emitted the event
* `type` (`string`): the type of the event
* `source` (`string`): the name of the service that emitted the event
* `emitted_at` (`datetime`): the time at which the event is emitted
* `payload` -> user private profile fields

Example:
```javascript

{
  "id": "95494570-BA22-488E-AF9A-E272589ACEA2",
  "type": "UserCreated",
  "source": "orbita-oauth",
  "emitted_at": "2015-09-17T13:55:02.930Z",
  "payload": {
    user: {
      id: 1,
      email: 'example@mail.com',
      first_name: 'first',
      last_name: 'last'
    }
  }
}
```

## UserUpdated

Emitted on channel `orbita-oauth`

* `id` (`UUID`): an `UUID` generated by the service that emitted the event
* `type` (`string`): the type of the event
* `source` (`string`): the name of the service that emitted the event
* `emitted_at` (`datetime`): the time at which the event is emitted
* `payload` -> user private profile fields

Example:
```javascript

{
  "id": "95494570-BA22-488E-AF9A-E272589ACEA2",
  "type": "UserUpdated",
  "source": "orbita-oauth",
  "emitted_at": "2015-09-17T13:55:02.930Z",
  "payload": {
    user: {
      id: 1,
      email: 'example@mail.com',
      first_name: 'first',
      last_name: 'last'
    }
  }
}
```

## User language changed

Emitted on channel `orbita-oauth`

* `id` (`UUID`): an `UUID` generated by the service that emitted the event
* `type` (`string`): the type of the event
* `source` (`string`): the name of the service that emitted the event
* `emitted_at` (`datetime`): the time at which the event is emitted
* `payload` -> `{from: 'en', to: 'it', user_id: 1}`

Example:
```javascript

{
  "id": "95494570-BA22-488E-AF9A-E272589ACEA2",
  "type": "UserLanguageChanged",
  "source": "orbita-oauth",
  "emitted_at": "2015-09-17T13:55:02.930Z",
  "payload": {
    "user_id": 1,
    "from": "en",
    "to": "it"
  }
}
```

## UserDeleted

Emitted on channel `orbita-oauth`

* `id` (`UUID`): an `UUID` generated by the service that emitted the event
* `type` (`string`): the type of the event
* `source` (`string`): the name of the service that emitted the event
* `emitted_at` (`datetime`): the time at which the event is emitted
* `payload` -> `{user_id: 1}`

Example:
```javascript
{
  "id": "95494570-BA22-488E-AF9A-E272589ACEA2",
  "type": "UserDeleted",
  "source": "orbita-oauth",
  "emitted_at": "2015-09-17T13:55:02.930Z",
  "payload": {
    "user_id": 1,
  }
}
```

## UserLogout
* `id` (`UUID`): an `UUID` generated by the service that emitted the event
* `type` (`string`): the type of the event
* `source` (`string`): the name of the service that emitted the event
* `emitted_at` (`datetime`): the time at which the event is emitted
* `payload` -> `{user_id: 1}`

Example:
```javascript
{
  "id": "95494570-BA22-488E-AF9A-E272589ACEA2",
  "type": "UserDeleted",
  "source": "orbita-oauth",
  "emitted_at": "2015-09-17T13:55:02.930Z",
  "payload": {
    "user_id": 1,
  }
}
```

1. Oauth service must be run on `5100`
2. JWT-Test app must be run on `3000`
3. Network app must be run on `4001`
4. Components must be run (webpack) on `8080`

## Mta-Oauth create apps

* `rake db:seed`
* create via rails c an admin user (get a user from User.first and se is_admin to true)
* Open localhost:5100, login with admin user
* Go to admin section, and create first application, add callback urls. (mark `trusted` as true)

Example:

```javascript
  Redirect uri: "http://portal.mysite-staging.com/users/auth/oauth51/callback",
  Uid: "dc58bccd82602fa1c86bdff342e3c627d44dd642dabf2e9b645ea0e501291d14",
  Secret: "a19c7a77185df47182dbe241a9e02614465da2dfe8f06d7fe888645465312001"
```

Then go to your application (jwt_test, developers) and add this secret and id to secret.yml

# How to create new application and use all orbita services

### Create new rails app
* `rails new orbita_example && cd orbita_example`
* add to Gemfile auth gems:
 * `gem 'omniauth-oauth51', git: 'https://github.com/coders51/omniauth-oauth51.git'`
 * `gem 'oauth51-client', git: 'https://github.com/coders51/oauth51-client.git'`

* `bundle install`
* Create oauth application in oauth app.
 - Login  into `connect.mysite-staging.com as admin` and go to `Dashboard`
 - Navigate to `Application` and click `Add new`
 - Fill into: name `application name`, Redirect uri `http://my-site.mysite.com/users/auth/oauth51/callback`, Trusted `true`
 - Click `Save` button
 * Go to your application and open `config/secrets.yml` (also put to `.gitignore` this file)
 * Add your application secret code and uid to this file, example:
 *
 ```ruby
    oauth:
    client_id: 0e08f9a0d80790d1d945fabfb4a80987fb17a5ac0a423448f8dc318e141f0d0e
    client_secret: 657a4c196c50ec957804c87d3915ad5b1f0504def89682b5d50131f4e72df766
    app_url: http://localhost:5100
 ```

## How to compose your secret.yml file

- Rails.application.secrets.jwt_cookie - This is the name of the browser cookie where the JWT token will be stored. We use JWT for authentication. This must be the same for all services that will communicate with each other. For example, all orbita services in production must have the same cookie. The cookie used for orbita services in production must be different from the cookie used for orbita services in staging.

- Rails.application.secrets.cdn_url - This is the url for the components/cdn service. For example, orbita staging’s cdn service is at http://cdn-staging.getorbita.io while orbita production’s cdn service is at http://cdn.getorbita.io.

- Rails.application.secrets.amqp - We are expected to set 3 variables here which are vhost, username and password. Setting a virtual_host is a way for us to ensure that orbita services for staging do not mix up RabbitMQ messages with orbita services for production. We run one instance of RabbitMQ which is shared by multiple deployments. These details must be the same for services that will communicate with each other.

- Rails.application.secrets.oauth['client_id’] - In the oauth service, we’ll need to add the application. Once added, we’ll have oauth credentials for both client_id and client_secret. If I remember correctly, API key is client_id.

- Rails.application.secrets.oauth['client_secret’] - In the oauth service, we’ll need to add the application. Once added, we’ll have oauth credentials for both client_id and client_secret.

- Rails.application.secrets.oauth['app_url’] - This is the url for the oauth service. For example, in staging for orbita, it would be http://connect-staging.getorbita.io. For development, it would be something like http://localhost:5100. For production, it would be http://connect.getorbita.io.

### Login section
For login, you can use oauth provider, and implement it in your application. Let's do it:
 * Add to `devise.rb`:

```javascript
config.omniauth :oauth51, Rails.application.secrets.oauth['client_id'],   Rails.application.secrets.oauth['client_secret'], scope: 'public accounts points', client_options: {site: Rails.application.secrets.oauth['app_url']}

```
 * Create `omniauth_callbacks_controller.rb` in `app/controllers` folder:

```ruby
  class OmniauthCallbacksController < Devise::OmniauthCallbacksController

    COOKIES_KEY = :_jwt_token
    COOKIES_REFRESH_KEY = :_refresh_token

    def oauth51
      o51_authentication_token = request.env["omniauth.auth"].credentials.token
      o51_refresh_token = request.env["omniauth.auth"].credentials.refresh_token

      ### We need set token to cookies, then all apps can use it for login user.
      set_auth_cookies(o51_authentication_token, o51_refresh_token)
      client = Oauth51Client::Client.new(o51_authentication_token, Rails.application.secrets.oauth['app_url'])
      client.me # returns info about user
      User.create_from_o51(client.me)
    end

    private

    def set_auth_cookies(jwt_token, refresh_token)
      cookies[COOKIES_KEY] = {
        value: jwt_token,
        expires: 7.days.from_now,
        domain: :all
      }
      cookies[COOKIES_REFRESH_KEY] = {
        value: refresh_token,
        expires: 7.days.from_now,
        domain: :all
      }
    end
  end
```
* Login user if cookie present (include this concern where you want check and login user by cookies)

```ruby
  module JwtLogin
    extend ActiveSupport::Concern

  COOKIES_KEY = :_jwt_token
  COOKIES_REFRESH_KEY = :_refresh_token

  def login_with_jwt(tries=0)
    return delete_cookies if tries > 1
    jwt_token = cookies[COOKIES_KEY]
    url = Rails.application.secrets.oauth['app_url'] + "/api/v1/users/me"
    begin
      user_info = RestClient.get(url, {'Authorization' => "Bearer #{jwt_token}"})
    rescue => e
      if e.to_s.match(/401/)
        return unless cookies[COOKIES_REFRESH_KEY]
        refresh_token(cookies[COOKIES_REFRESH_KEY])
        login_with_jwt(tries + 1)
      end
      return nil
    end
    User.create_from_jwt_token(JSON.parse(user_info))
  end

  def refresh_token(token)
    params = {
      grant_type: "refresh_token",
      client_id: Rails.application.secrets.oauth["client_id"],
      client_secret: Rails.application.secrets.oauth["client_secret"],
      refresh_token: token
    }
    begin
      token_data = RestClient.post(Rails.application.secrets.oauth["app_url"] + '/oauth/token', params)
    rescue => e
      return nil
    end
    update_tokens(JSON.parse(token_data))
  end

  def update_tokens(tokens)
    cookies[COOKIES_KEY] = {
      value: tokens["access_token"],
      expires: 7.days.from_now,
      domain: :all
    }
    cookies[COOKIES_REFRESH_KEY] = {
      value: tokens["refresh_token"],
      expires: 7.days.from_now,
      domain: :all
    }
  end

  def delete_cookies
    cookies.delete(COOKIES_KEY, domain: :all)
    cookies.delete(COOKIES_REFRESH_KEY, domain: :all)
  end
end
```

* Check if user token valid: Make request to oauth app `/api/v1/users/me` with HEADERS: `Authorization: "Bearer #{token}"`

## Now Our application can login by token, and go to all applications as logined user.
Next step - add some components to our application. Lets add `toolbar` with notifications:
 * Add to config/secrets.yml cdn url: `cdn_url: http://cdn.mysite-staging.com`
 * Add to your layout next code:

```html
<head>
  <script src="<%= Rails.application.secrets.cdn_url %>/orbita_components.js"></script>
  <script src="https://cdn.polyfill.io/v2/polyfill.js?features=Intl.~locale.en"></script>
  <script src="https://cdn.polyfill.io/v2/polyfill.js?features=Intl.~locale.it"></script>
  <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css" rel="stylesheet">
  <link href="<%= Rails.application.secrets.cdn_url %>/orbita_components.css" rel="stylesheet">
</header>
<body>
<div id="orbita_toolbar_wrapper">
</div>

<div id="orbita_body">
  Here you can add your code..........
</div>

<div id="orbita_footer">
</div>
  <script type="text/javascript" charset="utf-8">
    var store = orbitaComponents.configureStoreFunction(window.Reducers);
    //You can use store in your own redux application if you use react, or ignore it.
    orbitaComponents.renderToolbar({wrapperId: 'orbita_toolbar_wrapper', store: store, links: [{
      url: 'https://www.mysite.com/inventory/users/sign_in',
      it: 'Inventario',
      en: 'Inventory'
    },{
      url: 'http://www.mysite.com',
      it: 'Magazine',
      en: 'Magazine'
    }]});
    orbitaComponents.renderFooter({wrapperId: 'orbita_footer', store: store});
  </script>
</body>
```

**It's all, enjoy!**



# How to Store User Data in Orbita Bucket Service

We can store additional Orbita-specific user data in the Orbita Bucket service. To do this, we will need to send a request to Orbita Oauth which exposes the `http://connect.getorbita.io/api/v1/users/1/bucket` endpoint. It works for the following methods:

1. POST - replaces the user data stored in the bucket with the data passed along with the request. It expects a JSON-format body with a `data` property like `{"data": {"foo": "bar"}}`. Making a POST request to `http://connect.getorbita.io/api/v1/users/1/bucket` with the above payload, will create a record with the following details `{user_id: 1, data: {foo: "bar"}}`.

2. PUT - merges the `data` payload with the data already stored for a specific user. If no previous user data exists, a new record will be created for that user. It expects a JSON-format body with a `data` property like `{"data": {"foo": "bar"}}`. If a user `{user_id: 1, {foo: "bar"}}` exists, and we do a PUT request to `http://connect.getorbita.io/api/v1/users/1/bucket` with payload of `{"data": {"bar": "baz"}`, the updated record will be `{user_id: 1, {foo: "bar", bar: "baz"}}` where `data` property is merged with the `data` property of the payload.

3. GET - this gets a user record given a user `id`. Just send a GET request to `http://connect.getorbita.io/api/v1/users/1/bucket`.

4. DELETE - deletes a user record given a user `id`. Just send a DELETE request to `http://connect.getorbita.io/api/v1/users/1/bucket`


When making the requests, keep in mind that an access token is required. You will need to get an access token from oauth or oauth-staging. Below are some sample access tokens I use for testing that have expiration times set in the far future.

- `http://connect.getorbita.io/api/v1/users/1/bucket?access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjp7ImlkIjoyLCJlbWFpbCI6ImdqYWxkb244NUBnbWFpbC5jb20iLCJmaXJzdF9uYW1lIjoiIiwiZGF0ZSI6IjIwMTYtMDMtMjJUMDY6NTk6NTYuODA1KzAxOjAwIn19.9dgb2dvq4Qmya13PANLHLxWRMAnXF2ihNcrUThpOCqE`
- `http://connect-staging.getorbita.io/api/v1/users/1/bucket?access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjp7ImlkIjoyLCJlbWFpbCI6ImdqYWxkb244NUBnbWFpbC5jb20iLCJmaXJzdF9uYW1lIjpudWxsLCJkYXRlIjoiMjAxNi0wMy0yMVQxMTo0OToxOC44NzcrMDE6MDAifX0.OdBAm883FqlHyBOob9t4TEk2bDLgNhJVIm6zDE5oxxs`


# How to Setup a New Orbita Instance

## Configuration

We will need to edit the [config.exs](https://git.coders51.com/coders51/orbita/blob/master/config.exs) file found in the root directory of the `orbita` repo. A `get_configs/1` function is expected for the instance you want to setup. The `get_configs/1` function should return a keyword list with certain options. If you want to generate configs for a `"foo"` instance, we define the following `get_configs/1` function:

```ruby
  def get_configs("foo") do
    [apps: @apps, # a list of apps you want to generate configs for. refer to @apps to see all available options
     ports: %{
       "networking" => "4030",
       "notifications" => "4130",
       "cookietrack" => "9040"}, # a mapping of ports for certain apps
     domain: "foo.com", # the parent domain for the services
     namespace: "foo", # the namespace we use to refer to this instance. typically the same as the argument expected by  the function
     server: "deploy@orbita.lan.coders51.com", # the server we will deploy to
     env: "production"] # either "staging" or "production" environment
  end
```

## Generation of Configurations

From the command line, go to the root of the `orbita` directory. From there, we run the following:

```shell
$ ./gen_configs -n foo
```

The above command generates capistrano, nginx, supervisor, unicorn, thin, secrets.yml and vm.args configs for each of the apps depending on the templates that exist for them. You can check the templates available for each app under `./templates` directory in the orbita repo.

## First Deployment of Each Service/App

Before deploying, go to the repo of either Networking, Notifications or Oauth and then run the ff:
```shell
$ bundle exec cap foo_production setup:amqp
```

The above command creates the rabbitmq vhost for the instance and a corresponding user with admin-like privileges. This is an important step so that the services can actually communicate with each other and is required so that you can deploy multiple orbita instances on one server.

### All services except Dashboard and Components/CDN are deployed this way:

Make sure you are in the root directory of the service we want to deploy.
```shell
$ git commit -am "foo_production deploy configs"
$ bundle exec cap foo_production deploy:initial # if env is "staging" then it should be foo_staging
```

Note that we `deploy:initial`. This is different from the capistrano `deploy` task we will use in succeeding deploys since it includes some setup tasks such as uploading of nginx and supervisor configs.

### For Dashboard, we do the ff:

Before deploying, you will need to add the necessary configs for your deployment to `config/environment.js`. Commit and push that to the remote host before running the below commands.

```shell
$ ./setup_deploy foo_dashboard_production deploy@server.com  # the first arg is the name of the dashboard app which is the namespace + dashboard + env
$ ember deploy foo_dashboard_production
```

### For Components/CDN, we do:
```shell
$ git commit -am "foo_production deploy configs"
$ bundle exec cap foo_production copy:initial
```

### Important Notes

- You can check the services that are running in the server with `sudo supervisorctl status`. Watch out for services that keep on restarting. You will need to look into the logs for those services to find out why they stop.
- When building releases for the elixir apps(this happens every time you deploy), you may encounter errors that have to do with `:idna` or some other dependencies failing to compile. Some other Elixir libraries require more memory to compile and will fail in compiling in the server. If building the release locally works fine with the same configs, then you likely need to increase the memory for your server.
- Some apps such as Demo and Dashboard, require oauth configuration. This means you will need to add the Application to the Connect service. Once added there and set to `trusted`, copy their `client_secret` and `client_id` to your instance's Demo secrets.yml file which would be `foo_demo_production.secrets.yml`. For Dashboard, you just need the `client_id` copied to `config/environment.js`under `Env.torii.providers.oauth51.apiKey`.


## Staging Deploy

For every application just run `cap staging deploy`

## Staging Remote URLs

* http://connect.mysite-staging.com -> It's the ouath server -> mta-oauth
* http://portal.mysite-staging.com -> It's the portal -> mta-test-jwt
* http://developers.mysite-staging.com -> It's the site for the developers -> mta-developers
* http://networking.mysite-staging.com -> It's the networking service -> mta-networking
* http://notification.mysite-staging.com -> It's the notification server -> mta-notification
* http://cdn.mysite-staging.com -> We use for distribute the js compoments file -> mta-components

# Vagrant Provisioning

1. Install Ansible and Vagrant
2. Clone this repository and go to its root directory
3. `ansible-galaxy install -r requirements.yml` try `sudo` if the command fails with permission error.
4. `vagrant up` - This will take a long time as it will provision your vagrant machine with all the dependencies of Orbita's services.
5. `vagrant ssh` - Logs into your Orbita dev machine
6. `orbita-components` folder is shared between the local machine and the vagrant vm with rsync. The folder is synced **only** when vagrant vm is initialized. If you want a live rsync (necessary if you develop orbita-components) you need to execute also `vagrant rsync-auto`
7. Run `./setup.sh` to initialize all the repositories (`orbita-networking`, `orbita-notifications`, and so on)
8. `foreman start` - Start all services

* Note that all the Orbita repositories must exist in your computer. They need to be located in the same directory as this Orbita repo.
* Make sure that the forwarded ports found in the Vagrantfile are open in your host computer. `vagrant up` will fail if those ports aren't available.
* If you want to start services separately, refer to the Procfile to see how to start them.
* If `vagrant up` fails, you can run provisioning again by running `vagrant provision`.

# Provisioning Staging Server

1. Install Ansible in your dev machine
2. Set staging host in `/etc/ansible/hosts` by adding the following to that file:
    ```ruby
    [staging]
    deploy@orbita.lan.coders51.com
    ```
3. Git clone the https://git.coders51.com/coders51/orbita repository
4. From the root of that repository, run `ansible-playbook provision/staging.yml`
5. If you encounter any bugs, report them by creating an issue at https://git.coders51.com/coders51/orbita/issues

# Deploying Orbita Services

## Deploying Services Except Dashboard

$ bundle exec cap foo_production deploy

```shell
$ bundle exec cap foo_production deploy
```

## Deploying Dashboard

$ ember deploy foo_dashboard_production

```shell
$ ember deploy foo_dashboard_production
```


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
