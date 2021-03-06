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
MATCH (hugo:Person {name:"Hugo Weaving"})-[:ACTED_IN]->(m)<-[:ACTED_IN]-(coActors),
      (coActors)-[:ACTED_IN]->(m2)<-[:ACTED_IN]-(cocoActors)
WHERE NOT (hugo)-[:ACTED_IN]->()<-[:ACTED_IN]-(cocoActors) AND hugo <> cocoActors
SET cocoActors.act= "didnot"
```
## Find actors who can introduce Hugo Weaving to Charlize Theron and set a property `introduction` to value `can` on them
```
MATCH (hugo:Person {name:"Hugo Weaving"})-[:ACTED_IN]->(m)<-[:ACTED_IN]-(coActors),
      (coActors)-[:ACTED_IN]->(m2)<-[:ACTED_IN]-(theron:Person {name:"Charlize Theron"})
SET coActors.introduction = "can"
```

## Load Products from CSV
```
LOAD CSV WITH HEADERS FROM "file:///products.csv" As line
CREATE(:Product {unitPrice: toFloat(line.unitPrice), unitsInStock: toInteger(line.unitsInStock),unitsOnOrder: toInteger(line.unitsOnOrder),reorderLevel: toInteger(line.reorderLevel)}) RETURN COUNT(*);
```
## Load Customers and Orders from URL
```
LOAD CSV WITH HEADERS FROM "http://data.neo4j.com/northwind/customers.csv" as line
CREATE(:Customer {customerID: line.customerID,companyName:line.companyName,contactName:line.contactName,contactTitle: line.contactTitle,address:line.address, city: line.city,region:line.region,postalCode:line.postalCode,country:line.country,phone: line.phone, fax:line.fax }) RETURN COUNT(*);


LOAD CSV WITH HEADERS FROM "http://data.neo4j.com/northwind/orders.csv" as line
CREATE(:Order{orderID:line.orderID,customerID:line.customerID,employeeID:line.employeeID,orderDate:line.orderDate,requiredDate:line.requiredDate,shippedDate:line.shippedDate,shipVia:line.shipVia,freight:line.freight,shipName:line.shipName,shipAddress:line.shipAddress,shipCity:line.shipCity,shipRegion:line.shipRegion,shipPostalCode:line.shipPostalCode,shipCountry:line.shipCountry}) RETURN COUNT(*);
```

## Orders
```
LOAD CSV WITH HEADERS FROM "http://data.neo4j.com/northwind/order-details.csv" As line
CREATE (o)-[:ORDERS {quantity:toInteger(line.quantity)}]->(p)
```

## Find the Customers who have purchased more than 1000 products from all their orders and give them a label as :Top_Customer
```
MATCH (cust:Customer)-[:PURCHASED]->(:Order)-[o:ORDERS]->(p:Product) WITH cust as cust, SUM(o.quantity) AS TotalProductsPurchased
WHERE TotalProductsPurchased > 1000
SET cust:Top_Customer
```
