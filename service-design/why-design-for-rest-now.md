# Why design for REST now

Why design for REST now (instead of later).

## Initial Example

Here's an example sub-set of resources designed for a service:

*Service Subset Implementation version 1a*

```
/groups GET
/groups/{groupName} GET, PUT
```

The groupName PUT method takes a representation that looks (in part) like:

*groups/{groupName} PUT request body*

```
{
   "description": "group description",
   "create": [
      {
        "type": "group-item-1",
        "value": "item value 1"
      },
      {
        "type": "group-item-2",
        "value": "item value 2"
      }
   ],
   "delete": [
     {
        "type": "group-item-3",
        "value": "item value 3"
     }
   ]
}
```

With the intention being to update a resource's sub-objects in an atomic way with sub-items to create and delete.

## Proposed RESTful approach

The RESTful implementation might look like:

*Service Subset Implementation version 2*

```
/groups GET, POST
/groups/{groupName} GET, PUT
/groups/{groupName}/items GET, POST
/groups/{groupName}/items/{itemId} GET, PUT, DELETE  -- with the items having the keys: type, value
```

## Problems preventing the RESTful approach

But as a practical matter this could not be created at the moment due to back-end requirements.

Analysis of this problem suggests that the resource we are describing then is actually a command against a back-end system rather than the group items themselves.

## A pragmatic RESTful approach based on constraints

Implementing this would result in this implementation:

*Service Subset Implementation version 1b*

```
/groups GET
/groups/{groupName} GET
/groups/{groupName}/commands POST (with the same body as the original PUT
```

Not much has changed - what's the point in this difference?

## Immediate value

The immediate value for doing this is:

1.  Maintaining the uniform interface aspect of rest by using the HTTP verb with the semantic meaning of what is being done.  *POST* is used here to create a resource under the `commands` collection.
2.  The resource's URL address now expresses that the resource is a command against the group rather than a command directly on the group resource.

## Future value

The real value comes in the future when (if?) the group resource becomes addressable by more standard REST semantics.

Here is what could happen:

### Going with the initial API version

Going with version 1a.

API Version
Route
Verb
Request Representation
Client(s)
Notes
1a
/groups/{groupName}
PUT
 {
 "description": "group description",
 "create": [
 {
 "type": "group-item-1",
 "value": "item value 1"
 },
 {
 "type": "group-item-2",
 "value": "item value 2"
 }
 ],
 "delete": [
 {
 "type": "group-item-3",
 "value": "item value 3"
 }
 ]
 }

client-1
 
1a
/groups/{groupName}
GET
 
client-1
 
2
/groups/{groupName}
PUT
 { "description": "I didnt like my old description" }

client-1 (with changes)</span>,

client-2

client-1 breaks here because *PUT* now operates as a RESTful *PUT* (replacing the full resource) - effectively a different resource.
2
/groups/{groupName}/items
POST
 { "type": "group-item-1", "value": "item value 1" }

client-1 (with changes),

client-2

client-1 needs to call this instead
2
/groups/{groupName}/items/{itemId}
DELETE
 
client-1 (with changes)

client-2

 

## Going with the pragmatic API approach

Using version 1b as the starting API version.

API Version
Route
Verb
Representation
Client(s)
Notes
1b
/groups/{groupName}/commands
POST
 {
 "description": "group description",
 "create": [
 {
 "type": "group-item-1",
 "value": "item value 1"
 },
 {
 "type": "group-item-2",
 "value": "item value 2"
 }
 ],
 "delete": [
 {
 "type": "group-item-3",
 "value": "item value 3"
 }
 ]
 }

client-1
 
1b
/groups/{groupName}
GET
 
client-1
 
2
/groups/{groupName}/commands
POST
*Same as above*

client-1
 
2
/groups/{groupName}
GET
  

client-1,

client-2

 
2
/groups/{groupName}
PUT
 { "description": "I didnt like my old description" }

client-2
 
2
/groups/{groupName}/items
POST
 { "type": "group-item-1", "value": "item value 1" }

client-2
 
2
/groups/{groupName}/items/{itemId}
DELETE
 
client-2
 

In this case client-1 requires no changes. The service developers will need to re-wire the `/groups/{groupName}/commands` *POST* to work in the context of new functionality, but the contract need not change.

Over time, client-1 may decide (or be encouraged) to move from the *commands* resource over to the version 2 APIs. But because we structured the API in this way initially, the decision can be made outside of the scope of when version 2 of the API is released.

Ultimately, the service owners can decide to maintain the `/groups/{groupName}/commands `resource or even extend it with additional HTTP verb support (like *GET* and/or a `commands/{commandId}` sub-resource without breaking existing clients.

 
