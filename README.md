## ChatAPP Documentation for Group B
Hi! Welcome to our documentation page. All the information and resources you need to understand and implement our API will be found on this page.


### 

<!--# Understanding Our API

# Objects

# The Stub

# Use Cases-->



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
1. Get Occupants from `remoteUserStub` and `roomId`: `userStubs = remoteUserStub.getOccupants(roomId)`
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
1. Get Occupants from `remoteUserStub` and `roomId`: `userStubs = remoteUserStub.getOccupants(roomId)`
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

##Send a Message to a Group
### Context (What you need prior)
* `remoteUserStubsArray` - the list of stubs that you want to send a message to
* `roomId` - the roomId of the room you want to send the message to.

### Steps
1. Send a message to each person in `remoteUserStubsList`.
 
i.e For each `remoteUserStub` in `remoteUserStubsList`, do: `remoteUserStub.receiveMessage(Class<T>, DataPacket<T>(Message of type T), roomId)`

*You will notice that sending a message to a group requires no new methods as compared to sending a message to an individual. This is because, in a peer-to-peer architecture, sending a message to a group of people is just a repeated action of sending a message to an individual person. Since a chatroom is defined as a roomId and list of user stubs, sending a message to an entire chatroom is just looping through its remote stubs.*


##Create Room

That’s local. Do it yourself.

# Current Data Types

### ChatRoom

One of the benefits of our current implementation is that you can define a chatroom and store however you see fit. At the very least you need these fields:

`ChatRoom {String: RoomID, IStub[]: Occupants}`

Everything else is up to you. What it might be nice to do is to define methods for your ChatRoom that use our API (e.g.`sendMessage(Message)` could do the looping through the stubs for you; `inviteUser(userStub)` could just pass in the RoomID of the room, get your own remote stub from its closure, and call `userStub.receiveInvite(remoteSelfStub, roomID)`; there are other possible use cases)

### Invite

An invite is just a RoomId of the room the invite was sent from and the stub of the user who sent it.
`Invite {String: roomId, IStub: inviterStub}`

You probably want to be able to view and respond to multiple invites simultaneously (not having a newer one overwrite an older one), so it's probably a good idea to have some sort of list to store all your invites. It also might be nice to encapsulate methods like accepting and declining in your invite object (if you define it as an object).

### User
This is an implicit data type, but it would probably be nice to define one. (We might even define one later). The user could have fields such as its name to be prepended to messages, pending invites, and its current chatrooms. All of this could just sit in the model. However, no one wants that (**DECOUPLE EVERYTHING!!!**).

### IStub

This is currently how we perform any interactions from one user's perspective to another. It is composed of all methods needed to interact between users. 

# FAQs

Will be updated upon feedback.

# Possible Changes and Updates

### 1. Division of Stubs
There seems to be a sort of division falling out of our API methods. We might be able to split things up this way. Maybe not now since it would take some refactoring, but on the revision. It might make sense to have room stubs for each of intuition and reduction of parameters:

### User
* `Map<String, String> sendRoomNames()` 
* `Integer receiveInvite(IStub : remoteSelfStub, String : roomID)`
* `Integer receiveMessage(Class<T> : classType, DataPacket<T, S> : data, String : roomID)`

### Room
* `IStub[] sendOccupants()`
* `Integer addUserToRoom(IStub : userStub)`
* `Integer removeUserFromRoom(IStub : userStub)`


### 2. Perspective of Method Calls
Currently all method names are from the perspective of the stub. However, it is definitely more intuitive to have the method names be from the perspective of the one calling the methods, so that could change (`sendOccupants` -> `getOccupants`, `receiveInvite` -> `sendInvite`). However, we all have IDEs with autocomplete, so it probably wouldn't be to difficult either way.
