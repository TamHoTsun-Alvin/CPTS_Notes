We can use different methods to subvert / prematurely end the query and insert what we want the engine to do for us, first, we need to guess what kind of query at the back is being executed and where our statement is inserted, below is some method for us to do injections.

-Using ' to terminate term:

For example our query is:
```
select * from logins where username like '%input'
```

We need to first input ' to terminate the term %input first, then we can input statement that we like (for the above example, we require more techniques for it to actually work)

-Using or to subvert the queue logic:

A common case to use or in subverting queue logic is to perform auth bypass, consider the following statement:

```
SELECT * FROM logins WHERE username='<username>' AND password = '<pw>';
```

if we happen to know a valid username, then what we need to do is to have the query always evaluate to true, we can then subvert the logic by adding a 1=1:

```
SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something';
```

The 1=1 AND password = something would evaluate to false, but the username = admin or false will evaluate to true, at this case we can login as long as the username we provide is valid

However, if we do not know any user and we just want to get ourselves into the system, what we want to do is do further subvert the query by adding or at the password side as well, so that the query becomes:
```
SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something' or '1'= '1';
```
Usually, when inserting query, usually we would do something like a '1' = '1, notice that the final singlequote is not present as most likely we would be finishing that with the singlequote the one that comes from the prepared query.

-Using Comments to terminate query early

Somtimes, the query itself may perform different checks aside from checking if the information provided is correct, with the use of comments, we can possibly terminate the query early, allowing us to perform auth bypass, below is an example:

```
SELECT * FROM logins WHERE (username='<input1>' AND ........) AND password = '<hashofinput2>'
```

We can see that input2 is hashed, so we can't really inject it in there, also there is additional check after input 1, however, if we are able to guess the queue structure, use comments to terminate the conditions early and provide back all essential symbols, we can auth bypass, below is the correct payload on input1:

```
admin') -- -
```
Notice that we actually gave back 1 ' and 1 ) before terminating with -- , also notice that the correct symbol for terminating with comment is -- , where it is composed by 2 - and 1 space, the - after the comment symbol is only for demo purpose

-Union Injection:

Union select is a function that allows us to "combine" queries into same output, for example:
```
SELECT * FROM ports UNION SELECT * FROM ships;
```

The following queries combine the select result from ports and ships and output them into the same columns, by using union and other techniques, we can perform union injection and enumerate some properties regarding current database

-Columns Enumeration by 