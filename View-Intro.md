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
    * [View for get, list and count APIs](View-GetListCount.md)
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
comment: This view gives primary information about user data.
table: users
fields:
- id:
    - comment
- name
- surname
- sex
- date_of_birth
```

### Primary fields
There are **4 primary settings for every view** and those should be defined in this exact order. 

The first setting for every view is `name`. This setting defines the name of the view, and therefore it's compulsory 
and is unique for every view. 

The next setting is `comment`. This is the only arbitrary setting of the four basic settings, however we 
strongly encourage you to use it. This setting's whole purpose is for you to describe who this exact view is for.

The third and the fourth settings are `table` and `fields`. These two settings define the table and the fields of the 
table which data will be saved to, read from or deleted from. These two fields are also compulsory, since otherwise 
a view simply won't have any logical purpose.

### Field settings
We've just seen that fields of the view are defined under the `fields:` section of a view. Now let's take a look at 
different settings we can set for individual fields.


```
name: user
comment: This view gives primary information about user data.
table: users
fields:
- id                !       #not null
- name              15      #length
- surname           15      #length
- age               int     #type
- sex       

```


### Not Null

### Length

### Type

**#TODO** conventions?

check mojoz/src/main/scala/MdConventions.scala

### Other

**#TODO** ask what other options are there



> 1: {id: 1, code: "LVHABA-1", country_code: 1, name: "Swedbank"}
> 
> 
> 
> 
> 
>








