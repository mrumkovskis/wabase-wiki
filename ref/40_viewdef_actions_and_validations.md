# Actions

In addition to default save, list and other behaviors, developers can extend them or completely override them by providing custom code.

Actions can be defined for the following methods:

* get - get single record
* list - list array of records
* save - save record
* insert - insert record, if defined both save and insert, then insert is used for insert and save for update **TODO** test this
* update - update record, if defined both save and update, then update is used for update and save for insert **TODO** test this
* delete - delete record
* create - create record
* count - count number of records

Here is a basic example of save action:

```yaml
name: bank_account
table: account a
fields:
  - id
  - client_id
  - number  [+]:
  - balance [+]:
      - field api: readonly
  - currency
  - type
  - date_created [+]:
      - field api: readonly
  - date_closed
insert:
  - date_created = now()
  - balance = 0.0
  - insert this
```

In this case, insert action is used to set `date_created` and `balance` fields and then insert record. 


Awailable steps are:

* Evaluation - evaluate operation and assign it to variable in context (`name = :current_person_name`), **TODO** by default all operations are evaluations without assignment
* SetEnv - evaluate operation and override entire context where action is executed (`setenv {"JOHN" name, "DOE" surname}`)
* Return - return value from action (`return unique {"1234565" account_number}`), also terminates action if used in if block
* Validations - validate data within context
* RemoveVar - remove variable from context (`name -=`)

Within steps, the following operations can be used:

* tresql - execute tresql statement (`time = now()`)
* view call - do action for another view (`res = save person`)
* status - return http status code (`status ok {"Happy hour"}`, `status { 303, '/data', 'path', :status }`)
* redirect - redirect to another view, creates status 303 with location (`redirect {'person_health', :name, :manipulation_date::date, '?', 'val1' par1, 'val2' par2 }` creates location - `person_health/John/2020-01-01?val1=par1&val2=par2`)
* unique - extracts unique record from a list, otherwise throws error (`person = unique { 'John' name, 'Doe' surname }`)
* unique_opt - extracts unique record from list, otherwise returns null (`person = unique_opt { 'John' name, 'Doe' surname }`)
* invocation - call scala or java method (`res = org.wabase.QuereaseActionTestManager.concatStrings`)
* variable transforms - assign value to variable (`return (count = :c) + (data = :d)`)
* foreach - foreach loop block
* if, else - if else blocks
* resource - reads a file from classpath as `akka.util.ByteString` (`template = resource 'templates/person_template.txt'`)
* file - reads a file from file streamer as `akka.util.ByteString` (`template = file 'templates/person_template.txt'`), see [File streamer](../misc/80_files.md) for more details
* to file - writes file to file streamer (`to file accounts{number} 'account_numbers' 'application/json'`), see [File streamer](../misc/80_files.md) for more details
* template - generate template (`template 'Hello {{name}}!'`)
* email - sends email (`email ({'foo@bar.com' 'to', 'FOO', name}) (template 'Hello') (template 'Hello {{name}}!')`)
* http - make http call (`res = http get {'/not_decode_request_insert_test', '?', 'value' name }`)
* http header - extracts header from original request (`header = extract header X-Forwarded-For`)
* db - can open connection to database if connections are not initialized by setting flag `explicit db: true`, (`transaction save this`) **TODO** document "explicit db"
* conf - read config parameter (`app_name = conf string app.name`) **TODO** document "conf"
* json codec - encode or decode json (`person_object = from json :json_string`) (`json_string = to json :person_object`)
* block - group of actions
* job - run predefined job. (`job_res = job test_job`) See [Scheduling](../misc/10_scheduling.md)
* commit - commit transaction  (`commit`)
* extract parts - extract request parts for manual processing (`res = extract parts`)

## Forming response

Return statement:

**TODO not the best example since we are forced to follow view model**

```yaml
name: bank_account_list_with_count
table:
api: manage_bank_clients list
comments: Returns list of page of bank accounts with total count
fields:
  - count: int
  - data *:
    table: account a
    fields:
      - id
      - number
      - balance
      - currency
      - type
      - client_id
      - date_created
      - date_closed
    order:
      - id
    filter:
      - client_id
list:
  - c: count bank_account_list_with_count_data
  - d:   list bank_account_list_with_count_data
  - return (count = :c) + (data = :d)
```

Response as option from a list, in this case custom select:

```yaml
name: bank_account_get_with_custom_select
table: account a
api: manage_bank_clients get
comments: Bank account details with custom select
fields:
  - id
  - number
  - balance
  - custom_field string =
get:
  - unique_opt account[id = :id] {id, number, balance, 'custom_value' custom_field}
```

Unique option can be used from a list call:


