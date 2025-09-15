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
- a set of Users with
    - a string Username
    - a string Password

## Write a requires/effects specification for each of the two actions. (Hints: The register action creates and returns a new user. The authenticate action is primarily a guard, and doesn’t mutate the state.)
register (username: String, password: String): (user: User)
- **requires:** the username does not exist
- **effect:** create a new user with this username and password

authenticate (username: String, password: String): (user: User)
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
register (username: String, password: String, email: String): (user: User, token: String)
- **requires:** the username does not exist
- **effect:** create a new user with this username and password

authenticate (username: String, password: String): (user: User)
- **requires:** the username and password combination exists in the set of users
- **effect:** none

confirm (username: String, token: String)
- **requires:** the username is registered
- **effect:** will change the Confirmed state to true


# Exercise 3: Comparing concepts
## Deliverables: a concept specification for PersonalAccessToken and a succinct note about how it differs from PasswordAuthentication and how you might change the GitHub documentation to explain this.

**concept** PersonalAccessToken\[User]
**purpose** grant access to all repos of a particular user securely
**principle** An authenticated user to an application wants to grant you (via the CLI) or another application access to its resources (ie a repo).  The user generates a personal access token. They then input this personal access token into the CLI/other application, which authenticates them as being them so now they can manipulate the resources (ie push, pull, publish repos) and be treated like the user.  They cannot use this to log into the real application itself however, unlike a password.
**state**
a set of PersonalAcessTokens with 
    - a User
    - a boolean Disabled
**actions**
generatePAT(user: User): (personalAccessToken: PersonalAcessToken)
    - **requires** user exists and is authenticated
    - **effect** creates a personal access token and shares it with user
authenticate(personalAccessToken: PersonalAcessToken): (user: User)
    - **requires** personalAccessToken exists and it is not disabled
    - **effect** none

Passowrd authentication differs from personal access tokens (PATS) in a few ways.  They both are meant for the account-holder as it gives access to all repositories in the organizations that you are a part of, and your personal repos.  PATS are more secure than regular passwords, and you must already have an account to generate one.  Additionally you can revoke access to using them, and in the case that it leaks, the user can't log into your account itself and you can simply disable that token.  It's more secure (fine-grained are even more secure with only specific repos and permissions rather than giving you all access) than passwords, and many CLI tools use that over passwords.  

Note: technically Github also requrires a username with the CLI, but it isn't used to authenticate.  In order to be more general for all PersonalAccessTokens, I chose to omit that argument in authenticate().

# Exercise 4
