# Jobs

Wabase provides option to define jobs in the `.yaml` files. Jobs are used to run some kind of background tasks, 
like sending emails, cleaning up the database, etc.

Example of job definition:

```yaml
job: test_job1
action:
- unique { 'Hello ' || coalesce(if_defined(:name?, :name || ' '), '') || 'from test_job1!' greeting }
```

Where:

* `job` - job name
* `action` - list of actions to be performed by the job, see [Viewdef Actions](../ref/30_viewdef.md#actions) 
for more information for available actions. 

As it is with views, each action updates the context that is passed to the next action. Initial context is empty if job is scheduled,
but if job is called from view, then context is filled with view context.

**TODO** Could not find information how to schedule the job.

In addition to scheduling jobs, they can be run manually in view action with 'job' operation.

```yaml
name: job_call_test1
table:
api: get, list
fields:
  - name
get:
  - job_res = job test_job1
  - status ok :job_res.greeting
```