# Defining a view with wabase.
Defining a view is one of the Wabase's key features. In this section we are going to take a closer look at all the
features and capabilities of defining a view, as well as an example no illustrate the concepts. We are going to 
start with a simple example and slowly improve it, making it more and more complex to illustrate one concept at a time.

- - -

## Simple view
In wabase you define views through .yaml files. Multiple views can be defined in a single file, but in order to keep 
our views organised and avoid large files, usually many files are used. 
This is probably 
one of 
the simplest views you can define.

```
name: bank_list_row
table: bank
fields:
- id
- code
- country_code
- name
  order: ~id
```

In this definition we can see a view which gives some basic information about a bank.
Firstly, we define the name of the view `bank_list_row`.  Next up, we set the source table `bank` and the fields of the
view `id`, `code`, `country_code` and `name` are defined. Finally, we define preferred order of the view, which is by 
the `id`. When requested with `list` API, the returned JSON would look something like this


**#TODO** add example and ask if it's possible to see the responses from tests myself.

> 1: {id: 1, code: "LVHABA-1", country_code: 1, name: "Swedbank"}
> 
> 
> 
> 
> 
>


## Field options

We've just seen that fields of the view are defined under the `fields:` section of a view. Now let's take a look at 
different settings we can set for individual fields. 


### Optional and Compulsory fields

By default, all the fields of a view are optional. In order to make a field compulsory just add `!` after the name 
of the field.

```
name: bank_list_row
table: bank
fields:
- id    !               #copulsory field
- code  int             #type
- country_code  50      #length          
- name
  order: ~id
```

**#TODO** ask what other options are there

### type

Some 


### length



**#TODO** are there any other field options?








## Nested view
**#TODO** Nested, multilayer - what's the correct term

Now let's take a look on how to define a nested view - there is an example below. 

```
name: bank_with_account
table: bank
fields:
- id
- code
- name
- account:
    table: account
    fields:
    - id
    - billing_account
    - last_modified
```

**#TODO** What's the difference between ^ and v
```
name: lietotajs_list
table: adm_lietotajs l
api: lietotajs_skatit list, lietotajs_skatit count
comments: Lietotāji
fields:
- id
- vards:
  - Vārds
- uzvards:
  - sortable: true
  - Uzvārds
- telefons
- lomas *:
    table: adm_lietotaja_loma ll
    comments: Lietotāja lomas
    joins:
      - ll / adm_lietotajs l
    fields:
      - adm_loma = ^lomas_choice.nosaukums -> = ^lomas_choice[^nosaukums = _]{id}

```

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

## API


## Validations
