

What is a keytable store?

A key table store is a special kind of keyvalue database, which has a built in
convention for converting table references, which include a table name, a
column name, and a row index, into a specific database key.

Additionally, a keytable database should easily allow export to, but not
necessarily imports from, a CSV format. For all entries not in a table, a file
called "keyvalue.csv" will be generated.


Why use a keytable store over SQL?

SQL stores information in tables, but it is primarily designed for accessing
that information using queries.  Queries are an immature and ineffective way
to process data, and arguably completely outdated.  Using batched processing
to handle data 

But one of the biggest benefits of SQL, is that all your information is
structured into a specific schema.


What about object relational mapping? Why not use it?

Well, you can, but it is better to manually handle conversions between objects
and tables.  Objects are a hierarchical structure for organizing data.
Objects can be nested to any level.  This makes all relationships obvious from
context.  This also makes object data great for serializing and saving, as the
receiver does not need to know any pre-defined or versioned schema to process
the data and interpret it.  The semantics of the data are inherently part of
the datastructure itself, and with proper care, no versioning of the data is
needed.

Commonly, data stored in tables is used "relationally" which can replicate the
same structures as object data, using references from one row data entry to
other data entries.  Because tabular relational data is already factored using
references, it can serialize complex hierarchical structures, without fear of
circular references.  In fact, tables can represent arbitrary graph
structures, while nested objects can only be used for so much.

While tabular relational data can be very powerful, it is often recommendable
to not embed relations or references into your table, and simply use tables as
a way to flatten the hierarchical datastructures of relational data.
Eliminating references means that the data is presented "as is", without
interpretation, constraints, or interdependence between parts of your data.
Any structure or interpretation can then be imposed by the application itself,
or linguistically through the use of non-binding categories. For example, you
could find a users with a specific favorite food, without considering that
some kind of restriction or relation between users.  In other words, just
because 2 users like pizza, does not mean they both belong to a "pizza lover's
society".

The benefits of flattening and unstructuring data, or "agnosticizing" data in this way can be
substantial.  This can make applications much easier to build and maintain,
and can help you avoid the plague of versioned data.

One problem with many relational or even object datasets, is that the data
might be collected under one set of definitions or practices, and then
remapped to another form, and thus the representativeness of the data has been
compromised.  Data exists as representations, and data migrations, merging,
factoring, etc, can often compromise these representations.  Creating
"agnostic" data representations, which don't impose norms or hierachies, means
that the data does not have to be versioned, that the meaning of your data
won't break as your collection practices or application changes, and that the
application, and not the database, has the burden of integrating and
interpretting data.

Much of this is missed with object relational mapping, complex schemas, etc.

Simple tabular data, without references or constraints, strikes a very nice
balance between too rigidly structured or too loosely structured data.

It is also easier to cross reference data based on real world practices of
collating or cross referencing data.  For example, you may have a table for
famous chess players, and you may have another table for famous basketball
players.  In order to interpret someone as the same person across both tables,
you may need sufficient information such as their birthday and birth year, the
city they were born, and/or the names or information on their parents.  In
real life we would not assume that two people are the same, just if they have
the same first and last name.  Similarly, you may want to avoid identifying
two records as belonging to one person, if you lack sufficient cross
referential information.

Finally, tabular data can have a relatively stable stucture, and yet still be
extensible with new columns.  Data can later be filled in, if missing
information can be acquired in a reliable way.  The records for data
acquisition can thus be tracked and confirmed as well, without requiring
versioning tables, entries or schemas.

Softlock: Versioning data for transactional purposes.

There is one case where versioning data can be beneficial, and that is for
executing transactions.  Versioned data allows for high concurrency, because
you can tell if the data used by a transaction, is outdated and can be
rejected, and yet the transactions themselves can be processed completely
concurrently and in parallel, even though committing or rejecting these
transactions is handled separately.

So one way to extend keytables, is through such a soft-lock data versioning
system.  Contrary to other forms of data versioning, which are based on
semantic differences, softlock has stable semantics but evolving state, which
is committed in a transactional way based on dependent version numbers for a
transaction.  If any of the dependent versions have been changed, the
transaction is rejected, and not committed.  Such a system will never
gridlock, unless the transactions themselves are poorly designed and cycle.

Gridlock is avoided by the fact that a transaction is never rejected, unless
another transaction has updated the data in the interim.

It is recommended that this softlock algorithm version data on the level of
row indexes.  Whenever a specific row for a given table is updated, the
version number is updated.  If you try to update a row, and someone else
updates it first, the transaction is rejected, and you are free to retry with
the new row.

It is possible to add versioning with greater or less granularity.  For
example, the entire database might have one version number.  If anyone updates
any part of the database, since you read the data, then your transaction would
be rejected.  Or you could version the data on every single entry, both by row
and column.  Then the transaction would be allowed so long as the dependent
entries were not changed.

Row level versioning provides a good balance or middle ground between being
too granular, and unnecessarily storing too many version numbers, and too
coarse, which would make concurrent updates get rejected much more frequently.

Note that dependent versions need not be updated or changed by a transaction.
A transaction might depend on the permissions or existence of a user, but not
make any modifications to that user, for example.

While this softlock versioning algorithm is a great way to allow for high
concurrency transactional data management, such as for games or physics
simulations, for most applications it is not necessary, as high concurrency
needs are rare.
