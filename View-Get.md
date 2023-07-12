# Views for GET 

In this section we are going to discuss Wabase functionality for `HTTP-GET` method specifically and features specific 
to this method. Before getting to the features we should remember that HTTP GET method can be matched to Wabase's 
`get-API` or `list-API`. While the former will return a single record, the latter will return a list of records. 
Therefore, there are some distinct features for both of those. We are going to start with th

SQL / trSQL ref

---

## Table of Contents
* [Home](Home.md)
* [View Definition](View-Intro.md)
    * [Intro](View-Intro.md)
    * [View for GET](View-Get.md)
    * [View for POST, PUT](View-InsertUpdate.md)
* [Table Definition](Table-Definition.md)

---

## HTTP methods to Wabase API to SQL request
First of all,

**#DONE**: ask about how APIs and HTTP methods are connected

if id then get otherwise list

HTTP get > Wabase get or Wabase list


Thus depending on your needs you are going to use the according combination of HTTP method and URL.



## Get matched with `list` API

This is one of the most common type of requests. We use `API-list` whenever we need to get a list of items. In order 
to use it, all we need to do is to send `HTTP-GET` to `baseURL/nameOfTheView`. Now let's see how this works in 
action and  get back to our very first example from the View-Intro. 

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

###  Field expressions
Quite often we need the data to be preprocessed on the back-end side before sending the view to the web. This can be 
easily done by declaring a field followed by equation sign and then writing the expression like this `- name_of_fiel 
= expression`. For instance, to create a single field containing full name of a user, all we need to do is to append 
the name, a space and the surname, like this:

```
name: user
table: users
fields:
- id
- full_name = user.name + ' ' + user.surname
- surname
- sex
- date_of_birth
- balance = balance * 10
comment: This view gives primary information about user data.
```

**#DONE** Ask about rules of expressions.
tresql column clause expression

### Setting filters

Whenever we request a list of some kind, quickly after that we need to filter some of 
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
filter is where clause

-smth1
-smth2

=> smth1 AND smth2

Similarly, in order to filter all people older than 18 years old we just add.

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


```
name: pieteikums_list
table: pieteikums p
api: pieteikums_skatit list, pieteikums_skatit count
comments: Pieteikumu saraksts
auth: p.statuss != 'Melnraksts' & p.statuss != 'VERSIJA' & can_access_pieteikums(p.id)
joins:
  - p/konkurss k
  - k/programma prog
fields:
  - id
  - statuss
  - programma = prog.nosaukums
  - konkurss = k.nosaukums
  - k_numurs = k.numurs
  - k_beigu_datums = k.beigu_datums
  - iesniegsanas_laiks
  - progammas_nosaukums
  - uznemums_nosaukums
  - uznemums_registracijas_nr
  - darbibas_periods_no
  - darbibas_periods_lidz
  - pieprasitais_finansejums = (pieteikuma_budzets pb/pieteikuma_budzeta_gads pbg[pb.pieteikums_id = p.id]{sum(pbg.summa)})
  - numurs = concat_ws('/', k.numurs, lpad(p.numurs::varchar, 3, '0'))
filter:
  - if_defined(:q, ts_query(p.weighted_tsv, :q))
  - if_defined(:konkursa_pieteiksanas_beigu_datums_no, ^k_beigu_datums >= :konkursa_pieteiksanas_beigu_datums_no::date)
  - if_defined(:konkursa_pieteiksanas_beigu_datums_lidz, ^k_beigu_datums <= :konkursa_pieteiksanas_beigu_datums_lidz::date)
  - if_defined(:pieprasitais_finansejums_no, ^pieprasitais_finansejums >= :pieprasitais_finansejums_no::numeric)
  - if_defined(:pieprasitais_finansejums_lidz, ^pieprasitais_finansejums <= :pieprasitais_finansejums_lidz::numeric)
  - if_defined(:planotais_sakuma_datums_no, darbibas_periods_no >= :planotais_sakuma_datums_no::date)
  - if_defined(:planotais_sakuma_datums_lidz, darbibas_periods_no <= :planotais_sakuma_datums_lidz::date)
  - if_defined(:planotais_beigu_datums_no, darbibas_periods_lidz >= :planotais_beigu_datums_no::date)
  - if_defined(:planotais_beigu_datums_lidz, darbibas_periods_lidz <= :planotais_beigu_datums_lidz::date)

```
^ is field expression reference to a field name

### Joins

``` 
name: pieteikums_base
table: pieteikums p
api:
comments:
joins:
  - p/konkurss k
  - k/programma prog
fields:
  - id
  - konkurss_id
  - adm_lietotajs_id
  - statuss
  - statuss_admininistrativa
  - statuss_atbilstibu
  - statuss_kvalitate
  - programma = prog.nosaukums
  - konkurss = k.nosaukums
  - k_numurs = k.numurs
  - k_beigu_datums = k.beigu_datums
  - numurs = concat_ws('/', k.numurs, lpad(p.numurs::varchar, 3, '0'))
  - progammas_nosaukums
  - progammas_merkis
  - darbibas_periods_no
  - darbibas_periods_lidz
  - uznemums_nosaukums
  - uznemums_registracijas_nr
  - uznemums_pretendenta_tips_id
  - uznemums_epasts
  - uznemums_talrunis
  - uznemums_www
  - uznemums_adrese adrese
  - uznemums_nace_kods_id
  - amatpersona_vards_uzvards
  - amatpersona_amats
  - amatpersona_epasts
  - amatpersona_talrunis
  - kontaktpersona_vards_uzvards
  - kontaktpersona_amats
  - kontaktpersona_epasts
  - kontaktpersona_talrunis
  - pieteikums -> = _::json
  - budzeta_skaidrojums
  - lidzfinansejums
  - lidzfinansejums_natura
  - pieprasitais_finansejums = (pieteikuma_budzets pb/pieteikuma_budzeta_gads pbg[pb.pieteikums_id = p.id]{sum(pbg.summa)})
  - partneri * pieteikuma_partneri_editable
  - budzets * pieteikuma_budzets_editable
  - aktivitates * pieteikuma_aktivitates_editable
  - faili *:
      table: pieteikuma_pielikums
      fields:
        - id
        - pieteikums_id
        - file_info_id
        - nosaukums = file_info.filename
        - sha_256 = file_info.sha_256
        - apraksts

```


