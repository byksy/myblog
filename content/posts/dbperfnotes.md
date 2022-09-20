---
title: "Database Performance Notes"
date: 2022-09-19T17:20:08+03:00
draft: false
categories: ["database performance"]
slug: database-performance-notes
cover: 
    image: img/perf.jpg
    alt: 'This is a post image'
    
---


I changed my career path four months ago, leaving the role of database engineer to become a Backend Developer. When I reflected on the past, I decided to write about what I had learned from database performance issues. Although I am not a database administrator, I have solved numerous database performance issues.

There are numerous things that can be done to improve the database system; some will have a minor impact, while others will have a significant impact. Some of them must be completed, while others are enjoyable to be around. 

> *Everything must be made as simple as possible, but not one bit simpler.*
> 
>  <cite>[Albert Einstein]</cite>
 

This blog is not about database administration; it is about a startup company that is struggling with database performance. I share my knowledge to help the database system. When considering how to improve your database system, consider the following solutions.

Because there are many items below, I will present them briefly. Then I explain each item in detail in serial sections. Okay, you're ready. Let's get started.

### **Data type selection**

More so than you might realize, data type selection is crucial. Because the database query engine behaves differently when you select a text data type for the column with a limited size, such as an identification number or social number. There are numerous benchmark tests for incorrect data type selection, which reduces database performance. Also, if you consider that the id column should be of the GUID data type and your data grows incrementally, your table execution time will be reduced.

### Index usage

You heard many times if you have struggled with a performance problem in your query you should use indices on columns that are in where clauses. Is it true or is it enough for your query performance? For instance, you add an index to a text column and use like operator two wildcards (such as **…. where column_name like %bla bla%** ). Will the query executor use your index when you run this query?

I can easily respond to this question; No! Because you used both wildcards back and front of your parameter.

This is just one example of many incorrect index usages. I demonstrated the most frequent misusage. I'll go over many of the index usage examples on a deeper level later.

### Where does the data store? How does the data load your dashboard? Memory - Disk usage of the database.

Another critical aspect that we overlook is data storage logic. Yes, you say my data is in the cloud or on my local server, and so on. Unfortunately, this basic understanding is insufficient for data storage logic. In the PostgreSQL DBMS, did you hear a toasted table or an unlogged table? Maybe, but most of the time no. What does your mental picture of the query executor look like when you run the query?

You are aware that memory is the most important component of any DBMS, but we are unsure how to select the appropriate memory for our database. It makes database maintenance simple and improves query execution if you are aware of data storage logic. I'm going through this for the time being because this topic will be covered in a different post.

### Dead tuple handling, index fragmentation

The performance of your database will gradually decline if you have a large number of dead tuples or a fragmented index structure. When you have a table with more update transactions than insert transactions, you should consider dead tuple handling as well as index fragmentation.

In this section, I'll also go over how to set up the table structure in systems where update operations outnumber save operations. But for the time being, you should first be a little more patient.😊

### Database Maintenance

Many of us have heard that you should maintain your database on a regular basis, but if we are a small startup without a database engineer or administrator, we can easily overlook this critical issue. Considering that everything appears to be in order, we believe there to be no issue in my database.

However, that is not how the world really is. Your DBMS might occasionally be shut down if you neglect to maintain the following things:

- Reindex
- Vacuum/Shrink
- Analyze table

In another section, I'll detail the specific benefits of database maintenance. Keep scrolling.

### Configuration

I only have a thorough understanding of PostgreSQL database configuration; other DBMSs may have a different configuration logic. PostgreSQL comes with its default settings when you install it. This configuration item satisfies a specific requirement.

If the database is small, you don't need to worry about configuration. However, as your system grows, you should change your mind and recognize that not every performance issue has been addressed in order to create indices or increase memory capacity.

When you change any configuration to fit your system requirements, you may be able to eliminate the most serious performance issue. Having a lot of order by clauses in your queries, for example, slows down the execution time. If you increase the work mem parameter in your configuration, your database system reflects differently and well.

### Monitoring your database

I've noticed that monitoring the database is a very misunderstood concept. Understanding what is happening in the database, such as I/O reads and hits, transaction count, open sessions, and tuples in and tuples out, is part of database monitoring.

