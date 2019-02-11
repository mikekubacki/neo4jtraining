# Neo4j training day

_Note: This repository has been moved over from https://gitlab.com/mikekubacki/neo4jtraining/._

## Basic concepts

The ID's are not needed, also the relationships depends on the questions asked.

## Naming conventions
Uppercase for relationships - **not required**.

## Good ideas
It is a good idea to traverse through bigger node to a smaller node.

Defined relationships directions are not set in stone, this can be inverted later on in the cypher query.

*Example:*

```
(a:Employee) -[:SOLD]-> (b:Order) -[:PRODUCT]-> (c:Product) -[:PART_OF]-> (d:Category)
```

- Practice Merge, as it seems the most complicated element of cypher.

- Don't go over the top with heap size, as Java is pretty bad in removing it afterwards. Default settings are: `dbms.memory.heap.initial_size=512m` and `dbms.memory.heap.max_size=1G`.
If you comment out the above it will run built in defult. 

### How to load data:

- Run seprately:

```cypher
LOAD CSV WITH HEADERS FROM "file:///customers.csv" AS row
WITH row LIMIT 5
CREATE (c:Customer {customerID:row.customerID})
```
- or just load all with this one:
```cypher
LOAD CSV WITH HEADERS FROM "file:///customers.csv" AS row
LOAD CSV WITH HEADERS FROM "file:///customers.csv" AS row
WITH row LIMIT 5
MERGE (c:Customer {customerID:row.customerID})
SET c+= row
```
### Create relationships:

```cypher
LOAD CSV WITH HEADERS FROM "file:///customers.csv" AS row
WITH row LIMIT 5
MERGE (c:Customer {customerID:row.customerID})
on create set c += row
on match set c += row
```

### Load multiple csv files:

```cypher
load csv with headers from file as erow
with erow limit 5
load csv with headers from file as crow
with erow , crow limit 5
return erow, crow
```

```cypher
with row limit 5 
merge (e:Employee {employeeID:row.employeeID})
```

#### Create index:
`CREATE INDEX ON :Employee(EmployeeID)`

#### To connect data:

`ON MATCH` = update attribute

`ON CREATE` = when you are on the initial creation step

#### Memory:

`USING PERIODIC COMMIT 2000` will commit every 2000 rows, hence keep memory usage low.

#### Comments:

Simple, just use `//`, just like in PHP.

#### How to create a constraints?
`CREATE CONSTRAINT ON (n:Employee) ASSERT n.employeeID IS UNIQUE`

### Super important
- The `<id>` is not static. Never refer to the node by its id.
- Never run the following on a big database: `MATCH (n) RETURN n`. As this will essentially display **absolutely** everything.
- Code to destruct everything: `MATCH (n) DETACH DELETE n`.
- If you can't see relations, it might be that the tick box is off in settings for: `Connect result nodes` it's like KETTLE_HOME missing, easy but difficult to notice when it breaks.

## Clustering
1. Currently only one cluster (core) can get a data, the rest will catch up later.
2. Majority of clusters have to decide on incoming transactions.

### Leader:
Choosing the leader node, if one has more data than the other, it wins, if equal, which one sent request first. Leaders are being choosen even daily. Even a quick delay (couple of ms) this may get another server being elected if this one is busy for that time.

## Refactoring

Use this to start: `:play http://guides.neo4j.com/modeling_sandbox/02_flight.html`

You can use APOC iterate call to redo the relationships.

## Notes

- Minus point to consider - to add own APOC, you need to shut down the system. Every time you need to do that. APOC is like stored procedures. Example apoc: `apoc.periodic.iterate`.

- `call apoc.export.graphml.all(file,config)` - for exporting graphs.

- Dense nodes are the ones that almost every node points to.

## Links

- https://www.kaggle.com/rounakbanik/the-movies-dataset/version/7
- http://sandbox.kettle.com:8080/spoon/spoon
- https://github.com/neo4j-examples/kettle-plugin-examples
- http://guides.neo4j.com/modeling_sandbox/02_flight.html
