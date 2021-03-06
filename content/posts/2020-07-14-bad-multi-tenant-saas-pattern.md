title = "The Bad Multi-Tenant SAAS Pattern"
date = 2020-07-14T17:47:00+02:00
tags = [
    "saas",
    "multitenant",
    "pattern"
]
published = true
+++++

Note: This article assumes PostgreSQL.

Multi-tenant web apps are currently all the rage. Just take a look at the top tech stocks for July of 2020. Adobe, Salesforce, Microsoft... most if not all of them provide some sort of software as a service. SAAS has taken the web by storm, but there is one specific hidden pattern that is being taught that I MUST ADDRESS.

The majority of data access strategies for multi-tenant applications are garbage. If you search the web, you'll find lots of articles giving multiple solutions to this problem. Let's go ahead and list the main ones below.

- Shared database and shared schema.
- Shared data and separate schemas per tenant.
- Separate databases per tenant.

From the list above the bottom two are DEAD wrong. You might wonder.. but why Let me dive in deep.

#### First up is database resources.

Since Postgres defines one file on disk per table, you will have a hard limit at the maximum amount of files allowed in a single folder. What is that limit? Heck, do you even know what operating system your database server is even running on? How good is your kernel at handling this madness?

#### Next, lets talk about migrations. 

SAAS apps use migrations. It's the easiest way to make changes to the database schema while keeping track of them all. This also forces us, developers, to think more in-depth about our changes since they will go through peer reviews. Imagine having to run a migration on a few of your tenant's databases or schemas.. not horrible but the computational complexity for a multi-database or shame approach raises the migration bar from O(1) to O(n), where `n` is the number of tenants you have. Now imagine your app takes off.. grows to a couple of thousand tenants. That's quite the wrench in your CD/CI process. Might as well use the waterfall methodology from this point forward.

#### Issues with Active Records ORMs.

ORMs are a tool that almost every app uses nowadays, they are the essential workers of the SAAS world. ORMs that follow the Active Record pattern have one main thing in common, they all reach out to the database to load up a metadata blob full of them juicy table details. This will dramatically increase your process size when starting up your production servers. Thus unnecessarily bloating your app servers. Say bye-bye to those AWS Dollar bills.

#### Global Reports

Someone from the business side of the house wants a report that contains stats from all of your tenants... Good luck. Who would have realized that you now have duplicate ids across your `accounts` tables? Joins be damned.

#### Conclusion

Hopefully, I've given you a little insight into why you shouldn't start that multi-tenant app using multiple databases or schemas. Yet if you must, I'd love to know how you solved my list of problems. Please leave me a comment with some feedback. Perhaps I missed a pain-point you had to deal with. Enlighten me.


#### Sources:
- https://www.barrons.com/articles/3-software-stocks-that-could-be-big-winners-in-2020-51579005001

- https://blog.lftechnology.com/designing-a-secure-and-scalable-multi-tenant-application-on-node-js-15ae13dda778

- https://stackify.com/writing-multitenant-asp-net-core-applications/

- https://medium.com/@venkateshpnk22/how-to-build-a-multi-tenant-application-with-django-rest-framework-part-1-8176d3e315cf

- https://dev.to/kspeakman/breaking-postgres-with-too-many-tables-4pg0

Originally Posted on [dev.to](https://dev.to/mrbrazel/the-bad-multi-tenant-saas-pattern-20b4)
