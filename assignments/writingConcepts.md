# Exercise 1: Reading a Concept

## Invariants. What are two invariants of the state? (Hint: one is about aggregation/counts of items, and one relates requests and purchases). Say which one is more important and why; identify the action whose design is most affected by it, and say how it preserves it.
One invarient is that the "count Number" in a Request must be non-negative since we can't have negative inventory of an item.  Additionally, the set of Purchases in the requests should match up with the set of purchases itself.  Specifically, for each purchase in a Request, that purchase in Purchases must point to the item in the request.  There also can't be any additional purchases in the Requests set. 

The more important invarient is the invariant about the requests and purchases matching up.  This is because the stated purpose of the GiftRegistration is to track the purchases of requested gifts (Though counting the number of requests is important as well). The givers and recipients are most affected by this invariant because they can see if a gift has already been purchased, or if they have not.  By preserving the invariant that the set of Purchases matches the set of Requests, we can guarantee that we know exactly who bought each gift and which items were purchased.


## Fixing an action. Can you identify an action that potentially breaks this important invariant, and say how this might happen? How might this problem be fixed?
removeItem could break this invariant.  This is because if the item is removed from the registry, then a person who has already purchased that item will still have a "purchase", but not an associated item.  Thus, we can't track who bought which presents, which the principle says we should be able to do after the recipient closes the registry.  

A way to combat this is by simply disallowing a removeItem when purchases already exist. 


Another way to combat this is by adding a flag to Requests called inactive.  That way, we can store the "Purchases", but the flag gets switched when removeItem is called.  

## Inferring behavior. The operational principle describes the typical scenario in which the registry is opened and eventually closed. But a concept specification often allows other scenarios. By reading the specs of the concept actions, say whether a registry can be opened and closed repeatedly. What is a reason to allow this?
The concept actions do allow repeated opening and closing of the registry.  A reason to allow this is that a recipient might temporarily close the registry to edit items offline (e.g. during event planning) and reopen later when they've finalized what they want. 

## Registry deletion. There is no action to delete a registry. Would this matter in practice?
No.  You can just close it and then it's not visible to the public.  Maybe from a UI perspective, you don't want the recipient to see a bunch of old registries, but there are ways to combat this.  A delete option could be useful just to prevent clutter and save space, but again it's not nessecary in practice.

## Queries. What are two common queries likely to be executed against the concept state? (Hint: one is executed by a registry owner, and one by a giver of a gift.)
A query is not an action.  The giver of a gift will probably want to query "Which items are still available for purchase, and in what quantities?”.  The owner might query "Which items were purchased and what are the relevant details like quantity purchased and who puchased them."

## Hiding purchases. A common feature of gift registries is to allow the recipient to choose not to see purchases so that an element of surprise is retained. How would you augment the concept specification to support this?
One way to augment this is to add boolean to the state called surpriseMode.  The Requests could also have an additional number associated called requestedCount which isn't needed, but could make life easier later.

## Generic types. The User and Item types are specified as generic parameters. The Item type might be populated by SKU codes, for example. Explain why this is preferable to representing items with their names, descriptions, prices, etc.
This way, another concept can deal with users and we are more modularized and generalized.  If we instead made each concept have an item with their names, descriptions, prices, etc., then we're doing extra work because we have to record all this information in multiple places, when in reality we can just use it one concept that can handle everything about it.


# Exercise 2: Extending a familiar concept
## Complete the definition of the concept state.
**state**
a set of Users with
- a string Username
- a string Password

## Write a requires/effects specification for each of the two actions. (Hints: The register action creates and returns a new user. The authenticate action is primarily a guard, and doesn’t mutate the state.)
register (username: String, password: String): User
- **requires:** the username does not exist
- **effect:** create a new user with this username and password

authenticate (username: String, password: String): User
- **requires:** the username and password combination exists in the set of users
- **effect:** none

## What essential invariant must hold on the state? How is it preserved?
Each user must have a unique username.  Otherwise, we can't distinguish between whose trying to become authenticated.  It is preserved because of the specs of the actions.  Specifically, register only allows registration with a username if it doesn't already exist in the set of Users.

## One widely used extension of this concept requires that registration be confirmed by email. Extend the concept to include this functionality. (Hints: you should add (1) an extra result variable to the register action that returns a secret token that (via a sync) will be emailed to the user; (2) a new confirm action that takes a username and a secret token and completes the registration; (3) whatever additional state is needed to support this behavior.)

**State**
- a set of Users with
- a string Username
- a string Password
- a boolean Confirmed
- a string Token

**actions**

register (username: String, password: String, email: String): (User, Token)
- **requires:** the username does not exist
- **effect:** create a new user with this username and password, with Confirmed equal to true and a token is generated.

authenticate (username: String, password: String): User
- **requires:** the username and password combination exists in the set of users
- **effect:** none

confirm (username: String, token: String)
- **requires:** the username is registered
- **effect:** will change the Confirmed state to true


# Exercise 3: Comparing concepts
## Deliverables: a concept specification for PersonalAccessToken and a succinct note about how it differs from PasswordAuthentication and how you might change the GitHub documentation to explain this.

**concept**: PersonalAccessToken\[User]

**purpose**: authenticate a user

**principle**: An authenticated user to an application wants to grant you (via the CLI) or another application access to its resources (ie a repo).  The user generates a personal access token. They then input this personal access token into the CLI/other application, which authenticates them as being them so now they can manipulate the resources (ie push, pull, publish repos) and be treated like the user.  They cannot use this to log into the real application itself however, unlike a password.

