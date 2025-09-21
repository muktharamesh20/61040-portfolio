# Composing Conecpts

## Concept Questions

### The NonceGeneration concept ensures that the short strings it generates will be unique and not result in conflicts. What are the contexts for, and what will a context end up being in the URL shortening app?

The reason for contexts is to allow more flexibility for the users. If both Instagram and Snapchat used this service, and wanted a shortURL called "feed", they could each have one in seperate contexts even though it's the same URL. The reason that this is okay is because different domain names can have the same shortened URL, but a single domain can't have duplicate shortened URLs. This is why the context will most likely end up being the domain name of the website.

### Why must the NonceGeneration store sets of used strings? One simple way to implement the NonceGeneration is to maintain a counter for each context and increment it every time the generate action is called. In this case, how is the set of used strings in the specification related to the counter in the implementation? (In abstract data type lingo, this is asking you to describe an abstraction function.)

It must set a set of used strings to maintain an invarient that the generated string is unique. If NonceGeneration did not know what strings were previously used, it could not guarantee that a newly generated string truly was unique.

Because specifications and abstract data types don't care about the implementation, and we want to keep the concept as generalized as possible, we actually wouldn't mention that it maintains a counter for each context.

The way that the used strings relate to the counter in implementation is that all numbers below the counter (and above the initialized counter value) is in the set of used strings. For instance, if the counter was initialized to 0, and now is 10, the integers from 0-9 (inclusive) are all in the set of used strings. The next used string/shortened URL in this case would be 10.

### One option for nonce generation is to use common dictionary words (in the style of yellkey.com, for example) resulting in more easily remembered shortenings. What is one advantage and one disadvantage of this scheme, both from the perspective of the user? How would you modify the NonceGeneration concept to realize this idea?

One benefit is that the shortened URLs are easier to remeber and to share verbally. This helps with usability, especially as the number of shortened url's in a context grows.

One disadvantage is that it could become very long. Using a common dictionary means that we are concatonating words, and words can be long. This reduces the advantage of "short" links.

I would modify the NonceGeneration concept to simply change the purpose to "generate unique memorable strings, made up of dictionary words, within a context" and the generate() effect to "returns a nonce that is not already used by this context, and is made up of dictionary words".

## Synchronization Questions

### Partial matching. In the first sync (called generate), the Request.shortenUrl action in the when clause includes the shortUrlBase argument but not the targetUrl argument. In the second sync (called register) both appear. Why is this?

