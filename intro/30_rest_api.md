# REST api

## Introduction

Every wabase application architecture is based on defining views and executing actions on them. 
This section contains an overview of http calls to wabase api.
For more details or view definition see [View Definition](../ref/30_viewdef.md).

## List of CRUD functions

On each view with name `view_name` and primary key `id` you can call the following functions:


* `HTTP-GET` to `baseURL/view_name` - get a list of items
* `HTTP-GET` to `baseURL/count/view_name` or `baseURL/count:view_name` - get a count of items
* `HTTP-GET` to `baseURL/view_name/id` - get a single item
* `HTTP-POST` to `baseURL/view_name` - insert a new item
* `HTTP-PUT` to `baseURL/view_name/id` - update an item
* `HTTP-DELETE` to `baseURL/view_name/id` - delete an item

**TODO add requests by key field**

## Other functions

* metadata requests
* File upload / download
* defered requests/responses




-------------------
**TODO unparsed content below**
-------------------

## The basic sections of view

There are **three basic sections for every view** – `name`, `table` and `fields`. These three fields are compulsory and
should be defined in the order shown above.

The first setting for every view is `name`. This setting defines the name of the view, and therefore it's compulsory and
must be unique for every view.

The other two settings are `table` and `fields`. These two settings define the table and the fields of the table which
data will be read-from/written-to. These two fields are also compulsory, since otherwise a view simply won't have any
logical purpose.

One more primary setting for every view is `comment`. This is the only arbitrary setting of the four basic settings.
However, we strongly encourage you to use it. This setting's whole purpose is for you to describe who this exact view is
for. It can be placed in the very end as shown in the **Simple View** section above, or it can be placed right after
the `name` section. There are more sections, but we are going to discuss those a bit later.

## HTTP request to Wabase API to SQL request(s)

This might as well be one of the most important sections for understanding and learning all the capabilities of Wabase.
The idea is quite simple yet very powerful.

As already mentioned, we define views with `.yaml` files. Then, while working with a view through HTTP requests, it goes
through Wabase's inner API (backend server), which generates the treSQL inquiry, which is then translated into SQL.
Thus, the key concept to understand is that in the end, most views can be directly translated into a SQL request. Of
course, except for the most advanced ones, which include validations and stuff like that, but we shouldn't worry about
it just yet. The whole reason why this is so important, is that when learning how to define views through `.yaml` files
in wabase, remember that the structure of the file is going to resemble the one of an sql request with its own select,
from, where and with clauses.



# Views for GET

In this section we are going to discuss Wabase functionality for `HTTP-GET` method specifically and features specific
to this method. Before anything else, we should remember that HTTP requests are matched to Wabase's APIs. `HTTP-GET`
method can be matched to Wabase's `get-API` or `list-API`. Both are very similar, yet there are some minor
differences between the two. The former will return a single record, and the latter will return a list of records.
You can even use the same view definition for both by setting `get-API` and `list-API` in the `API` setting of a view.



## `HTTP-get` matched with `list-API`

This is one of the most common types of requests. We use `list-API` whenever we need to get a list of items. In order
to use it, all we need to do is to send `HTTP-GET` to `baseURL/nameOfTheView`. Now let's see how this works in
action and get back to our very first example from the View-Intro.

```yaml
name: user
table: users
fields:
- id
- name
- surname
- sex
- date_of_birth
comment: This view gives primary information about user data.
```

```sql
SELECT id, name, surname, sex, date_of_birth FROM users
```


# Defining a view with wabase.

Defining a view is the second of the Wabase's key features. In this section, we are going to take a closer look at all
the features and capabilities of defining a view, as well as an example to illustrate the concepts. Firstly, we are
going to see how to define the most basic view in wabase, but then we will discuss some API specific features for
different kinds of requests.

---

## Table of Contents

* [Home](Home.md)
* [View Definition](View-Intro.md)
    * [Intro](View-Intro.md)
    * [View for GET](View-Get.md)
    * [View for insert and update APIs](View-InsertUpdate.md)

---

## Simple view

In wabase you define views through `.yaml` files. Multiple views can be defined in a single file, but in order to keep
our views organized and avoid large files, usually views are distributed among multiple files following some logical
pattern. All the views should be located in `/views` directory.

Now let's take a look at a generic view.

```yaml
name: user
table: users
fields:
  - id
  - name
  - surname
  - sex
  - date_of_birth
comment: This view gives primary information about user data.
```

Even without any prior knowledge about wabase, this view should be pretty self-explanatory. It defines a view named
`user` that reads/writes to fields `id`, `name`, `surname`, `sex` and `date_of_birth` which are fields of table
names `users`.




