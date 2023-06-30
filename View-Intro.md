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
comment: this is 
table: bank
fields:
- id:
    - comment
- code
- country_code
- name
```

In this definition we can see a view which gives some basic information about a bank.
Firstly, we define the name of the view `bank_list_row`.  Next up, we set the source table `bank` and the fields of the
view `id`, `code`, `country_code` and `name` are defined. Finally, we define preferred order of the view, which is by 
the `id`. When requested with `list` API, the returned JSON would look something like this


**#TODO** add example and ask if it's possible to see the responses from tests myself. Maybe a test project with 
basic back-end to play with. MAP - Instance


> 1: {id: 1, code: "LVHABA-1", country_code: 1, name: "Swedbank"}
> 
> 
> 
> 
> 
>