```yaml
name: bank_account_list
table: account a
fields:
  - id
  - number
  - balance
filter:
  - client_id

name: bank_account_by_client
table: account a
api: manage_bank_clients get
comments: Bank account details with unique option
fields:
  - id
  - number
  - balance
get:
  - client_id = :id
  - unique_opt list bank_account_list
```



Returning variable as response:

**TODO test this example**

```yaml
name: invocation_test_1
table:
api: get, save, count, delete, create
fields:
- s1
- s2
- n1
- n2
get:
- setenv create this
- s3 = org.wabase.QuereaseActionTestManager.concatStrings
- status ok { :s3 }
```

Returning variable as response second example:

**TODO test this example**

```yaml
name: http_test_2
table:
key: name
api: get, list, save
fields:
- id
- name
- manipulation_date
- vaccine
get:
- unique {:name name, :manipulation_date::date manipulation_date, :vaccine vaccine, :id::long id}
list:
- res = http '/invocation_test_1'
- status ok :res
```

Redirect examples:

**TODO test this example**

```yaml
name:   status_test_3
table:
api:    get, save, list, count
fields:
  - id
save:
  - status { 303 }
get:
  - redirect { 'data/path/' || :id }
list:
  - status { 303, 'data/path', :id, '?', 'val-of-par1' par1 }
count:
  - redirect { null, :id, '?', (:id + 1) par1 }
```

## View call

Example how to call view itself:

**TODO test this example**

```yaml
name: person_list
table: person
fields:
  - id
  - name
  - surname
  - sex
  - birthdate
filter:
  - name %~~%
  - surname %~~%
list:
  - c: count this   # named step supports also colon notation
  - d: list  this   # named step supports also colon notation
  - return (count = :c) + (data = :d)
```

**TODO** - is supper save action executed by default?

**TODO test this example**

```yaml
name: persona_base
table: person
fields:
  - id
  - name
save:
  - name = 'Mr ' || :name
  - save this
    
name: persona
extends: persona_base
fields:
  - surname
  - birthdate
save:
  - save persona_base
  - save this
```

**TODO** how to forward parameters to view call?, Martins thinks that it will work

**TODO test this example**

```yaml
name: do_audit
table: audit
fields:
  - id 
  - data
  - timestamp
save:
  - timestamp = now()
  - save this
    
name: persona
extends: persona_base
fields:
  - surname
  - birthdate
save:
  - (data = :surname) -> save do_audit
  - save this
```


**TODO** - discuss when permissions are checked

## Flow control

Foreach block allows you to iterate over list of items within context and execute block of statements for each item.
Within block context is changed to item in list, parent context can be accessed with `:'..'`.

**TODO** - should we talk about `build cursors`?

**TODO test this example**

```yaml
name: foreach_test_1
table: if_and_foreach_test ift
api: get, save
key: code
fields:
- code
- parent
- value
- children[!] * [:1(code) = parent]foreach_test_1
save:
- insert this
- foreach :children :
    - parent = :'..'.code
    - insert this
- get this
update:
- update this
- foreach :children :
    - parent = :'..'.code
    - update this
- get this
```

If block allows to create conditional blocks of statements.
If block contains else block as well then result can be returned from if block.

**TODO test this example**

```yaml
name: if_test_1
table: if_and_foreach_test
api: save, get, delete
fields:
- code
- parent
- value
save:
- code = 'if_test_1'
- if {:value = 'yes'} :
    - value = :value || '_value'
    - +if_and_foreach_test{code = :code, value = :value}
- if {:value = 'no'} :
    - value = :value || '_value'
    - =if_and_foreach_test[code = :code]{value = :value}
- unique_opt if_and_foreach_test[code = :code]{code, value}
get:
- result = if {:code = 'true'}:
  - unique { :code code, null parent, 'Value' value }
- else:
  - unique { :code code, null parent, 'Else value' value }
- :result
delete:
- result:
  - if {:code = 'true'}:
    - unique { :code code, null parent, 'Value delete' value }
  - else:
    - unique { :code code, null parent, 'Else value delete' value }
- :result
```

## Doing http call

Http call can be made based on action context and result can be used in further steps.
Following parameters can be provided:

* conformTo - expected response (`as forest`) **TODO**
* method - http method (`get`, `post`, `put`, `delete`)
* url - tresql expression that returns url
* body - optional, tresql expression that returns body
* headers - optional, tresql expression that returns headers

**TODO test this example**

```yaml
name: form_urlencoded_test
table:
api: insert, get
fields:
  - name
  - surname
get:
  - res =
      http post
        {'/form_urlencoded_test'}
        { :name name, :surname surname }
        { 'Content-Type', 'application/x-www-form-urlencoded' }
  - :res
insert:
- status ok { :name || ' ' || :surname }
```

