# Defining a view with wabase.
Defining a view is one of the Wabase's key features. In this section we are going to take a closer look at all the
features and capabilities of defining a view, as well as an example no illustrate the concepts. Firstly, we are 
going to see how to define the most basic view in wabase, but then we will discuss some API specific features for 
different kinds of requests.

---

## Table of Contents
* [Home](Home.md)
* [View Definition](View-Intro.md)
    * [Intro](View-Intro.md)
    * [View for get, list and count APIs](View-Get.md)
    * [View for insert and update APIs](View-InsertUpdate.md)
* [Table Definition](Table-Definition.md)

- - -

## Simple view
In wabase you define views through `.yaml` files. Multiple views can be defined in a single file, but in order to keep 
our views organised and avoid large files, usually views are distributed among multiple files following some logical 
pattern. All the views should be located in `/views` directory. 

Now let's take a look at how generic view looks. 

```
name: user
table: users
fields:
- id:
    - comment
- name
- surname
- sex
- date_of_birth
comment: This view gives primary information about user data.
```

Even without any prior knowledge about wabase, this view should be pretty self-explanatory. It defines a view named 
`user` that reads/writes to fields `id`, `name`, `surname`, `sex` and `date_of_birth` which are fields of table 
names `users`. 

## Primary fields
There are **3 primary settings for every view** - `name`, `table` and `fields`. These three fields are compulsory 
and should be defined in the order shown above. 

The first setting for every view is `name`. This setting defines the name of the view, and therefore it's compulsory 
and unique for every view. 

The other two settings are `table` and `fields`. These two settings define the table and the fields of the table 
which data will be read-from/written-to. These two fields are also compulsory, since otherwise a view simply won't 
have any logical purpose.

One more primary setting for every view is `comment`. This is the only arbitrary setting of the four basic settings, 
however we strongly encourage you to use it. This setting's whole purpose is for you to describe who this exact view 
is for. It can be placed in the very end as shown in the **Simple View** section above, or it can be placed right after 
the `name` setting.


## View for different HTTPs methods

Now you are equipped with the most basic knowledge about defining a view with wabase, so now it's about time to test 
it out. Let's use the very same `user` view we defined above and see what we can do just with this view.

> 1: {id: 1, code: "LVHABA-1", country_code: 1, name: "Swedbank"}
>
>
>
>
>
>

