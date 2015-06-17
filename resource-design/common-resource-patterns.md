# Common Resource Patterns

Though RESTful resources don't necessarily fall exclusively into one of these three categories, when they do, thinking in these terms adds consistency to your service and can make implementation and use easier.

## Collection Resources

Topics

* Conventions used to design representations of collections & how to keep collections easy to iterate.
* Media Types: 
    - [application/atom+xml](http://www.ietf.org/rfc/rfc4287.txt)
    - [application/vnd.collection+json](https://www.iana.org/assignments/media-types/application/vnd.collection+json) and http://amundsen.com/media-types/collection/
    - [application/vnd.collection.next+json](https://www.iana.org/assignments/media-types/application/vnd.collection.next+json) and http://code.ge/media-types/collection-next-json/

## Object Resources

These are the standard resource types for a singular resource representing a domain entity.

## Key/Value Resources

Key/Value Resources are used for domain entities that are accessed similar to a map or dictionary.