Request.shortenURL is something that the user requests, probably by clicking something on the frontend. It's not a real function, so it's okay if there are different arguments (the arguments aren't even defined anywhere in the Concept Specification for us to follow!) because the overall request is the same: to shorten the URL. In the first sync "generate", we only need the domain name to ensure no duplicate shortened url. In the second sync, we add "targetURL" because in order to register a shortenedURL, we actually want to know where it points to so that's just some additional information that we need for the URLShortening.register to work.

### The convention that allows names to be omitted when argument or result names are the same as their variable names is convenient and allows for a more succinct specification. Why isn’t this convention used in every case?

Sometimes, you bind variables used in other clauses. For instance, the return of UrlShortening.register() is shortURL in the setExpiry sync. However, we want to re-use this in setExpiry() result argument too. Thus, we want the same variable for "resuorce" and the return value of UrlShortening.register() which both have different names, so at least one of them have to break the convention.

Also, in the case of the generate sync, it's simply more readable to use "shortUrlBase" than "context". Sometimes we break convention even though it's more conveniant and succinct to be more readable.

### Why is the request action included in the first two syncs but not the third one?

It requires no user input, and we want it happen every time a new shortened URL is registered. This way, we make setting an expiration a system action that triggers only when nessecary.

### Suppose the application did not support alternative domain names, and always used a fixed one such as “bit.ly.” How would you change the synchronizations to implement this?

Request.shortenUrl in the "register" sync no longer would need shortUrlBase, and neither would the "generate" sync. Then, we would put the fixed domain name (ie "bit.ly") in place of shortUrlBase as a string in UrlShortening.register().

### These synchronizations are not complete; in particular, they don’t do anything when a resource expires. Write a sync for this case, using appropriate actions from the ExpiringResource and URLShortening concepts.

**sync** expireURL

**when** ExpiringResource.expireResource(): (resource)

**then** UrlShortening.delete(shortURL: resource)

## Extending the design

### Design a couple of additional concepts to realize this extension, and write them out in full (but including only the essential actions and state). It should not be necessary to make any changes to the existing concepts.

**concept** AnalyticsCounter\[Resource, User]

**purpose** allow a user to track how many times their resource was used

**principle** after a random person attempts to use a resource, the analytics are updated so that the url creater knows that the number of uses has increased by one.  

**state**
- a set of Resources with
    - a count Number
    - an owner User

**actions**

- accessResource(resource: Resource)
    - **requires** the resource exists
    - **effect** the analytics will record that the resource was accessed one more time

- getAnalytics(resource: Resource): (numAcesses: Number)
    - **requires** the resources exists for the resource
    - **effect** returns the number of times the resource was accessed

- register(resource: Resource)
    - **requires** the resources exists
    - **effect** creates the resource

- delete(resource: Resource)
    - **requires** the resources exists
    - **effect** deletes the resource

-----

**concept** Authenticate

**purpose** Tracks users and their login information

**principle** A user signs up on a site. The user then logs in. Now, the site can track this person's activities, and make sure he only takes actions that he's allowed to do. The user can log off and the site no longer thinks that it is that user.

**state**
- a set of username with  
    - a password String

**actions**
- authenticate(username: String, password:String): (authenticated: Boolean)

    - **requires** the username and password pair exist

    - **effect** returns true if the pair exists, and false otherwise
- register(username: String, password:String)

    - **requires** the username does not already exist

    - **effect** the username and password pair are registered


-----
----

### Specify three essential synchronizations with your new concepts: one that happens when shortenings are created; one when shortenings are translated to targets; and one when a user examines analytics.

**sync** register

**when**
Request.shortenUrl (targetUrl, shortUrlBase)

NonceGeneration.generate (): (nonce)

**then**
UrlShortening.register (shortUrlSuffix: nonce, shortUrlBase, targetUrl): (shortUrl)

AnalyticsCounter.register (resource: shortURL)

-----

**sync** useShortenedLink

**when**
Request.translateShortenedUrl (shortUrl)

**then**
UrlShortening.lookup (shortUrl): (targetUrl)

AnalyticsCounter.accessResource(resource: shortUrl)

-----

**sync** checkAnalytics

**when**
Request.checkAnalytics (shortUrl)

Authenticate.authenticate(username, password)

**then**
AnalyticsCounter.getAnalytics(resource: ShortUrl)


-----
-----

### As a way to assess the modularity of your solution, consider each of the following feature requests, to be included along with analytics. For each one, outline how it might be realized (eg, by changing or adding a concept or a sync), or argue that the feature would be undesirable and should not be included:

- **Allowing users to choose their own short URLs;**
  - Just don't use NonceGeneration in the register sync, and use UrlShortening.register() with user input for the shortUrlSuffix argument.
- **Using the “word as nonce” strategy to generate more memorable short URLs;**
  - We could use a WordAsNonceGeneration concept which acts exactly like NonceGeneration, but has an additional aspect of generate() where the effect says "returns a nonce that is not already used by this context, and is made up of common dictionary words". The purpose would also change to include the fact that the strings are unique and made up of words.
- *I*ncluding the target URL in analytics, so that lookups of different short URLs can be grouped together when they refer to the same target URL;**

  - We could extend Analytics to include a targetUrl attribute linked to the resource. However, this doesn't seem very useful as there isn't much use in mulitple shortUrls being linked to the same target url. It will make things more confusing on the user side too since people might think two urls link to two different websites.

  - The only use case that maybe makes sense is if we also allow users to choose their own short URLs, and maybe you could track how often people click on one versus the other. If you're doing a marketing campaign with two different flyers, and each has a different shortUrl link, maybe you can use which one is clicked to figure out which flyer works better. However, this isn't the purpose and isn't in the principle of any of the concepts.

- **Generate short URLs that are not easily guessed;**
  - I would argue that this is undesirable because this would likely require longer and/or random characters strung together. We are hoping to shorten URLs, so adding this "security" could be a tradeoff. However, if security was a very big issue and people still want it because it's shorter than normal, then we could define a SecureNocneGeneration concept with purpose "returns a nonce that is not already used and has sufficient randomness to resist guessing."
- **Supporting reporting of analytics to creators of short URLs who have not registered as user.**
  - Perhaps we can generate passcodes or links with each short URL, and use an Email concept to send it to the creator. Or, we could just display it once to the creator too. That way, a creator can get back to the correct analytics without registering an account. This could be good since it's easier to use the service (lower barrier to entry).
