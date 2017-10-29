## ChatAPP Documentation for Group B
Hi! Welcome to our documentation page. All the information and resources you need to understand and implement our API will be found on this page.


# Quick Start Guide

This guide should step you through how to implement the basic functionality of a chat application. You are free to implement your chat app differently, but this is what we think works best.

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
1. `remoteUserStub.receiveInvite(remoteSelfStub, roomId)`
1. Hope they accept



## Accept an Invite
### Context (What you need prior)
* `remoteUserStub` - the stub of the remoteUser whose room you want to connect to. *Stored in the invite that was received.*
* `roomId` - the roomId of the room you want to connect to. *Stored in the invite that was received previously*
* `remoteSelfStub` - your own remote stub to be sent

### Steps
1. Get Occupants from `remoteUserStub` and `roomId`: `userStubs = remoteUserStub.getOccupants(roomId)`
1. Foreach `stub` in `userStubs`
	1. Add `remoteSelfStub` to everyone else’s rooms: `stub.addUserToRoom(RemoteSelfStub, RoomId)`
1. *Add user to my room if add was successful (could this cause problems with a mismatch in separate local chat rooms?)*

*Notice Anything Familiar???* ***We are able to reuse the same API functions from connect.***


## Decline an Invite
### Context (What you need prior)
* `remoteUserStub` - the stub of the remoteUser whose room you want to connect to. *Stored in the invite that was received.*
* `roomId` - the roomId of the room you want to connect to. *Stored in the invite that was received previously*
* `remoteSelfStub` - your own remote stub to be sent

### Steps
1. Just delete the invite from local storage.

## Create Room

That’s local. Do it yourself.

# FAQs

So to me one thing seems to be a division falling out of our API methods. I think we might be able to split things up this way. Maybe not now since it would take some refactoring, but on the revision. It might make sense to have room stubs. 

### User
* Map\<String, String\> : sendRoomNames() 
* Integer: receiveInvite(IStub : userStub, String : roomID)
* Integer: receiveMessage(Class\<T\> : classType, DataPacket\<T, S\> : data, String : roomID)

### Room
* Array[IStub] : sendOccupants(String : roomID)
* Integer : addUserToRoom(IStub : userStub, String: roomID)
* Integer : removeUserFromRoom(IStub : userStub, String : roomID)