**state**

a set of PersonalAcessTokens with 
- a User
- a boolean Disabled

**actions**

generatePAT(user: User): PersonalAcessToken
- **requires** user exists and is authenticated
- **effect** creates a personal access token and shares it with user, and sets the disabled flag to false

authenticate(personalAccessToken: PersonalAcessToken): User
- **requires** personalAccessToken exists and it is not disabled
- **effect** none

disablePAT(personalAccessToken: PersonalAcessToken):
- **requires** the personal access token exists and the disabled flag is false
- **effect** sets the disabled flag to true

enablePAT(personalAccessToken: PersonalAcessToken):
- **requires** the personal access token exists and the disabled flag is true
- **effect** sets the disabled flag to false

Passowrd authentication differs from personal access tokens (PATS) in a few ways.  They both are meant for the account-holder as it gives access to all repositories in the organizations that you are a part of, and your personal repos.  PATS are more secure than regular passwords, and you must already have an account to generate one.  Additionally you can revoke access to using them, and in the case that it leaks, the user can't log into your account itself and you can simply disable that token.  It's more secure (fine-grained are even more secure with only specific repos and permissions rather than giving you all access) than passwords, and many CLI tools use that over passwords.  

# Exercise 4

## Billable Hours Tracking
**concept** BillingHoursTracking

**purpose** Track the number of hours that clients use a specific software

**principle** The company registers the project.  Then, if a client uses the software, an employee marks the time that the client starts using the software.  When the client stops using the software, the employee stops the time used.

**state**

a set of Projects with
- number Totalhours
- a set of Sessions

a set of Sessions with
- a Project
- a string Description
- a dateTime Start
- a dateTime? End

**actions**

startSession(project: Project, notes: String): Session
- **requires** requires that project exists, and another session is not currently running
- **effects** creates a new session associated with the project with the current time and uses notes as a description for the project

endSession(session: Session): 
- **requires** requires that the session exists and that it hasn't ended yet
- **effects** ends the session with the current time

changeEndTime(session: Session, endTime: dateTime): Session
- **requires** that the session exists and that it has an endtime
- **effects** changes the end time to endTime

changeStartTime(session: Session, startTime: dateTime): Session
- **requires** that the session exists
- **effects** changes the start time to startTime

Subtleties: An employee might forget to end or start a session on time, so there exists actions to remedy this.

## Conference Room Booking
**concept** RoomBooking\[Rooms, User]

**purpose** reduce scheduling conflicts of people who need bookable rooms

**principle** the company specifies rooms that can be rented out, as well as what time that they are not open.  A person who wants to use a room reserves a time period to use the room, and then can be assured of having the room free at that time.

**state** 

a set of UnbookableSlots with
- a time StartTime
- a time EndTime
- a Room

a set of Reservations with
- a User
- a Room
- a StartTime
- an EndTime

a set of Rooms with
- a set of UnbookableSlots
- a set of Reservations

**actions** 

disableBooking(room: Room, startTime: Time, endTime: Time): UnbookableSlot
- **requires** the room exists, and there are no reservations at the same time
- **effects** If there's an overlapping unbookable slot associated with the room, merge it with this unbookable slot.  Otherwise, create a new unbookableSlot.

enableBooking(room: Room, startTime: Time, endTime: Time):
- **requires** startTime and endTime fall within an unbookable slot associated with the room
- **effect** Remove the time between startTime to endTime from the unbookableSlots associated with the room.  If needed, split up the unbookable slot that contains the startTime and endTime.

reserveRoom(user: User, room: Room, startTime: Time, endTime: Time): Reservation
- **requires** the room exists and has the interval [startTime, endTime) does not overlap with any reservations or unbookable slots with the associated room
- **effects** creates a reservation associated with the room for the specified times.

cancelReservation(reservation: Reservation)
- **requires** the reservation exists
- **effect** deletes the reservation, freeing up the time for other people to reserve the room

Subtleties: If someone reserves a room already, we need to figure out what to do if the company/university wants to make that part unbookable.  This system ensures that the time is guaranteed if you book the time, so it just doesn't allow the company/university to do this.  Perhaps they could contact the bookers directly if they really needed to.

## Address Verification\[Store]
**concept** AddressVerification

**purpose** fraud protection

**principle** a company adds all their locations with their addresses.  a user attempts to buy something from a store and provides their address.  If their address is an acceptable distance away, they are authenticated and the purchase goes through.  Otherwise, they are not verified.

**state**
a set of StoreLocations with
- a string Address
- a number AcceptableDistance
- a Store

**actions**
addStoreLocation(store: Store, address: Address, acceptableDistance: Number):
- **requires** the address isn't already used by the store
- **effect** creates a new store location associated with the store specified as well as an acceptable distance a customer could be located from it

removeStoreLocation(StoreLocation: storeLcation):
- **requires** the storeLocation exists
- **effect** removes the store location

modifyAcceptableDistance(StoreLocation: storeLcation, newAcceptableDistance: number)
- **requires** the storeLocation exists
- **effect** modifies the acceptable distance from the store location to the distance newAcceptableDistance miles

authenticateUser(userLocation: String, storeLocation): boolean
- **requires** the storeLocation exists
- **effect** returns that the purchaser is suspicious if the distance between the userLocation and the storeLocation is greater than acceptableDistance associated with the store location

Subtleties: this allows multiple different stores from the same location (ie many stores in a mall).  Also, it makes sure each store location is only listed once because it's a set.