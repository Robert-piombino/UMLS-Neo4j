1. Preprocess MRCONSO and MRREL with preprocess.py
2. Copy the output into neo4j_import directory
3. Use neo4j-admin bulk importer but note:
  - Can only import into an offline db
  - Does not create any constraints or index, have to do that afterwards as it's a huge performance increase



`
bin/neo4j-admin import --nodes import/MRCONSO.processed.csv --relationships import/MRREL.processed.csv --database umls.db
`

Output:
`
IMPORT DONE in 6m 1s 43ms.
Imported:
  3590365 nodes
  105332256 relationships
  7180730 properties
Peak memory usage: 1.35 GB
`

Now stop the docker machine and delete or rename the (empty) graph.db that was created. Rename the umls.db that the import created to graph.db and start a new docker container (The alternative is to mount a config file in neo4j to use a different db name. TODO simplify this).

`
docker run -it --publish=7474:7474 --publish=7687:7687 --volume=/mnt/datastore/my_neo4j_data:/data --volume=/mnt/datastore/neo4j_plugins:/var/lib/neo4j/plugins --volume=/mnt/datastore/neo4j_import:/var/lib/neo4j/import -e NEO4J_dbms_security_procedures_unrestricted=apoc.\\\* neo4j:3.4.0
`

Now create the index from web interface
`CREATE INDEX ON :Concept(CUI)`
This index speeds up finding nodes by cui from ~ 1500ms to 30ms
