# Neo4J-Hands-On

## Create Customer Node

``` 
CREATE(p:Customer:Person{name:"Barry",age:29,phone_number:99999})
RETURN p;
```

## Create Product Node-1
``` 
CREATE(p1:Product:Food{name:"Pizza", price:450, discount:10.0, manufacture_date: 20/03/2018})
RETURN p1;
```

## Create Product Node-2
```
CREATE(p2:Product:Food{name:"Coke", price:45, discount:0.0, manufacture_date: 12/02/2018})
RETURN p2;
```

## Create Relationship
``` 
MATCH (c:Customer:Person { name:"Barry" }) 
MATCH (p1:Product:Food { name:"Pizza" }) 
MATCH (p2:Product:Food { name:"Coke" })
CREATE(c)-[r1:Bought{ date: '25/03/2018'}]->(p1)
CREATE(c)-[r2:Bought{ date: '25/03/2018'}]->(p2)
RETURN c,p1,p2,r1,r2;
```

## Update Customer Node
```
MATCH (c:Customer:Person{name: "Barry"}) 
SET c.loyalty_points = 100, c.phone_number = 00000
RETURN c
```

## Delete Coke product and its relationship with Customer

```
MATCH (p:Product:Food { name:"Coke" }) 
DETACH DELETE p

```

# Second

## Load movies DB
```
:play movies
```

## Check movies DB loaded- count movies ordering them by year of release 
```
MATCH (m:Movie) return m.released, COUNT(m) as count ORDER BY count
```

## Find all actors and their movie counts and order them by descending order of Movie Count
```
MATCH (m:Movie) <- [:ACTED_IN] - (p:Person) RETURN p, COUNT(m) as c ORDER BY c DESC
```

## Find the movies in which **Hugo Weaving** acted.

```
MATCH (m: Movie) <- [:ACTED_IN] - (p:Person {name: "Hugo Weaving"}) RETURN m,p
```

## Find all co-actors of **Hugo Weaving**
```
MATCH (coactor: Person) - [:ACTED_IN] -> (m: Movie) <- [:ACTED_IN] - (p:Person {name: "Hugo Weaving"}) RETURN coactor
```

## Find the actors who *DID NOT* act with *Hugo Weaving* but acted with his co-stars and `SET` a property *act* with value *didnot* on those actors. (WIP)
```
MATCH
(co_actors_of_co_actors_of_hugo:Person)-[:ACTED_IN]->(movies_of_co_actors_of_hugo:Movie)<-[:ACTED_IN]-(co_actors_of_hugo: Person)-[:ACTED_IN]->(movies_of_hugo: Movie)<-[:ACTED_IN]-(hugo:Person{name:"Hugo Weaving"})  

WHERE NOT ((co_actors_of_co_actors_of_hugo:Person)-[:ACTED_IN]->(movies_of_hugo:Movie)) 

RETURN DISTINCT co_actors_of_co_actors_of_hugo.name
```

## Load Products from CSV
```
LOAD CSV WITH HEADERS FROM "file:///products.csv" As line
CREATE(:Product {unitPrice: toFloat(line.unitPrice), unitsInStock: toInteger(line.unitsInStock),unitsOnOrder: toInteger(line.unitsOnOrder),reorderLevel: toInteger(line.reorderLevel)}) RETURN COUNT(*);
```
## Load Customers and Orders from URL
```
LOAD CSV WITH HEADERS FROM "http://data.neo4j.com/northwind/customers.csv" as line
CREATE(:Customer) RETURN COUNT(*);


LOAD CSV WITH HEADERS FROM "http://data.neo4j.com/northwind/orders.csv" as line
CREATE(:Order) RETURN COUNT(*);
```
