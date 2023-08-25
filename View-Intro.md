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
* [Table Definition](Table-Definition.md)

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
through Wabase's inner API (backend server), which generates the treSQL inquiry, which is then translated into sql.
Thus, the key concept to understand is that in the end, most views can be directly translated into an sql request. Of
course, except for the most advanced ones, which include validations and stuff like that, but we shouldn't worry about
it just yet. The whole reason why this is so important, is that when learning how to define views through `.yaml` files
in wabase, remember that the structure of the file is going to resemble the one of an sql request with its own select,
from, where and with clauses.

## Prerequisites and dependencies - treSQL

Before moving on, there is one more thing. In order to fully understand and be able to use all the features of Wabase,
understanding the basic syntax of treSQL is essential. You can find more information about treSQL
[here](https://github.com/mrumkovskis/tresql).

## Next up

Now you are equipped with the most basic knowledge about defining a view with wabase, so it's about time to test it out.
Let's use the very same `user` view we defined above and see what we can do just with this view and different HTTPs
methods. However, before we begin, we should set up our test environment.