You can identify the database trouble area if you carefully examine the dashboard. For instance, if the I/O read count is higher than hit, you should be aware that queries are loading from disk rather than the cache. Then you devise a strategy for resolving the issue, such as expanding your database's buffer capacity. In the future, this part will be thoroughly described.

### Read query planning

It's possible that we created numerous queries and believed they were very powerful. RDBMS logic may dictate that if you show a report to a customer, this report may include many tables, so we occasionally join many tables together.

How will you handle displaying the data from the various tables? You might believe that all you have to do is join those tables with a key-based structure (primary-foreign key matching) and display the results. However, the data may come from ten or more tables, reducing the query execution time. Why?

The logic behind query execution is sometimes disregarded. Numerous algorithms exist, including choose, join sorting, relation algebra, and others. You may have heard terms like rapid sort, hash join, and merge and sort. Even if you are confident in your understanding of SQL or the speed at which your local system can execute queries, you should still examine your query, figure out what is happening in it, and comprehend query planning.

### Write effective queries

Everybody wants to create efficient or effective queries. In our query, we make extensive use of sort and join logic. However, this is insufficient on its own to create efficient queries. You ought to be aware of when to employ CTE structures, subqueries, and possibly your database-based function, and vice versa.

Incorrect sorting (order by), join logic (left outer join, cross join, vs.), filtering and occasionally incorrect table selection are other adversaries of query performance that come to mind.

In the future, we will take care of this section piece by piece. It is, in my opinion, the part that you notice the most. We first gain a basic understanding of the database performance section's reasoning before delving deeply into it.

### The structure that is normalized or not. What are their advantages and drawbacks?

Denormalization is the process of adding redundant data to your schema, typically to remove joins from queries. You might be required to often display the average merchant rating, for instance, in a model with a merchant and orders where each order gets a rating.

 

The straightforward solution would be to group the Orders by their Merchant and do the average calculation as part of the query, but this would necessitate an expensive join between the two tables. Denormalization would instantly make the estimated average of all posts available on Merchant in a new column, without joining or calculating.

Think about it!

### Scalable database structure: Partitioning

Reliable, scalable, and maintainable data systems, in my opinion, are the system underpinnings of what we're aiming for. And in this section, we will learn about the scalable data system. Scalability becomes more crucial as the application's user base expands. A scalable system is capable of handling a large number of requests.

Consider that you display the data time-based and have a table with one million records. You have a report and use the weekly or monthly data to display it. Even if the time column in the table contains an index, as the number of data increases, your display performance will steadily decrease. The size of the index is increasing together with the data, which will reduce query execution time due to the index's enormous size.

You may have heard the phrase "divide and conquer," and you may use partitioning to divide your table in order to tackle this issue. A monthly division of the date field could speed up query execution and the system's response time.

### Using pgBadger, analyze logs.

The pgBadger is a PostgreSQL performance analyzer that uses your PostgreSQL log files to provide completely detailed reports quickly.

pgBadger is a quick and simple tool for creating HTML5 reports with dynamic graphs and analyzing your SQL traffic. The ideal tool for understanding the operation of your PostgreSQL servers and determining which SQL queries require optimization is pgBadger.

Afterward by integrating a pgBadger into our system, we can subsequently take a closer look at this part and learn how to evaluate these logs and find solutions to the issues they reveal.

### Trigger usage!

Deadlock! The system's and the developer's worst nightmare. Data loss is simply understood when you hear this term. To find out what a deadlock is and how to solve it, just use Google.

One example of a source of deadlock occurring is incorrect trigger usage. In most cases, if you don't have a thorough understanding of usage, you should avoid using this option to activate your system. In a subsequent discussion, we will cover the benefits and drawbacks of database triggers as well as how to use them if necessary.

### JSON data type is advantageous.

NoSQL is the most widely used database in many systems. According to many experts, NoSQL has many benefits when you require key-value data storage and the key-value data storage uses the JSON data type. PostgreSQL and SQL Server both support JSON, but after studying JSON in both of them, I made it clear that PostgreSQL offers more advantages for JSON usage.

The process of serializing or deserializing is simpler because of PostgreSQL's abundance of JSON-type functions, such as the array to JSON function or reverse, the JSON to columns function, etc. You can choose the JSON data format with ease if you want to use some key-value storage that isn't very large.

We've reached the end of our post; I hope you enjoyed it. Following this post, I will go over the topics mentioned above in greater depth. Thank you for your time.