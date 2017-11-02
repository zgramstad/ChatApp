# CURRENTLY UNDER CONSTRUCTION. NOT FULLY UPDATED TO REPRESENT CHANGES.

## ChatAPP Documentation for Group B
Hi! Welcome to our documentation page. All the information and resources you need to understand and implement our API will be found on this page.


# Table of Contents
* [**Understanding Our API**](https://github.com/zgramstad/ChatApp/blob/master/README.md#understanding-our-api-definitions)
* [**Quick Start Guide**](https://github.com/zgramstad/ChatApp/blob/master/README.md#quick-start-guide)
* [**FAQs**](https://github.com/zgramstad/ChatApp/blob/master/README.md#faqs)
* [**Possible Changes and Updates**](https://github.com/zgramstad/ChatApp/blob/master/README.md#possible-changes-and-updates)

# Understanding Our API (Definitions)



<!--# Objects

# The Stub

# Use Cases-->


### ChatRoom

One of the benefits of our current implementation is that you can define a chatroom and store however you see fit. At the very least you need these fields:

`ChatRoom {String: RoomID, IStub[]: Occupants}`

The RoomID is unique, and we think it's a good idea to use the timestamp of the room's creation. Everything else is up to you (we would also suggest a name). What it might be nice to do is to define methods for your ChatRoom that use our API (e.g.`sendMessage(Message)` could do the looping through the stubs for you; `inviteUser(userStub)` could just pass in the RoomID of the room, get your own remote stub from its closure, and call `userStub.receiveInvite(remoteSelfStub, roomID)`; there are other possible use cases)

### Invite

An invite is just a RoomId of the room the invite was sent from and the stub of the user who sent it.
`Invite {String: roomId, IStub: inviterStub}`

You probably want to be able to view and respond to multiple invites simultaneously (not having a newer one overwrite an older one), so it's probably a good idea to have some sort of list to store all your invites. It also might be nice to encapsulate methods like accepting and declining in your invite object (if you define it as an object).

### User
This is an implicit data type, but it would probably be nice to define one. (We might even define one later). The user could have fields such as its name to be prepended to messages, a list of pending invites, and its current chatrooms. All of this could just sit in the model. However, no one wants that (**DECOUPLE EVERYTHING!!!**).

### IStub

This is currently how we perform any interactions from one user's perspective to another. It is composed of all methods needed to interact between users. 

* `Map<String, String> sendRoomNames()`
* `IStub[] sendOccupants(String : roomID)`
* `Integer addUserToRoom(IStub : userStub, String: roomID)`
* `Integer receiveInvite(IStub : userStub, String : roomID)`
* `Integer removeUserFromRoom(IStub : userStub, String : roomID)`
* `Integer receiveMessage(Class<T> : classType, DataPacket<T, S> : data, String : roomID)`

### IMessage

An `IMessage` is what is sent inside a `DataPacket`. Processing messages is via the visitor design pattern. A message is the host defined as having a `type` (the type of the data), `data` (what to display), a `name` (the display name of the sender) and a `process()` method (how to display it).

`receiveMessage()` has various message types it can handle. If the message is of known type, then the system uses its own protocol to display it. However, if a message is received that contains a type unknown to the system, `receiveMessage()` calls the message's own `process()` method.




# Quick Start Guide

This guide should step you through how to implement the basic functionality of a chat application. You are free to implement your chat app differently, but this is what we think works best given the methods we defined for the API.

* **Retrieve Rooms from A User**
* **Connect to Room**
* **Invite a User To a Room**
* **Accept an Invite**
* **Decline an Invite**
* **Send a Message to a Single Person**
* **Send a Message to a ChatRoom**
* **Create a Room**

## Retrieve Rooms

### Context (What you need prior)
Nothing

### Steps
1. Connect to the IP: `ConnectToIP(String ipAddress)`
1. `remoteUserStub` = registry look up at ipAddress and retrieval of remote stub:![Image](https://i.imgur.com/gqjB4Er.png)
1. Get Rooms from `remoteUserStub`: `remoteUserStub.sendRooms()`
1. Store `remoteUserRooms` as a **Map\<RoomID, RoomName\>** to display to view, and use to connect to a room

*Steps 1 and 2 are currently no different than the last homework.*

## Connect to Room
### Context (What you need prior)
* `remoteUserStub` - the stub of the remoteUser whose room you want to connect to
* `roomId` - the roomId of the room you want to connect to
* `remoteSelfStub` - your own remote stub to be sent

### Steps
1. Get Occupants from `remoteUserStub` and `roomId`: `userStubs = remoteUserStub.sendOccupants(roomId)`
1. Foreach `stub` in `userStubs`
	1. Add `remoteSelfStub` to everyone else’s rooms: `stub.addUserToRoom(RemoteSelfStub, RoomId)`
1. *Add user to my room if add was successful (could this cause problems with a mismatch in separate local chat rooms?)*	


## Invite a User To a Room

### Context (What you need prior)
* `remoteUserStub` - the stub of the user you want to invite
* `roomId` - the id of the room you want to invite them to
* `remoteSelfStub` - your own remote stub to be sent

### Steps
1. Receive the invite on their stub: `remoteUserStub.receiveInvite(remoteSelfStub, roomId)`
1. Hope they accept

*Sending the remoteSelfStub (your own remote stub) allows the user to connect to the room via the user.*

## Accept an Invite
### Context (What you need prior)
* `remoteUserStub` - the stub of the remoteUser whose room you want to connect to. *Stored in the invite that was received.*
* `roomId` - the roomId of the room you want to connect to. *Stored in the invite that was received previously*
* `remoteSelfStub` - your own remote stub to be sent

### Steps
1. Get Occupants from `remoteUserStub` and `roomId`: `userStubs = remoteUserStub.sendOccupants(roomId)`
1. Foreach `stub` in `userStubs` + `remoteUserStub`
	1. Add `remoteSelfStub` to everyone else’s rooms: `stub.addUserToRoom(RemoteSelfStub, RoomId)`
1. *Add user to my room if add was successful (could this cause problems with a mismatch in separate local chat rooms?)*

*Notice Anything Familiar???* ***We are able to reuse the same API functions from connect.***


## Decline an Invite
### Context (What you need prior)
* `remoteUserStub` - the stub of the remoteUser whose room you want to connect to. *Stored in the invite that was received.*
* `roomId` - the roomId of the room you want to connect to. *Stored in the invite that was received previously*
* `remoteSelfStub` - your own remote stub to be sent

### Steps
1. Send a message to the person that you have declined the message: `remoteUserStub.receiveMessage(Class<String>, DataPacket("User has declined your invite"), roomId)`
1. Delete the invite from local storage.



## Send A Message To a Single Person
### Context (What you need prior)
* `remoteUserStub` - the stub of the remoteUser who you want to receive your message
* `roomId` - the roomId of the room you want to send the message to.

### Steps
1. Cause the other person to receive the message: `remoteUserStub.receiveMessage(Class<T>, DataPacket<T>(Message of type T), roomId)`

*You will notice that our API only allows sending a message to a single person. This allows private messages (like rejecting an invite) even within a group.*

## Send a Message to a Group
### Context (What you need prior)
* `remoteUserStubsArray` - the list of stubs that you want to send a message to
* `roomId` - the roomId of the room you want to send the message to.

### Steps
1. Send a message to each person in `remoteUserStubsList`.
 
i.e For each `remoteUserStub` in `remoteUserStubsList`, do: `remoteUserStub.receiveMessage(Class<T>, DataPacket<T>(Message of type T), roomId)`

*You will notice that sending a message to a group requires no new methods as compared to sending a message to an individual. This is because, in a peer-to-peer architecture, sending a message to a group of people is just a repeated action of sending a message to an individual person. Since a chatroom is defined as a roomId and list of user stubs, sending a message to an entire chatroom is just looping through its remote stubs.*


## Create Room

That’s local. Do it yourself.


# FAQs

Will be updated upon feedback. Feel free to create a pull request to add your own!

# Possible Changes and Updates

### 1. Division of Stubs
Depends on the scalability and ease-of-use of the current ChatRoom paradigm, we may upgrade to room stubs in subsequent iterations of the API design as such:

### User
* `Map<String, String> sendRoomNames()` 
* `Integer receiveInvite(IStub : remoteSelfStub, String : roomID)`
* `Integer receiveMessage(Class<T> : classType, DataPacket<T, S> : data, String : roomID)`

### Room
* `IStub[] sendOccupants()`
* `Integer addUserToRoom(IStub : userStub)`
* `Integer removeUserFromRoom(IStub : userStub)`

### 2. Perspective of Method Calls
Consider this semantic: to possess a friend's mobile number (or facebook we-are-friends status) is to possess a point-of-contact with her/him. In our API, to possess a friend's stub achieves the same effect. Calling methods on a stub is to ''''cause'''' a friend to do something for you. For instance, `friendStub.receiveMessage` causes friend to receive a message. `friendStub.sendRooms` causes friend to send his cache of chat rooms. This is exactly what we set out to do with our API.

However, some might say that it is more intuitive to have the methods named from the perspective of the user. If it improves net programmer happiness, we could potentially change the name `sendOccupants` to `getOccupants`, `receiveInvite` to `sendInvite`, etc. We care about our clients :)

### 3. Do we really need `connectToIP`?
Connecting to an IP is a critical part of the interaction between users, but it is currently a combination of just the standard RMIServer 2 line code (to connect to an IP and get the stub) and the `sendRoomNames()` method on the `remoteUserStub` that is returned. We can safely assume that it is implemented elsewhere.

### 4. Predefined vs. Freedom to Define
Should we pre-define the needed objects such as Invite, ChatRoom, User, etc. that people should use? This would encapsulate a lot of the methods that are needed, meaning that it would be more clear what each thing should do. However, it would mean that we would be restricting people to implement things the way we see them, and it would mean that our API would not necessarily be clean, orthogonal, and minimal. Or we could define interfaces and allow the users of our API to define the exact implementation. We understand and appreciate the fact that different programmers have vastly different coding habits. We take it upon ourselves to maximize the programmmer's creative space, while ensuring that, collectively, we can all get the job done.

### 5. Concurrency
It is probably a good idea to use objects from the `java.util.concurrency` package, so the rare situations like receiving multiple simultaneous invites or messages are handled. However one might argue that, in peer-to-peer networks with local copies of the same data shared among everyone, maintaining concurrency is a sparse problem to consider. For scalability sake, it deserves attention in future iterations.
