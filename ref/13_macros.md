# Macros

Macros are a way to define reusable tresql snippets. They are used in tresql to define common patterns that are used in multiple places.
Macros can be defined as text in text file or in more complex cases within scala class.


## Macros in text file

Macros are defined in file named `tresql-macros.txt` in class path, for example `src/main/resources/tresql-macros.txt`.

Example of macros file:

```tresql
include /querease-macros.txt

has_role_user(userId::long, roleName::string)::boolean =
    exists(
      adm_user_role ur/adm_role_permission rc!/adm_permission p[
        p.code = $roleName &
        ur.adm_user_id = $userId &
        coalesce(ur.date_from, current_date) <= current_date &
        coalesce(ur.date_to, current_date) >= current_date])

has_role(roleName::string)::boolean =
    has_role_user(:current_user_id, $roleName)

ts_query(field::string, queryString::string)::boolean =
    `$field @@ (select to_tsquery(string_agg(u, ' & ')) from (select unnest(tsvector_to_array(to_tsvector('simple', $queryString))) || ':*' as  u) as u)`
```

Following macros are defined in the example above:

* `has_role_user` - checks if user has a specific role
* `has_role` - checks if current user has a specific role
* `ts_query` - creates a ts_query for full text search in postgresql

To lookup predefined macros you can see `querease-macros.txt` and `tresql-default-macros.txt` files provided by libraries

**TODO** maybe talk about syntax of macros

## Macros in scala class

Macros can also be defined in scala class. This is useful when you need to define more complex macros that require scala code.

There are predefined macros in `tresql` that can be used in tresql statements and view definitions.

Following macros are defined within `org.tresql.Macros` class:

* `sql` - include sql expression within tresql. Example `sql('select 1')`
* `containsVar` - checks if variable is present in the environment. 
* `if_defined` - checks if variable is defined and embeds expression if it is. Example `adm_users[if_defined(:name_if_def, name = :name_if_def)]{id}`
* `if_defined_or_else` - checks if variable is defined and embeds expression if it is, 
otherwise embeds another expression. Example `adm_users[if_defined_or_else(:name_if_def_or_else, name = :name_if_def_or_else, true)]{id}`
* `if_missing` - checks if variable is missing and embeds expression if it is. Example `adm_users[if_missing(:name_if_miss, true)]{id}`
* `if_all_defined` - checks if all variables are defined and embeds expression if they are. Example `adm_users[if_all_defined(:name_if_all_def, :surname_if_all_def, name = :name_if_all_def & surname = :surname_if_all_def)]{id}`
* `if_any_defined` - checks if any variable is defined and embeds expression if it is.
* `if_all_missing` - checks if all variables are missing and embeds expression if they are.
* `if_any_missing` - checks if any variable is missing and embeds expression if it is.
* `sql_concat` - **TODO** add description
* `bin_op_function` - **TODO** add description
* `dynamic_table` - **TODO** add description
* `map_exps` - **TODO** add description
* `concat_exps` - **TODO** add description



**TODO** add updated version of scala macros. 
