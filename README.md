hstore_ops - better operator class for hstore
=============================================

hstore_ops extension provides another implementation of GIN index (opclass)
for hstore. It provides smaller index and faster @> operator queries than
default GIN opclass. However, queries containing ?, ?|, ?& operators could
become a little slower.
    
Idea of this opclass is to use composite GIN key which consists of hashes
of hstore key and value. Thus, search for @> operator is possible as simple
match for each queried key-value pair. Search for @>, ?, ?|, ?& is possible
as partial match for each queried hstore key. Recheck is essential for
every kind of search, because of possible hash collision. Hashing provides
small size of index, mixing key and value into same GIN key provides high
performance for @> search operator.

Authors
-------

 * Alexander Korotkov <aekorotkov@gmail.com>, Intaro Soft Ltd., Moscow, Russia
 * Oleg Bartunov <oleg@sai.msu.su>, Moscow University, Moscow, Russia

License
-------

Development version, available on github, released under the
GNU General Public License, version 2 (June 1991).

Downloads
---------

    Stable version of hstore_ops is available from
    https://github.com/akorotkov/hstore_ops

Installation
------------

hstore_ops is regular PostgreSQL extension depending on hstore contrib.
To build and install it you should ensure in following:
    
 * You have development package of PostgreSQL installed or you built
   PostgreSQL from source.
 * Your PATH variable configured so that pg_config command available.
 * You did "CREATE EXTENSION hstore;" before "CREATE EXTENSION hstore_ops;".
    
Typical installation procedure may look like this:
    
    $ git clone https://github.com/akorotkov/hstore_ops.git
    $ cd hstore_ops
    $ make USE_PGXS=1
    $ sudo make USE_PGXS=1 install
    $ make USE_PGXS=1 installcheck
    $ psql DB -c "CREATE EXTENSION hstore_ops;"

Usage
-----

Just create index on hstore column using following command.
   
    CREATE INDEX index_name ON table_name USING GIN (column_name gin_hstore_hash_ops);
    
Index will be automatically used for search on @>, ?, ?|, ?& operators on this
column.
