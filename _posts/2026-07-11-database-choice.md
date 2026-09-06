---
layout: post
author: ted
title: "Database Selection"
summary: Advice from Ethan McCue on the reasons to use Postgres.
tags: [devops]
---

<i>This is a copy of an article by Ethan McCue on the selection of database. I have dropped several database from the comparison and kept the main ones only.</i>
<br>
<br>
<hr>

## Just use Postgres: by Ethan McCue (https://mccue.dev)

This is one part actionable advice, one part question for the audience.

Advice: When you are making a new application that requires persistent storage of data, like is the case for most web applications, your default choice should be Postgres.

#### Why not sqlite?
Sqlite is a pretty good database, but its data is stored in a single file.

This implies that whatever your application is, it is running on one machine and one machine only. Or at least one shared filesystem.

If you are making a desktop or mobile app, that's perfect. If you are making a website it might not be.

There are many success stories of using sqlite for a website, but they mostly involve people who set up their own servers and infrastructure. Platforms as a service-s like Heroku, Railway, Render, etc. generally expect you to use a database accessed over network boundary. It's not wrong to give up some of the benefits of those platforms, but do consider if the benefits of sqlite are worth giving up platform provided automatic database backups and the ability to provision more than one application server.

The official documentation has a good guide with some more specifics.

#### Why not DynamoDB, Cassandra, or MongoDB?
Wherever Rick Houlihan is, I hope he is having a good day.

I watch a lot of conference talks, but his 2018 DynamoDB Deep Dive might be the one I've watched the most. I know very few of you are going to watch an hour-long talk, but you really should. It's a good one.

The thrust of it is that databases that are in the same genre as DynamoDB - which includes Cassandra and MongoDB - are fantastic <b>if</b> - and this is a load bearing if:

* You know exactly what your app needs to do, up-front
* You know exactly what your access patterns will be, up-front
* You have a known need to scale to really large sizes of data
* You are okay giving up some level of consistency

This is because this sort of database is basically a giant distributed hash map. The only operations that work without needing to scan the entire database are lookups by partition key and scans that make use of a sort key.

Whatever queries you need to make, you need to encode that knowledge in one of those indexes before you store it. You want to store users and look them up by either first name or last name? Well you best have a sort key that looks like <FIRST NAME>$<LAST NAME>. Your access patterns should be baked into how you store your data. If your access patterns change significantly, you might need to reprocess all of your data.

It's annoying because, especially with MongoDB, people come into it having been sold on it being a more "flexible" database. Yes, you don't need to give it a schema. Yes, you can just dump untyped JSON into collections. No, this is not a flexible kind of database. It is an efficient one.

With a relational database you can go from getting all the pets of a person to getting all the owners of a pet by slapping an index or two on your tables. With this genre of NoSQL, that can be a tall order.

Its also not amazing if you need to run analytics queries. Arbitrary questions like "How many users signed up in the last month" can be trivially answered by writing a SQL query, perhaps on a read-replica if you are worried about running an expensive query on the same machine that is dealing with customer traffic. It's just outside the scope of this kind of database. You need to be ETL-ing your data out to handle it.

If you see a college student or fresh grad using MongoDB stop them. They need help. They have been led astray.

#### Why not MSSQL or Oracle DB?
Genuine question you should ask yourself: Are these worth the price tag?

I don't just mean the straight-up cost to license, but also the cost of lock-in. Once your data is in Oracle DB you are going to be paying Oracle forever. You are going to have to train your coders on its idiosyncrasies, forever. You are going to have to decide between enterprise features and your wallet, forever.

I know its super unlikely that you will contribute a patch to Postgres, so I won't pretend that there is some magic "power of open source" going on, but I think you should have a very specific need in mind to choose a proprietary DB. If you don't have some killer MSSQL feature that you simply cannot live without, don't use it.

#### Why not MySQL?
This is the one that I need some audience help with.

MySQL is owned by Oracle. There are features locked behind their enterprise editions. To an extent you will have lock-in issues the same as any other DB.

But the free edition MySQL has also been used in an extremely wide range of things. It's been around for a long time. There are people who know how to work with it.

My problem is that I've only spent ~6 months of my professional career working with it. I genuinely don't know enough to compare it intelligently to Postgres.

I'm convinced it isn't secretly so much better that I am doing folks a disservice when telling them to use Postgres, and I do remember reading about how Postgres generally has better support for enforcing invariants in the DB itself, but I wouldn't mind being schooled a bit here.

