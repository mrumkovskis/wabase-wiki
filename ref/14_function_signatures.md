# Function signatures

Function signatures are used to define functions that are available in database can be used in tresql queries.
Tresql defines a set of built-in functions, but you can define your own functions as well:


```
include /wabase-function-signatures.txt

greatest(pars::'*')::'$pars'
least(pars::'*')::'$pars'

substring(str::string, from::int, len::int)::string
```

File must be located in class path and named `tresql-function-signatures.txt` for example:  `src/main/resources/tresql-function-signatures.txt`

Usage in yaml:

```yaml
  - last_activity = greatest(date_created, date_updated, last_login) : Last activity
  - initials = substring(name, 1, 1) || substring(surname, 1, 1) : Initials
```