Example how to use response from http call to make another http call:

**TODO test this example**

```yaml
name: not_decode_request_insert_test
table:
decode request: false
api: insert, update, get
fields:
- name
get:
- unique { :name name }
insert:
- status ok { if_defined_or_else(:name?, 'error', 'ok') }
update:
- res =
    http post { '/not_decode_request_insert_test' }
      http get {'/not_decode_request_insert_test', '?', 'value' name }
- :res
```

**TODO test this example**

```yaml
name: forest
table: forest
key: nr
api: get, save, list
fields:
- nr
- owner = owner.name -> = owner[name = _] {id}
- area
- trees
save:
- save this as forest http { '/http_forest', '?', :nr nr, :owner owner, :area area, :trees trees }
```
## Sending email and templates

Template generates text from template string and variables by using mustache templating language.  
**TODO** Can I change template language?

> varam nomainīt, template engine jāpameklēt

Template takes following parameters:

* template - template string
* filename - optional filename for attachment, or content-disposition header
* data - By default, it uses context variables, but it can be provided with specif context in `data` parameter.


**TODO test this example**

```yaml
name: template_test1
table:
api: get, list, insert, update, delete
fields:
- name
get:
- res = template 'Hello {{name}}!'
list:
- res = template 'Hello {{title}}. {{name}}!' {:name name, 'Ms' title}
insert:
- res = template 'Hello {{name}}!' filename='file name'
update:
- res = template 'Hello {{name}} in {{action}}!' filename='file name' data={:name name, 'update' action}
delete:
- res = template 'Hello {{name}} in {{action}}!' data={:name name, 'delete' action}
```

To send email you must provide following parameters:

(**TODO** - can we return multiple rows?)
> > jā, bet varbūt vajag email batch operāciju lai netīšām nenosūta visai datubāzei epastus un defaultais email met eroru ja vairāk kā viena rinda

* emailTresql - tresql select statement that returns data for email
* subject - subject string for email, the example contains template
* body - body string for email, the example contains template
* attachmentsOp - rest of parameters are list of attachments

Following example sends email with three attachments:

**TODO test this example**

```yaml
name: email_test1
table:
api: get, insert
fields:
- attachment
get:
- attachment = { 'attachment from http for ' || trim(:name) }
- :attachment
insert:
- file = to file {'attachment from file' attachment} 'file_attachment'
- email
    ({ 'a@a.a' 'to', 'Hannah' name } + { 'b@b.b' 'to', 'Baiba' name })
    (template 'Subject for {{name}}!')
    (template 'Content for {{recipient}}.' {trim(:name) recipient})
    (http { '/email_test1', '?', :name name })
    (file {:file.id, :file.sha_256})
    (template 'Template attachment for {{name}}' {trim(:name) name} 'attachment name')
```

## Scala method call

Scala method may be called from view definition.

**todo** - explain parameter passing

```yaml
name: user_edit
table: adm_user l
fields:
  - id
  - name
  - pwd =
  - passwd:
      - field api: excluded
save:
  - passwd = org.wabase.Authentication.passwordHash :pwd
  - save this
```


Scala method receives context as parameter and can return value to be used in view definition.
Context can be defined as scala or java map or dto generated by querease.

Additional parameters can be specified as well:

**TODO** - single primitive parameter
* `scala.concurrent.ExecutionContext` - execution context for async operations
* `akka.actor.ActorSystem` - actor system to make actor calls
* `akka.http.scaladsl.model.HttpRequest` - request object that initiated the call
* `org.tresql.Resources` - tresql resources object to get connection to db
* `org.wabase.ResourcesFactory` - factory to get resources
* `org.wabase.FileStreamer` - file streamer to read and write files
* `org.wabase.RequestContext` - request context with parsed user, params ar view name
* `org.wabase.AppQuereaseIo` - querease io object to make db calls and access metadata
* `org.wabase.RequestPartResult` - part of request from `extract parts` statement


Parameters can be defined in arbitrary order as implicit or not.

The Result of the method can be:

* `null` or `Unit` - no return value
* `org.tresql.Result` - result of tresql statement
* `org.tresql.RowLike` - result of tresql query
* `Dto` - dto generated by querease
* `Option[Dto]` - optional dto generated by querease **TODO** - describe behavior if None
* `Map[String, Any]` - map of values that can be used in view definition
* `akka.http.scaladsl.model.HttpResponse` - http response to be returned to client
* `org.wabase.QuereaseResult` - result of querease operation
* `Iterator[_]`, `Iterable[_]`, `Array[_]` - collection of the above
* `Future[_]` - future of the above, view will wait for future to complete




**TODO** - Explain how result is assigned to view context (`s3 =` vs `set env` vs `return`)