```
name: pieteikums_view
extends: pieteikums_base
api: pieteikums_skatit get
comments: Pieteikums
auth: p.statuss != 'Melnraksts' & p.statuss != 'VERSIJA' & can_access_pieteikums(p.id)
fields:
  - atbildigie * pieteikuma_atbildigie_editable
  - lemumi *:
      table: pieteikuma_vertesanas_lemumi
      fields: 
        - id
        - pieteikums_id
        - bloks
        - lemums
        - diskusija
        - piezimes
        - komisijas_vertejumi *:
            table: pieteikuma_komisijas_lemumi pkl
            joins:
              - pkl/konkursa_vertesanas_komisija kvk
              - kvk/adm_lietotajs l
            fields: 
              - id
              - konkursa_vertesanas_komisija_id
              - vards_uzvards = concat_ws(' ', l.vards, l.uzvards)
              - lemums
              - datums
  - administrativie_vertejumi *:
      table: pieteikuma_administrativie_vertejumi pav
      fields:
        - id
        - adm_lietotajs_id
        - datums
        - statuss
        - kopejais_vertejums = case(can_edit_contest(:1(konkurss_id)) | (pav.adm_lietotajs_id in current_with_substituted()), kopejais_vertejums, null)
        - vards_uzvards = concat_ws(' ', adm_lietotajs.vards, adm_lietotajs.uzvards)
        - _can_view = can_access_contest(:1(konkurss_id))
        - _can_edit = can_edit_contest(:1(konkurss_id))
        - _can_vote = (pav.adm_lietotajs_id in current_with_substituted())
  - atbilstibas_vertejumi *:
      table: pieteikuma_atbilstibas_vertejumi pav
      fields:
        - id
        - adm_lietotajs_id
        - datums
        - statuss
        - kopejais_vertejums = case(can_edit_contest(:1(konkurss_id)) | (pav.adm_lietotajs_id in current_with_substituted()), kopejais_vertejums, null)
        - lemuma_nosacijumi
        - vards_uzvards = concat_ws(' ', adm_lietotajs.vards, adm_lietotajs.uzvards)
        - _can_view = can_access_contest(:1(konkurss_id))
        - _can_edit = can_edit_contest(:1(konkurss_id))
        - _can_vote = (pav.adm_lietotajs_id in current_with_substituted())
  - kvalitates_vertejumi *:
      table: pieteikuma_kvalitates_vertejumi pkv
      fields:
        - id
        - adm_lietotajs_id
        - konkursa_kriteriju_bloki_id
        - datums
        - statuss
        - kopejais_vertejums = case(can_edit_contest(:1(konkurss_id)) | (pkv.adm_lietotajs_id in current_with_substituted()), kopejais_vertejums, null)
        - punkti = case(can_edit_contest(:1(konkurss_id)) | (pkv.adm_lietotajs_id in current_with_substituted()), punkti, null)
        - lemuma_nosacijumi
        - vards_uzvards = concat_ws(' ', adm_lietotajs.vards, adm_lietotajs.uzvards)
        - _can_view = can_access_contest(:1(konkurss_id))
        - _can_edit = can_edit_contest(:1(konkurss_id))
        - _can_vote = (pkv.adm_lietotajs_id in current_with_substituted())
  - konsolidetie_vertejumi * :
      table: pieteikuma_kvalitates_konsolidetais pkv
      fields:
        - id
        - konkursa_kriteriju_bloki_id
        - kopejais_vertejums
        - punkti
        - kriteriju_vertejumi
        - datums
        - statuss
        - adm_lietotajs_id
        - vards_uzvards = concat_ws(' ', adm_lietotajs.vards, adm_lietotajs.uzvards)
  - atbilstibas_konsolidetie_vertejumi:
      table: pieteikuma_atbilstibas_konsolidetais pav
      fields:
        - id
        - kopejais_vertejums
        - kriteriju_vertejumi
        - datums
        - statuss
        - adm_lietotajs_id
        - vards_uzvards = concat_ws(' ', adm_lietotajs.vards, adm_lietotajs.uzvards)
```

select statement join clause


list
1. =
2. filter
3. join
4. order
4. group
5. limit



## Get a record
1. =
2. join 
3. . key




## Get a count

filter group limit (order, field, = don't do anything), join


As you can see, in order to do this you  define a new field followed by colon `:` and then follow the regular view
definition with a one tab indent. Thus nested view follows the same definition syntax. Of course, you can also
define third and fourth layers in the same manner, with each next layer having one more tab indent.

## Joins
**#TODO** Nested vs joins. How view depends on API.

While the regular nested views are usually used for post, 

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
