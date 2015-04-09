# File format

The file should be a nodejs module, defining:

* `formatVersion` - the string constant '1.0'
* `assets` - a JavaScript object containing zero or more of:
  * `backends` - a JavaScript object containing `Backend` items
  * `statics` - a JavaScript object containing `Static` items
  * `redirects` - a JavaScript object containing `Redirect` items
  * `images` - a JavaScript object containing `Image` items
* `customers` - a JavaScript object containing `Customer` items

# Basic deterministic syntax

These three exports should be defined in one file, with the following (hopefully deterministic) format:

````js
module.exports.formatVersion = '1.0';
mpdule.exports.assets = {
````

[...]

````js
};
module.exports.customers = {
````

[...]

````js
};
````

Assets and customers should be ordered alphabetically by key, and fields within each item as well.
Indent should be two spaces.
If this fits within 100 characters, items should be written on one line, like this:

````js
  something: { otherField: 'qwer', someField: 'asdf' },
````

If this notation would exceed 100 chars, then each field should be put on one line (still alphabetically), like this:

````js
  something: {
    'field-with-hyphens': 'Put the field key in single quotes if it contains special characters',
    noQuot3s_H3r3: 'bla',
    otherField: 'qwer',
    someField: 'asdf',
  },
````

In all cases, the comma at the end of a line should also be present on the last item in an object, since this is allowed by JavaScript,
and leads to consistent versioning diffs.

Put the field key in quotes if it contains special characters, but not if JavaScript allows it as an object field name.

# How item types are defined in this document

The different item types mentioned above are described by conditions on their key and fields, as they appear in the parent object.
To give an example, consider the item type `Temperature`, defined as:

* key: Number, the timestamp when this temperature was measured, in seconds since 1970.
* fields:
  * celsius: Number, degrees celsius measured.
  * location: Free-text description of the measurement location (optional).

If we now define `temperatures` as a JavaScript object containing `Temperature` items, that would mean that this would be an example
of a valid value for the `temperatures` variable:

````js
var temperatures = {
  1234567890: {
    celsius: 18,
};
````

Some further notes on these definitions:

* Fields are mandatory unless explicitly marked as '(optional)'.
* Key and fields are required to be of type `String`, unless another type is specified.

# Item types

## Backend

* key: the domain on which an application needs to be served
* fields:
  * application: the image name of the application to serve for this domain

## Static

* key: the domain on which a static website needs to be served
* fields:
  * repo: the git repository to pull from (optional)
  * folder: the folder that should be served as the domain root (optional)
  * pullFrequence: maximum interval, in milliseconds, between two git pulls (optional)
  * owner: the username of the owner

## Redirect

* key: the domain on which a 301 redirect needs to be served for http port 80
* fields:
  * redirectHost: the host to redirect to
  * owner: the username of the owner

## Image

* key: the image name by which this is referred to in 'application' fields in backends
* fields:
  * pull: Docker image is identified on [the Docker registry](http://registry.hub.docker.com/)

## Customer

* key: the username by which this customer is referred to in 'owner' fields in assets
* fields:
  * realName: The person's real name (optional)
  * otherEmail: The main email address, hosted elsewhere, where this person can be reached
