---
layout: post
title: The SQL query to find all records from one table with relationship to the collection of records from another table.
date:   2019-08-23 20:15
description: I had struggle with creating a SQL query which at first thought should be very easy, but it was not. The post describes the solution, but I think there might be a more elegant solution to the problem.
comments: false

tags:
- java
- development
---

Yesterday, I was working on SQL query which at first look should be very simple and easy to come with. But I was not able to write the solution withouth the help of my more experienced colleague.

I have 2 entity tables (I omit all fields as they are irrevelant to the problem):
`bills`

| bill_id |
|:--------:|
| 1 |
| 2 |
| 3 |
| 4 |
| 5 |
| 6 |
{:.inner-borders}

`meters`

| meter_id |
|:--------:|
| 1 |
| 2 |
| 3 |
{:.inner-borders}

and a mapping table `bills_meters`

| bill_id | meter_id |
|:-------:|:--------:|
| 1 | 1 |
| 2 | 2 |
| 2 | 3 |
| 3 | 1 |
| 4 | 1 |
| 4 | 2 |
| 4 | 3 |
| 5 | 3 |
| 6 | 1 |
| 6 | 3 |
{:.inner-borders}

As you can see from above a bill can have 1...n meters, and I need to write a query to find all bills which are linked to the given meters, e.g. 

>find all bills for meters with ids 2,3

which should return only bill with id `2` (and not bill `4` as it also linked to meter with id `3` or bill `6` which have meter `3` but not `2`).

My current SQL query is a combination of 2 `SELECT` statement with grouping. It starts with filtering out bills without the required number of meters:

{:.filename}
{% highlight SQL %}
SELECT 
   bm.bill_id 
FROM 
   bills_meters AS bm 
GROUP BY 
   bm.bill_id 
HAVING 
   COUNT (bm.bill_id) = 2;
{% endhighlight %}

then filter result for only bills with required meters
{% highlight SQL %}
SELECT 
   bm1.bill_id 
FROM 
   bills_meters
AS 
   bm1 
WHERE 
   bm1.meter_id IN (2, 3) 
AND bm1.bill_id IN 
   (SELECT 
      bm.bill_id 
   FROM 
      bills_meters AS bm 
   GROUP BY 
      bm.bill_id 
   HAVING 
      COUNT (bm.bill_id) = 2)
GROUP BY bm1.bill_id 
{% endhighlight %}

this will return bills `2` and `6` as both have linked 2 meters and at least one meter from the given collection (`IN (2, 3)`).

And finally, get only bills which have only both meters

{% highlight SQL %}
SELECT 
   bm1.bill_id 
FROM 
   bills_meters
AS 
   bm1 
WHERE 
   bm1.meter_id IN (2, 3) 
AND bm1.bill_id IN 
   (SELECT 
      bm.bill_id 
   FROM 
      bills_meters AS bm 
   GROUP BY 
      bm.bill_id 
   HAVING 
      COUNT (bm.bill_id) = 2)
GROUP BY bm1.bill_id 
HAVING COUNT(bm1.bill_id) = 2;
{% endhighlight %}

The query works, I've tested it against a larger set of entries, but somehow I think that there might be a better solution to this problem.
