## Field options



We've just seen that fields of the view are defined under the `fields:` section of a view. Now let's take a look at
different settings we can set for individual fields.


### Optional and Compulsory fields

By default, all the fields of a view are optional. In order to make a field compulsory just add `!` after the name
of the field.

```
name: bank_list_row
extends
api:
table: bank
fields:
- id    !               # Not null
- code  int             # type
- country_code  50      # length          
- name                  #TODO ask Ozols!
  
```


### type

Some

**#TODO** conventions?

check mojoz/src/main/scala/MdConventions.scala

### length

**#TODO** ask what other options are there

[] - tresql/core/src/main/scala/org/tresql /ORT.scala
SaveOptions(876) and Property(848)
+  - won't update if object already exists use field value only for new object creation
-  - only for childer. will be deleted if those are not in the received list
     =  - chilrends in the list and db will be updated
     ?  - for put and post(save) field is optional
     !  - readonly super puper never save
     [/]- left for itself right side of slash for child

field properties:
optional
type
length
after : comment


4. =
5. ->
6. []
7. *



5. smth || ' ' || smth
6. = ^smth ->
7. person.yaml
8. coalesce


## Nested view
**#TODO** Nested, multilayer - what's the correct term complex hierarchical

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


## API

## Validations

## Authentication

## INSERT UPDATE (SAVE is shortcut for insert and update)
key save-to