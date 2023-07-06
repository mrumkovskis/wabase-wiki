# Views for GET 

In this section we are going to discuss GET method specifically and what features does it have. The features 
discussed in this chapter are unique for this method. However, before we do that, we are going to see how HTTP GET 
method is matched with Wabase's inner APIs. First of all,

**#TODO**: ask about how APIs and HTTP methods are connected


Thus depending your needs

---

## Get a list

Usually the most common one to use is the regular get method, which

### Setting filters

Starting 


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
