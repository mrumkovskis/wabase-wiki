# Views for GET

In this section we are going to discuss Wabase functionality for `HTTP-GET` method specifically and features specific
to this method. Before anything else, we should remember that HTTP requests are matched to Wabase's APIs. `HTTP-GET`
method can be matched to Wabase's `get-API` or `list-API`. Both are very similar, yet there are some minor
differences between the two. The former will return a single record and the latter will return a list of records.
You can even use the same vie definition for both by setting `get-API` and `list-API` in the `API` setting of a view.

---

## Table of Contents

* [Home](Home.md)
* [View Definition](View-Intro.md)
    * [Intro](View-Intro.md)
    * [View for GET](View-Get.md)
    * [View for POST, PUT](View-InsertUpdate.md)
* [Table Definition](Table-Definition.md)

---

## `HTTP-get` matched with `list-API`

This is one of the most common type of requests. We use `list-API` whenever we need to get a list of items. In order
to use it, all we need to do is to send `HTTP-GET` to `baseURL/nameOfTheView`. Now let's see how this works in
action and get back to our very first example from the View-Intro.

```yaml
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

We are going to use the example above and slowly add different settings to show various features of the `list-API`.

### Fields section

Quite often we need the data to be preprocessed on the back-end side before sending the view to the web. This can be
easily done by declaring a field followed by equation sign and then writing the expression. This is how it looks
like this: `- name_of_fiel = expression`. For instance, to create a single field containing full name of a user, all
we need to do is to append the name, a space and the surname, using sql concat_ws function. Like this  `- full_name
= concat_ws(' ', u.name, u.surname)`

```yaml
name: user
table: users
fields:
- id
- full_name = concat_ws(' ', u.name, u.surname)
- sex
- date_of_birth
comment: This view provides basic users data
```

Field expressions are almost limitless, you can preprocess data from the database however you like. In order learn
all the possibilities, let's remember **HTTP request to Wabase API to SQL request** section from View-Intro. There
we discussed that views we define in `.yaml` are ultimately transformed into an SQL request. Here we can see that,
fields clause can be directly compared to sql select clause. So, if we use this view through `HTTP-GET` by sending
`data/user` we should see the generated sql request in the terminal window with the back-end running, and it should
look like this:


> SELECT id, CONCAT_WS(' ', name, surname) AS full_name, sex, date_of_birth FROM users


**#TODO** sortable, comment, no update, readonly options

**#TODO** 'field -> = _::json'

### Filter section

Whenever we request a list of some kind, next step after is that we need to filter some of those items out. It is very
easy to do this with wabase, all you need to do is to add `filter` clause after the `fields` clause. For instance, in
order to filter out all the males, all we need to do is add `sex = 'male'` in the filter setting.

```yaml
name: user
table: users
fields:
- id:
    - comment
- name
- surname
- sex
- date_of_birth
filter:
- sex = 'male'
comment: This view provides basic users data and filters out all males
```

Just like `fields section` is the SELECT clause of an sql statement, as you might have guessed, filter clause is WHERE
clause. Thus, the view above is transformed to this sql request:

```
select u.id, concat_ws(' ',u.name,u.surname) as full_name, u.sex, u,date_of_birth from users where sex is 'male'
```

Similarly, in order to filter all people older than 18 years old we just add.

**#TODO** How to calculate age. full_years function from goda_gimenes doesn't. How to use YAMLs only.

```yaml
name: user
table: users
fields:
- id
- name
- surname
- sex
- date_of_birth
filter: ??????????
comment: his view provides list of users older than 18 years old with basic information about each.
```

**#TODO** custom search requests 'concat_ws(' ', vards, uzvards) %~~~% :q?'

**#TODO**: ^ is field expression reference to a field name. Where to define it in the docs.

**#TODO** find what's a good place for field expression reference (^) in terms of structure (field section?)

**#TODO**

### Order section

This is a relatively self-explanatory section. In `order` section we can define how do we want the list to be 
ordered. It is directly translated to SQL `ORDER BY` clause. By default, the list is ordered by `id`, just like in sql 
but if we wish, we can change it to any other field. for instance using the same view we defined before, we can 
order the list by `name` in alphabetic order.

```yaml
name: user
table: users
fields:
- id
- name
- surname
- sex
- date_of_birth
order: name
comment: This view provides list of users with basic information about each ordered by name.
```

**#TODO** Ordering. Can be done by requested fields or any db field?
**#TODO** What are other ordering options

Really, that's it. There isn't anything more to this section. Basic—yet quite useful. 

### Group section

Again, ones with some SQL experience instantly understand what this section is for. It is directly translated to 
SQL `GROUP BY` clause. By default, there is no grouping, but with this section you can order the list into 
multiple sublists. Here we are going to introduce another structure - bank. 
```yaml
name: bank
table: bank
fields:
- id
- name
- code
- country_code
group: country_code
comment: This view provides list of banks with basic information about each grouped by country.
```
> SELECT id, CONCAT_WS(' ', name, surname) AS full_name, sex, date_of_birth FROM users GROUP BY country_code;

**#TODO** Is there some default grouping


### Limit Section
Similarly to the previous two, this setting is also obvious. It is directly translated to sql `LIMIT` clause. All 
this section does is limit the number of records returned in the list. 

```yaml
name: user
table: users
fields:
- id
- full_name = concat_ws(' ', u.name, u.surname)
- sex
- date_of_birth
limit: 100
comment: This view gives primary information about user data with limit of 100 records.
```

Thus, the view above would be translated into the following sql request

> SELECT id, CONCAT_WS(' ', name, surname) AS full_name, sex, date_of_birth FROM users LIMIT 100;


### Joins section

This might as well be one of the most difficult section

**#TODO** joins
**#TODO** '- partneri * pieteikuma_partneri_editable'
**#TODO** joins vs child/ref

join clause

## `HTTP-get` matched with `get-API`

Now, when we've discussed `list-API`, the next one in line is `get-API`. While the former returns a list of records, 
the latter provides detailed information about one record only. The two APIs are very similar and most of the setting 
are the same. There are a few minor differences, between the two, and we are going to discuss those right now. 

First and foremost - how to use `get-API`. It is as simple as using the `list-API`, but instead of sending a request 
to the `baseURL/nameOfTheView`, we send it to `baseURL/nameOfTheView/recordID`. Here `recordID` matches the one in 
the database, therefore requesting a record is possible only by its database id.  

Previously we've discussed different sections for `list-API`. Namely: field, filter, join, order, group and limit 
sections. It should be clear, why most of those simply do not apply to `get-API` - most of them just don't make 
sense for requesting a single record. 


Already explained above: filter, = and join, key
New: key

## `HTTP-get` matched with `count-API`