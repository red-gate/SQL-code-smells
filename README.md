SQL code smells
===============

>*This is still a work in progress - we'll add more of the book over the next few days.*

#Contents

- Introduction
- Problems with Database
- Design
- Problems with Table
- Design
- Problems with Data Types
- Problems with Expressions
- Difficulties with Query
- Syntax
- Problems with Naming
- Problems with Routines
- Security Loopholes
- Acknowledgements

#Introduction
**Once you’ve done a number of SQL code-reviews, you’ll be able to identify signs in the code that indicate all might not be well. These ‘code smells’ are coding styles that, while not bugs, suggest design problems with the code.**

Kent Beck and Massimo Arnoldi seem to have coined the term ‘CodeSmell’ in the ‘[Once And Only Once](http://www.c2.com/cgi/wiki?OnceAndOnlyOnce)’ page of www.C2.com, where Kent also said that code ‘wants to be simple’. Kent Beck and Martin Fowler expand on the issue of code challenges in their essay ‘Bad Smells in Code’, published as Chapter 3 of the book ‘Refactoring: Improving the Design of Existing Code’ (ISBN 978-0201485677).

Although there are generic code smells, SQL has its own particular habits that will alert the programmer to the need to refactor code. (For grounding in code smells in C#, see ‘[Exploring Smelly Code](https://www.simple-talk.com/dotnet/.net-framework/exploring-smelly-code/)’ and ‘[Code Deodorants for Code Smells](https://www.simple-talk.com/dotnet/.net-framework/code-deodorants-for-code-smells/)’ by Nick Harrison.) Plamen Ratchev’s wonderful article ‘[Ten Common SQL Programming Mistakes](https://www.simple-talk.com/sql/t-sql-programming/ten-common-sql-programming-mistakes/)’ lists some of these code smells along with out-and-out mistakes, but there are more. The use of nested transactions, for example, isn’t entirely incorrect, even though the database engine ignores all but the outermost, but their use does flag the possibility the programmer thinks that nested transactions are supported.

If you are moving towards continuous delivery of database applications, you should automate as much as possible the preliminary SQL code-review. It’s a lot easier to trawl through your code automatically to pick out problems, than to do so manually. Imagine having something like the classic ‘lint’ tools used for C, or better still, a tool similar to [Jonathan ‘Peli’ de Halleux’s](https://www.simple-talk.com/opinion/geek-of-the-week/peli-de-halleux-geek-of-the-week/) Code Metrics plug-in for .NET Reflector, which finds code smells in .NET code.

One can be a bit defensive about SQL code smells. I will cheerfully write very long stored procedures, even though they are frowned upon. I’ll even use dynamic SQL on occasion. You should use code smells only as an aid. It is fine to ‘sign them off’ as being inappropriate in certain circumstances. In fact, whole classes of code smells may be irrelevant for a particular database. The use of proprietary SQL, for example, is only a code smell if there is a chance that the database will be ported to another RDBMS. The use of dynamic SQL is a risk only with certain security models. Ultimately, you should rely on your own judgment. As the saying goes, a code smell is a hint of possible bad practice to a pragmatist, but a sure sign of bad practice to a purist.

In describing all these 119 code-smells in a booklet, I’ve been very constrained on space to describe each code smell. Some code smells would require a whole article to explain them properly. Fortunately, SQL Server Central and Simple-Talk have, between them, published material on almost all these code smells, so if you get interested, please explore these essential archives of information.

	-*Phil Factor, _Contributing Editor_*

#Problems with Database
#Design
#Problems with Table
#Design
#Problems with Data Types
#Problems with Expressions
#Difficulties with Query
#Syntax
#Problems with Naming
#Problems with Routines
#Security Loopholes
#Acknowledgements