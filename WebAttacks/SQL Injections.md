We can use different methods to subvert / prematurely end the query and insert what we want the engine to do for us, first, we need to guess what kind of query at the back is being executed and where our statement is inserted, below is some method for us to do injections.

-Using ' to terminate term:

For example our query is:
```
select * from logins where username like '%input'
```

We need to first input ' to terminate the term %input first, then we can input statement that we like (for the above example, we require more techniques for it to actually work)

-Using or to subvert the queue logic:


