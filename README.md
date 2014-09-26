SQL code smells
===============

>*This is still a work in progress - we'll add more of the book over the next few days.*

#Contents

- [Introduction](Introduction)
- [Problems with Database](Problems_With_Database_Design)
- [Problems with Table Design](Problems_with_Table_Design)
- [Problems with Data Types](Problems_with_Data_Types)
- [Problems with Expressions](Problems_with_Expressions)
- [Difficulties with Query Syntax](Difficulties_with_Query_Syntax)
- [Problems with Naming](Problems_with_Naming)
- [Problems with Routines](Problems_with_Routines)
- [Security Loopholes](Security_Loopholes)
- [Acknowledgements](Acknowledgements)

<a name="Introduction"></a>
#Introduction 
**Once you’ve done a number of SQL code-reviews, you’ll be able to identify signs in the code that indicate all might not be well. These ‘code smells’ are coding styles that, while not bugs, suggest design problems with the code.**

Kent Beck and Massimo Arnoldi seem to have coined the term ‘CodeSmell’ in the ‘[Once And Only Once](http://www.c2.com/cgi/wiki?OnceAndOnlyOnce)’ page of www.C2.com, where Kent also said that code ‘wants to be simple’. Kent Beck and Martin Fowler expand on the issue of code challenges in their essay ‘Bad Smells in Code’, published as Chapter 3 of the book ‘Refactoring: Improving the Design of Existing Code’ (ISBN 978-0201485677).

Although there are generic code smells, SQL has its own particular habits that will alert the programmer to the need to refactor code. (For grounding in code smells in C#, see ‘[Exploring Smelly Code](https://www.simple-talk.com/dotnet/.net-framework/exploring-smelly-code/)’ and ‘[Code Deodorants for Code Smells](https://www.simple-talk.com/dotnet/.net-framework/code-deodorants-for-code-smells/)’ by Nick Harrison.) Plamen Ratchev’s wonderful article ‘[Ten Common SQL Programming Mistakes](https://www.simple-talk.com/sql/t-sql-programming/ten-common-sql-programming-mistakes/)’ lists some of these code smells along with out-and-out mistakes, but there are more. The use of nested transactions, for example, isn’t entirely incorrect, even though the database engine ignores all but the outermost, but their use does flag the possibility the programmer thinks that nested transactions are supported.

If you are moving towards continuous delivery of database applications, you should automate as much as possible the preliminary SQL code-review. It’s a lot easier to trawl through your code automatically to pick out problems, than to do so manually. Imagine having something like the classic ‘lint’ tools used for C, or better still, a tool similar to [Jonathan ‘Peli’ de Halleux’s](https://www.simple-talk.com/opinion/geek-of-the-week/peli-de-halleux-geek-of-the-week/) Code Metrics plug-in for .NET Reflector, which finds code smells in .NET code.

One can be a bit defensive about SQL code smells. I will cheerfully write very long stored procedures, even though they are frowned upon. I’ll even use dynamic SQL on occasion. You should use code smells only as an aid. It is fine to ‘sign them off’ as being inappropriate in certain circumstances. In fact, whole classes of code smells may be irrelevant for a particular database. The use of proprietary SQL, for example, is only a code smell if there is a chance that the database will be ported to another RDBMS. The use of dynamic SQL is a risk only with certain security models. Ultimately, you should rely on your own judgment. As the saying goes, a code smell is a hint of possible bad practice to a pragmatist, but a sure sign of bad practice to a purist.

In describing all these 119 code-smells in a booklet, I’ve been very constrained on space to describe each code smell. Some code smells would require a whole article to explain them properly. Fortunately, SQL Server Central and Simple-Talk have, between them, published material on almost all these code smells, so if you get interested, please explore these essential archives of information.

	-*Phil Factor, _Contributing Editor_*

#Problems with Database Design <a name="Problems_With_Database_Design"></a>
##1) Packing lists, complex data, or other multivariate attributes into a table column

It is permissible to put a list or data document in a column only if it is, from the database perspective, ‘atomic’, that is, never likely to be shredded into individual values; in other words, as long as the value remains in the format in which it started. We store strings, after all, and a string is hardly atomic since it consists of an ordinally significant collection of characters or words. A list or XML value stored in a column, whether by character map, bitmap or XML data type, can be a useful temporary expedient during development, but the column will likely need to be normalized if values will have to be shredded.

A related code smell is:

###Using inappropriate data types
Although a business may choose to represent a date as a single string of numbers or require codes that mix text with numbers, it is unsatisfactory to store such data in columns that don’t match the actual data type. This confuses the presentation of data with its storage. Dates, money, codes and other business data can be represented in a human-readable form, the ‘presentation’ mode, they can be represented in their storage form, or in their data-interchange form. 

Storing data in the wrong form as strings leads to major issues with coding, indexing, sorting, and other operations. Put the data into the appropriate ‘storage’ data type at all times.

##2) Storing the hierarchy structure in the same table as the entities that make up the hierarchy
Self-referencing tables seem like an elegant way to represent hierarchies. However, such an approach
mixes relationships and values. Real-life hierarchies need more than a parent-child relationship. The ‘Closure Table’ pattern, where the relationships are held in a table separate from the data, is much more suitable for real-life hierarchies. Also, in real life, relationships tend have a beginning and an end, and this often needs to be recorded. The HIERARCHYID data type and the common language runtime (CLR) SqlHierarchyId class are provided to make tree structures represented by self-referencing tables more efficient, but they are likely to be appropriate for only a minority of applications.

##3) Using an Entity Attribute Value (EAV) model
The use of an EAV model is almost never justified and leads to very tortuous SQL code that is extraordinarily difficult to apply any sort of constraint to. When faced with providing a ‘persistence layer’ for an application that doesn’t understand the nature of the data, use XML instead. That way, you can use XSD to enforce data constraints, create indexes on the data, and use XPath to query specific elements within the XML. It is then, at least, a reliable database, even though it isn’t relational!

##4) Using a polymorphic association
Sometimes, one sees table designs which have ‘keys’ that can reference more than one table, whose identity is usually denoted by a separate column. This is where an entity can relate to one of a number of different entities according to the value in another column that provides the identity of the entity. This sort of relationship cannot be subject to foreign key constraints, and any joins are difficult for the query optimizer to provide good plans for. Also, the logic for the joins is likely to get complicated. Instead, use an intersection table, or if you are attempting an object-oriented mapping, look at the method by which SQL Server represents the database metadata by creating an ‘object’ supertype class that all of the individual object types extend. Both these devices give you the flexibility of design that polymorphic associations attempt.

##5) Creating tables as ‘God Objects’
‘God Tables’ are usually the result of an attempt to encapsulate a large part of the data for the business domain in a single wide table. This is usually a normalization error, or rather, a rash and over-ambitious attempt to ‘denormalize’ the database structure. If you have a table with many columns, it is likely that you have come to grief on the third normal form. It could also be the result of believing, wrongly, that all joins come at great and constant cost. Normally they can be replaced by views or table-valued functions. Indexed views can have maintenance overhead but are greatly superior to denormalization. 

##6) Contrived interfaces
Quite often, the database designer will need to create an interface to provide an abstraction layer between schemas within a database, between database and ETL processes, or between a database and application. You face a choice between uniformity, and simplicity. Overly complicated interfaces, for whatever reason, should never be used where a simpler design would suffice. It is always best to choose simplicity over conformity. Interfaces have to be clearly documented and maintained, let alone understood.

##7) Using command-line and OLE automation to access server-based resources 
In designing a database application, there is sometimes functionality that cannot be done purely in SQL, usually when other server-based, or network-based resources must be accessed. Now that SQL Server’s integration with PowerShell is so much more mature, it is better to use that, rather than xp_cmdshell or sp_OACreate (or similar), to access the file system or other server-based resources. This needs some thought and planning. You should also use SQL Agent jobs when possible to schedule your server-related tasks. This requires up-front design to prevent them becoming unmanageable monsters prey to ad-hoc growth. 


#Problems with Table Design <a name="Problems_with_Table_Design"></a>
#Problems with Data Types <a name="Problems_with_Data_Types"></a>
#Problems with Expressions <a name="Problems_with_Expressions"></a>
#Difficulties with Query Syntax <a name="Difficulties_with_Query_Syntax"></a>
#Problems with Naming <a name="Problems_with_Naming"></a>
#Problems with Routines <a name="Problems_with_Routines"></a>


#Security Loopholes <a name="Security_Loopholes"></a>

##115) Using SQL Server logins, especially without password expirations or Windows password policy  
Sometimes you must use SQL Server logins. For example, with Microsoft Azure SQL Database, you have no other option, but it isn’t satisfactory. SQL Server logins and passwords have to be sent across the network and can be read by sniffers. They also require passwords to be stored on client machines and in connection strings. SQL logins are particularly vulnerable to bruteforce attacks. They are also less convenient because the SQL Server Management Studio (SSMS) registered servers don’t store password information and so can’t be used for executing SQL across a range of servers. Windows-based authentication is far more robust and should be used where possible.

##116) Using the xp_cmdshell system stored procedure 
Use xp_cmdshell in a routine only as a last resort, due to the elevated security permissions they require and consequential security risk. The xp_cmdshell procedure is best reserved for scheduled jobs where security can be better managed.

##117) Authentication set to Mixed Mode
Ensure that Windows Authentication Mode is used wherever possible. SQL Server authentication is necessary only when a server is remote or outside the domain, or if third-party software requires SQL authentication for remote maintenance. Windows Authentication is less vulnerable and avoids having to transmit passwords over the network or store them in connection strings.

##118) Using dynamic SQL without the EXECUTE AS clause 
Because of ownership chaining and SQL injection risks, dynamic SQL requires constant vigilance to ensure that  it is used only as intended. Use the EXECUTE AS clause to ensure the dynamic SQL code inside the procedure is  executed only in the context you expect.

##119) Using dynamic SQL with the possibility of SQL injection 
SQL injection can be used not only from an application  but also by a user who lacks, but wants, the permissions  necessary to perform a particular role, or who simply  wants to access sensitive data. If dynamic SQL is  executed within a stored procedure, under the temporary EXECUTE AS permission of a user with sufficient privileges to create users, it can be accessed  by a malicious user. Suitable precautions must be taken  to make this impossible. These precautions start with  giving EXECUTE AS permissions only to WITHOUT  LOGIN users with least-necessary permissions, and using sp_ExecuteSQL with parameters rather than EXECUTE.



#Acknowledgements <a name="Acknowledgements"></a>
###For a booklet like this, it is best to go with the established opinion of what constitutes a SQL Code Smell. There is little room for creativity. In order to identify only those SQL coding habits that could, in some circumstances, lead to problems, I must rely on the help of experts, and I am very grateful for the help, support and writings of the following people in particular. 
Dave Howard
Merrill Aldrich
Plamen Ratchev
Dave Levy
Mike Reigler
Anil Das
Adrian Hills
Sam Stange
Ian Stirk
Aaron Bertrand
Neil Hambly
Matt Whitfield
Nick Harrison
Bill Fellows
Jeremiah Peschka
Diane McNurlan
Robert L Davis
Dave Ballantyne
John Stafford
Gail Shaw
Alex Kusnetsov
Jeff Moden
Joe Celko
Robert Young

And special thanks to our technical referees, Grant Fritchey and Jonathan Allen.
