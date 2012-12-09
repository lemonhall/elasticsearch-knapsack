.. image:: elasticsearch-knapsack/raw/master/Knapsack.png

Elasticsearch Knapsack Plugin
=============================

Knapsack is an index export/import plugin for `Elasticsearch <http://github.com/elasticsearch/elasticsearch>`_.

It uses TAR archives and compression for input/output.

Installation
------------

The current version of the plugin is **1.0.0**

In order to install the plugin, please run

``bin/plugin -install jprante/elasticsearch-knapsack/1.0.0``.

Be aware, in case the version number is omitted, you will have the source code installed for manual compilation.

================ ================
Compound Plugin  ElasticSearch
================ ================
master           0.20.x -> master
1.0.0            0.20.x           
================ ================

Example
=======

Note: you must have the _source field enabled, otherwise the Knapsack export will not work.

Let's assume a simple index.::

   curl -XDELETE localhost:9200/test
   curl -XPUT localhost:9200/test/test/1 -d '{"key":"value 1"}'
   curl -XPUT localhost:9200/test/test/2 -d '{"key":"value 2"}'

Exporting
---------

You can export this Elasticsearch index with::

   curl -XPOST localhost:9200/test/test/_export

The result is a file in the Elasticsearch folder::

   -rw-r--r--   1 joerg  staff    296  9 Dez 14:56 test_test.tar.gz
   
Check with tar utility, the settings and the mapping is also exported::   

   tar ztvf test_test.tar.gz 
   -rw-r--r--  0 0      0         116  9 Dez 14:56 test/_settings
   -rw-r--r--  0 0      0          49  9 Dez 14:56 test/test/_mapping
   -rw-r--r--  0 0      0          17  9 Dez 14:56 test/test/1
   -rw-r--r--  0 0      0          17  9 Dez 14:56 test/test/2

Also, you can export with::

   curl -XPOST localhost:9200/test/_export

with the result file test.tar.gz, or even all data with::

   curl -XPOST localhost:9200/_export

with the result file _all.tar.gz

Importing
---------

You can import the file with::

   curl -XPOST localhost:9200/test/test/_import

Be sure that the index does not exist. You must delete an index by hand. Knapsack does not delete or overwrite data.

You can import the file to a new index with renaming your file to test2_test2.tar.gz and executing the import command::

   mv test_test.tar.gz test2_test2.tar.gz
   curl -XPOST localhost:9200/test2/test2/_import

and check you have copied the data to a new index with::

   curl -XGET localhost:9200/test2/test2/1
   {"_index":"test2","_type":"test2","_id":"1","_version":1,"exists":true, "_source" : {"key":"value 1"}}

Caution
=======

Knapsack is very simple and works without locking or snapshots. So it is up to you to organize the safe export and import. If the index changes while Knapsack is exporting, you may lose data in the export. Do not run Knapsack in parallel on the same export.


License
=======

Elasticsearch Knapsack Plugin

Copyright (C) 2012 Jörg Prante

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.