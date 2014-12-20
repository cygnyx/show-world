#show-world

Another couchapp version of Hello, World!
written December 2014 by [cygnyx]

# Introduction

This is a step-by-step guide to creating a 'Hello, World!' pure
couchdb application based on version 1.6.1. The guide is followed by
a general explanation of what is going on.

# Preliminaries

Get [couchdb] running on your
local system and verify that it is operating properly.

Edit the file '/etc/hosts' and append 'hello' to the line starting
with: '127.0.0.1'. Initially it might look like:

```
127.0.0.1 localhost  
```

And it should be changed to something like:

```
127.0.0.1 localhost hello
```

Make sure the leave a space character between the words. You may need
administrator permission to modify this file.

## Configuration

Using your web browser go to: <http://localhost:5984/_utils>.
This is the couchdb administration page called Futon.
If necessary, sign in with administrator permissions in the lower
right corner of the page.

Near the top left of the page, select 'Create Database ...' and a
 modal dialog box appears with a prompt for the database name.
Enter 'hello' for its name and select 'Create'

Futon will display (the empty) contents of the newly created database.

Near the top left of the page, select 'New Document'.
Near the top right of the page, there is a 'Field' and a 'Source' tab,
select the 'Source' tab instead of 'Field'.
Double click on the text to enable editting.

Replace the default text with:

```json
{
   "_id": "_design/app",
   "rewrites": [
       {
           "from": "",
           "to": "_show/index"
       },
       {
           "from": "*",
           "to": "_show/404"
       }
   ],
   "shows": {
       "index": "function(doc, req){ return {body: '<h1>Hello, World!</h1>\\n'} }",
       "404": "function(doc, req){ return {body: '<h1>404 - Document not found</h1>\\n'} }"
   }
}
```

Near the top left of the page, select 'Save Document'
Futon will redisplay this document with some additional information.

On the right side of the page, select 'Configuration' from the menu.

Scroll to the bottom and on the bottom left of the page, select 'Add a
new section' and a modal dialog box appears.
Enter 'vhosts', 'hello', '/hello/_design/app/_rewrite' for section,
option, value, respectively. Select 'Create'

## Verify

In a web browser, goto: <http://hello:5984> to see the 'Hello,
World!' website.

## Explanation

To set up an effective couchapp, or couchdb application, a scheme is
needed to adjust nice looking 'user' URLs into the internally
consistent couchdb URIs. The mechanism used is based on the host name of
the URL; many hosts can be located on a single couchdb.

The 'hosts' file is a system file that is used to create
translations from system names to IP addresses. The line modified is
the 'loopback' entry that systems use to translate 'localhost' to your
own local system. The value 127.0.0.1 is a special IP value to
always refers to your local system. You can add many aliases for your
local system. These names do not need to have full internet domain
names like 'example.com'. In this case, we use 'hello' as an alias for
the local system. Add an additional alias for each couchapp.
In Unix-like systems the file is located in the '/etc' directory.
In MS Windows the location may depend on your version.

The couchdb includes a system management interface called Futon.
The URI is <http://localhost:5984/_utils>.
Since the hostname 'hello' will be re-routed, it is important to use
'localhost' here.

Initially couchdb is in 'admin party' mode. That is, everyone is an
administrator and can modify the system. You will likely want to
change this behaviour and create an administrator account. If you have
created an administrator account, then you must login with it inorder
to make this configuration changes. The account will logout after a
period of time and it may be necessary to login again.

We create a
database called 'hello' to hold all the data needed for the app.
The data for this app is the single JSON document that was entered.
All couchdb documents are
primarily [JSON] data with some extensions for handling 'attachments'. 
When Futon creates a new document it fills it in with some minimal
JSON which has the _id field. All documents must have this
field. The _ identifies it as a system field. The value of the _id
is a UUID (Universally Unique IDentifier). The default JSON is not
appropriate for this app configuration.

couchdb has a number of conventions on how to structure data within
itself.
At some level a database in couchdb is only a set of JSON-like
documents.
The reality is that documents are partitioned into data documents and
design documents. Data documents are the normal run-of-the-mill
documents that contain data. The design documents are important
meta-data documents. Design documents must have ids prefixed with
_design/. Note the _ to identify a system feature and the trailing
/ to identify a hierarchical structure. 

In this case, we create a design document called 'app'. Any other name
could have been used. The fully qualified path to this design document
is  /hello/_design/app. A single database can have many design documents.
You might notice that couchdb pretty prints the JSON
documents when you save them.

This app has 4 fields in its JSON document: _id and _rev which are system
required fields; rewrites and shows which are special names used by couchapp
to implement Web sites.

The rewrites field is an array of rules for translating nice looking URLs into
couchdb internal URIs. There are two rules in this app. The first rule
translates an empty URL to a URI call _show/index, while the
second rule translates every URL to _show/404. The _show URI is a special
couchdb element that invokes the corresponding Javascript function located
in the shows field. Note that the catch-all asterik does not match an empty URL.

The shows field is an array of functions that implement the apps (simple)
functionality. The index function returns the app text, while the 404 function
returns a missing document page.

Finally, the app is wired together by adding an entry to the vhosts
configuration. This routes anything with the hostname 'hello' to
/hello/_design/app/_rewrite. The prefix is the fully qualified path
to the design document we created. The suffix _rewrite is a system
identifier that will process URLs and convert them into URIs based on
the rules in the design document.

## Next
These steps build a static Hello, World! website even though Javascript
functions are invoked to build the static pages.
The principal idea of a couchapp is to provide enough
infrastructure to transform JSON documents into Web browser friendly
documents.
Since couchdb uses HTTP protocol and JSON documents, it works well
with Ajax based websites.

[cygnyx]: https://github.com/cygnyx
[couchdb]: http://couchdb.apache.org
[JSON]: http://www.json.org
