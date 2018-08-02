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
RETURN r1,r2;
```

## Update Customer Node
```
MATCH (c:Customer:Person{name: "Barry"}) 
SET c.loyalty_points = 100
SET c.phone_number = 00000
RETURN c
```
