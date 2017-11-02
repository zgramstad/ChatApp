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

### User
A User is simultaneously in two different states: outside of a room and inside of a chatroom.

Outside of a room: Each user has one version of themselves that exists completely decoupled from any specific room. This view is how others connect to the user (see what rooms the user is in and send the user invites). This means that the user has a set of their own presences in each room and a list of pending invites.

Inside of a room: A user has a unique "presence" for every room they are in. This presence allows others to communicate with them within a given room. This presence is also what a user sends to others to allow them to join them in a specific room. 


### Chat Room

A Chat Room is always defined from a user's perspective. It is just the set of users that a user sends a message to. It has a name and it has other users.

### Invite

An invite is just a gateway inside a room. Therefore, sending an invite is just sending your internal perspective of the room.

# Interfaces

### *IConnect*

This is how we represent a user outside (or decoupled from) a room. Every user only need one of these. Use it to initiate a connection with a user.

**Fields**

* `Set<ICommunicate> roomsContainingUser`
* `ConcurArrayList<ICommunicate> pendingInvites`

**Methods**

* `Set<ICommunicate> getRooms()`
* `Integer receiveInviteFrom(ICommunicate: sender)`

### *ICommunicate*

This is how we represent interactions within a room. It can be thought of as a user's perspective of a given room. Users have a unique ICommunicate per room they are apart of.

**Fields**

* `String UserName`
* `String RoomName`
* `Set<ICommunicate> peerStubs`
* `ILocalDisplayAdapter displayAdapter`
* `ConcurrentMap<ICommunicate, DataPacket> pendingMessages`
* `ConcurrentMap<Class<T>, ADataPacketAlgoCmd> commands*`

\* reference to a single command mapping for the user


**Methods**

* `Set<ICommunicate> : getPeers()`
* `Integer : add(ICommunicate: userToAdd)`
* `Integer : remove(ICommunicate: userToRemove)`
* `Integer : receiveMessageFrom(ICommunicate : sender, DataPacket<T> :  message)`
* `ADataPacketAlgoCmd : getCmd(Class<T> :  classType)`

### *ILocalDisplayAdapter*

This is nothing more than a direct connection to an MVC. In order to have non-routing architecture, each *ICommunicate* is coupled with an *ILocalDisplayAdapter* that pushes Components straight to the View. Thus a remote user can send across a *Factory* that produces a component and the ILocalDiplayAdapter can append it to the view.

**Fields**

None


**Methods**

* `Integer append(Factory<JComponent> messageFactory)`


# Quick Start Guide
# CURRENTLY UNDER CONSTRUCTION. NOT FULLY UPDATED TO REPRESENT CHANGES.

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