Examples of method signatures:

```scala
def personSaveBizMethod(data: Map[String, Any]): Map[String, Any]
def personSaveBizMethod(data: Map[String, Any], request: HttpRequest): Map[String, Any]

def personSaveBizMethod(data: Map[String, Any], request: HttpRequest, resources: Resources): Map[String, Any]
def personSaveBizMethod(data: Map[String, Any])(implicit resources: Resources): Map[String, Any]
def personSaveJavaMapBizMethod(data: java.util.Map[String, Any])

def personSaveBizMethod(data: PersonSaveDto): Future[PersonSaveDto]
```

If a parameter is defined as a map, then full context is passed to method. However, specific variable can be passed instead:

**TODO test this example**

```yaml 
name: invocation_test_2
table:
api: get, list, save, delete
key: key
fields:
  - key:
      - initial: "'key_val'"
  - value:
      - initial: "'value_val'"
get:
  - x = create this
  - :x -> org.wabase.QuereaseActionTestManager.rowLikeMethod
```
In this case `->` sign is used to pass variable `x` to method as map.


**TODO** What is `as` statement? :

> kāsto uz kādu dto vai listu ar dto

**TODO test this example**

```yaml
get:
- as any org.wabase.QuereaseActionTestManagerObj.int_array # no special type
```

## Advanced request processing

By default, HTTP POST and PUT requests are decoded and passed as Map of keys and values into context.
To switch off decoding and process request manually, set `decode request: false` in view definition.
Multipart request can be decoded with `extract parts` statement, it returns list of `RequestPartResult` objects.

`RequestPartResult` contains following fields:

* `name` - name of part
* `contentType` - content type of part
* `filename` - filename of part
* `data` - data of part as `akka.stream.scaladsl.Source[ByteString, Any]`

Example of extracting parts:

**TODO test this example**

```yaml
name: extract_parts_test
decode request: false
table:
key: none
api: insert, update
fields:
- none
  insert:
- res = extract parts
- as any
  { 'field1' name, :res.field1 value } ++
  { 'field2' name, :res.field2 value } ++
  { :res.file1.filename name, :res.file1.sha_256 value } ++
  { :res.file2.filename name, :res.file2.sha_256 value }
  update:
- res = extract parts
- as any unique { :res.filename file, :res.sha_256 sha_256 }
```

**TODO** none key is not needed, but it is needed for now

`RequestPartResult` can be passed to scala class method as well:

**TODO test this example**

```yaml
name: extract_parts_test2
decode request: false
table:
api: insert
insert:
- as any org.wabase.QuereaseActionTestManagerObj.processRequestParts extract parts
```

```scala
def processRequestParts(res: RequestPartResult)(implicit as: ActorSystem, ec: ExecutionContext)
```

# Validations

Validations are used to validate data before it is saved. They can be specified as separate block in view:

Each validation consists of tresql statement that is executed in context of view and error message.
If statement returns false, then error message is returned to client with given message.

Within validation block following operations can be used:

* build cursors - creates cursors for nested structures within context that can be used in query
* tuple - where first element is tresql statement that returns boolean value, second is error message
* triple - where first element is tresql statement that prepares data for second element,  second element is tresql statement that returns boolean value, third is error message

**TODO test this example**

```yaml
name: person
api: save, get
table: person
fields:
- id
- name
- surname
- sex
- birthdate
- accounts * :
    table: account
    fields:
    - id
    - number
    - balance
    - last_modified
validations:
  - build cursors
  - ac(# c) { accounts {count(*)} }, (ac{c}) > 0, "person must have at least one account"
```

Or can be specified within action. In this case each validation block can be named.
In this case validation block can be named and it can be used programmatically in code to generate proper response .

**TODO** - examples of name in response

**TODO test this example**

```yaml
name: payment
api: save, get
table: transaction tr
joins:
- tr[tr.originator_id = originator.id]account originator?
- tr[tr.beneficiary_id = beneficiary.id]account beneficiary?
fields:
- id
- originator  = originator.number  -> = account [number = _] { id }
- beneficiary = beneficiary.number -> = account [number = _] { id }
- amount
- date_time -> = :time :
  - initial: now()
save:
- validations amount:
  - :amount > 0, 'Wrong amount ' || :amount || '. Amount must be greater than 0'
- validations balance:
  - balance(# s) { account[number = :originator] {balance} }, ((balance{s}) = null | (balance{s}) >= :amount),
      "Insufficient funds for account '" || :originator || "'"
- time = now()
- save this
- =account[number = :originator] { balance, last_modified } [balance - :amount, :time]
- =account[number = :beneficiary] { balance, last_modified } [balance + :amount, :time]
- :id
```

