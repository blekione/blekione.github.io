---
layout: post
title: How to make Hiberante return Long value from a query.
date:   2019-08-23 17:15
description: Databases don't have Long as a data type. But how to return Long from a query using Hibernate?
comments: false

tags:
- java
- development
---

Databases don't have Long as a data type. Because of it Hibernate have to convert `java.lang.Long` Java type into database type (e.g. `BIGINT`) before persistence. For this reason 
Hibernate cannot recognise Long as a result of a database query which returns a numeric result, such as range of IDs. 

If you expect from the bellow query

>Java
{:.filename}
{% highlight java %}
@NamedNativeQuery(name = InvoiceCreditNoteEntity.NNQ_FIND_INVOICE_CREDIT_NOTES_IDS, resultClass = Long.class,
      query = "SELECT icn.icn_credit_note_id FROM bbv.invoices_credit_notes AS icn WHERE icn.icn_invoice_id = :invoice_id")
{% endhighlight %}

long results then Hibernate will throw an exception

{% highlight bash %}
javax.persistence.PersistenceException: [PersistenceUnit: managerBeo] Unable to build Hibernate SessionFactory
...
Caused by: org.hibernate.HibernateException: Errors in named queries: 
nnq_find_invoice_credit_notes_ids failed because of: org.hibernate.MappingException: Unknown entity: java.lang.Long
	at org.hibernate.internal.SessionFactoryImpl.<init>(SessionFactoryImpl.java:334)
{% endhighlight bash %}

Changing expected result type in `resultClass` to `BigInteger` doesn't help as now Hibernate reports unknown entity as `java.math.BigInteger`.

{% include note.html content="
**Note:** Adding java.lang.Long to persistence.xml doesnt work, I have tried it :).
" type="info" %}

# ResultSetMapping for the rescue

The fix is to create `@SqlResultSetMapping` declaration with the name of the column to be mapped. Next, add `resultSetMapping` parameter to the Named Query declaration. Just make sure its value matches the name parameter in the `@SqlResultSetMapping` annotation.

>Java
{:.filename}
{% highlight java %}
@SqlResultSetMapping(name = "mapping_icn_credit_note_id", columns = { @ColumnResult(name = "icn_credit_note_id") })
@NamedNativeQuery(name = InvoiceCreditNoteEntity.NNQ_FIND_INVOICE_CREDIT_NOTES_IDS, resultClass = BigInteger.class,
      query = "SELECT icn.icn_credit_note_id FROM bbv.invoices_credit_notes AS icn WHERE icn.icn_invoice_id = :invoice_id",
      resultSetMapping = "mapping_icn_credit_note_id")
{% endhighlight %}

Still, the result of the query will be of `BigInteger` type, but this can be easy converted.

>Java
{:.filename}
{% highlight java %}
final List<Long> invoiceCreditNotesIds = em
               .createNamedQuery(InvoiceCreditNoteEntity.NNQ_FIND_INVOICE_CREDIT_NOTES_IDS, BigInteger.class)
               .setParameter("invoice_id", invoiceEntity.getId()).getResultStream().map(BigInteger::longValue)
               .collect(Collectors.toList());
{% endhighlight %}

# Conclusion

Object-relational Mapping (ORM) promises an easier life for software developers. Especially for those who has little experience with databases. But there are many quirks like this which makes me scratching my head with loud "What?! Why?".
