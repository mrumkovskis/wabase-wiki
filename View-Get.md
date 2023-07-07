# Views for GET 

In this section we are going to discuss GET method specifically and what features does it have. The features 
discussed in this chapter are unique for this method. However, before we do that, we are going to see how HTTP GET 
method is matched with Wabase's inner APIs and how we implement these feature to achieve our goals.

---

## Table of Contents
* [Home](Home.md)
* [View Definition](View-Intro.md)
    * [Intro](View-Intro.md)
    * [View for GET](View-Get.md)
    * [View for insert and update APIs](View-InsertUpdate.md)
* [Table Definition](Table-Definition.md)

---

## HTTP methods and Wabase API
First of all,

**#TODO**: ask about how APIs and HTTP methods are connected


Thus depending on your needs you are going to use the according combination of HTTP method and URL.



## Get matched with `list` API

This is the most common type of requests. We use list API whenever we need to get a list of items. Let's get back to 
our very first example from the View-Intro. 

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

We are going to use the example above and slowly add different settings to show various features of the `list api`.

### Setting filters

Let's start with filtering. Whenever we request a list of some kind, quickly after that we need to filter some of 
those items. It is very easy to do this with wabase, all you need to do is to add `filter` setting after the 
`fields` setting. For instance, in order to filter out all the males, all we need to do is add `sex = "male"` in the 
filter setting. 

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
filter: sex = "male"
comment: This view gives primary information about user data.
```

Similarly, in order to filter all people older than 18 years old we just add.

### =

### 1. join

### 2. order

### 3. group

### 4. limit

## Get a record


### 1. filter

### 2. =

### 3. join

### 4. key




## Get a count

filter group limit (order, field, = don't do anything), join


As you can see, in order to do this you  define a new field followed by colon `:` and then follow the regular view
definition with a one tab indent. Thus nested view follows the same definition syntax. Of course, you can also
define third and fourth layers in the same manner, with each next layer having one more tab indent.

## Joins
**#TODO** Nested vs joins. How view depends on API.

While the regular nested views are usually used for put api

```
name: table_alias_test_bank_1
table: bank bk
joins:
- bk / country cr
  save-to:
- bank
- country
  fields:
- id
- code    -> bank.code    = _
- bk.name -> bank.name    = _
- cr.name -> country.name = _
```


```
name: account_details
extends: account
fields:
- bank.code
- bank.name
key:
- bank_id, bank_code
```

```
name: self_ref_test_account_2
table: account a1
fields:
- full_name = ^account_choice.name
```

```
name:   bank_with_accounts_1
table:  bank
fields:
- id
- code
- name
- accounts[+-=] * :
    table:  account
    fields:
    - id
    - billing_account
    - last_modified -> = now()
    order:
    - id
```


```
coalesce(_, '')
```

```
name: organization_with_accounts
table: organization
fields:
- id
- name
- main_account = organization_account a[a.id = (organization p[p.id = :id] {main_account_id})] {number} -> = organization_account[number = _]{id}
- accounts * :
    table: organization_account
    fields:
    - id
    - number
    - balance
```

```
name:   organization_optional_fields_test
table:  organization
key:    name
fields:
- name
- accounts      [?] * :
    table: organization_account
    fields:
    - number
    - balance
    order:
    - number


name:   organization_ref_only_update_test
table:  organization
fields:
- name
- main_account [/!]:
    table: organization_account
    fields:
    - number
    - balance


name:   organization_ref_only_update_legacy_test
table:  organization
fields:
- name
- main_account:
    table: organization_account
    fields:
    - number  [+]
    - balance [!]

```
## Filter

## Order

## Comments

# GET, LIST, COUNT
## get addS

filter, = and join, key

## list adds

filter order group limit, join, =

## count adds

filter group limit (order, field, = don't do anything), join




# DELETE
key
