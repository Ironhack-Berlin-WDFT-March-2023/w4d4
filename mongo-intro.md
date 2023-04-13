# MongoDB

The database that we are using is MongoDB. MongoDB is a non-relational database that stores data in collections. It stores data in flexible, JSON-like documents, meaning fields can vary from document to document and over time the data-structure can change.

Check if mongo is installed:
```
$ mongo / mongosh
```

## Terminology:

We are using Compass as our GUI for MongoDB. Connect to your local MongoDB with Hostname "localhost" and Port "27017".

Structure: Database -> Collections -> Documents -> Fields

To add some data, we first create a new Database. Then we can add a document by clicking on "Insert Document".

Documents are created using JSON format (or BSON - for binary json). They are key-value objects, so we need to specify both to create our first one.

### Most common data types in mongoDB:

| Type      | Example                               |
| ---       | ---                                   |
| Double    | 3.141625                              |
| String    | "IronHack Coding Bootcamp"            |
| Date      | "Sun Dec 08 07:15:44 UTC 2013"        |
| Boolean   | true                                  |
| Object    | { foo: "bar" }                        |
| Array     | ["apple", "oranges", "kiwis"]         |
| ObjectID  | ObjectId("52cdef7c4bab8bd675297d8a")  |
| Null      | null                                  |

## Mongo Queries

### Import a JSON file into a collection

Now let's import data into our database! We download a movies database:  
Download the file from here: https://s3-eu-west-1.amazonaws.com/ih-materials/learning-units/movies.json.zip

In downloads directory:  
Unzip movies.json.zip  
Use `mongoimport` to import the data:  
```
mongoimport --db video --collection movies --file movies.json --jsonArray
```  
Command syntax:  
```
mongoimport --db <name of db> --collection <name of collection> --file <path of file>
```  
You can also import the data in Compass.  
Now we should find our database "video" in Compass. You can add, edit and delete fields directly in Compass.   

### Basic queries

We use queries to find a subset of documents from our database.  
Syntax:  
```
{ field: value}
```  
Return all documents in the movies collection with **year** equal to **1994**:  
```
{ year: "1994" }
```  
Return all documents in the movies collection with **director** equal to **Quentin Tarantino**:  
```
{ director: "Quentin Tarantino" }
```  
Return all documents in the movies collection with **director** equal to **Quentin Tarantino** and **year** equal to **1994**:  
```
{ director: "Quentin Tarantino", year: "1994" }
```  

If you want to query for an **ObjectID**, you should specify that because MongoDB stores the ObjectID as type **ObjectId**. Otherwise, Mongo Compass searches for a string:  
```
{ "_id": ObjectId("636a32537c2e07f26d7fa5c1")}
```

### Logical operators

**$and**  
All of the filters passed to $and must match for a document to be returned. 
Return all documents in the movies collection with **director** equal to **Quentin Tarantino** and **year** equal to **2009**:  
```
{ $and: [{ director: "Quentin Tarantino" }, { year: "2009" }] }  
```

**$or**  
At least one of the filters passed to $or must match for a document to be returned.  
Return all documents in the movies collection with either **director** equal to **Quentin Tarantino** or **year** equal to **2009**:  
```
{ $or: [{ director: "Quentin Tarantino" }, { year: "2009" }] }
```

**$nor**  
None of the filters passed to $nor must match for a document to be returned.  
Return all documents in the movies collection with neither **director** equal to **Quentin Tarantino** nor **year** equal to **2009**:  
```
{ $nor: [{ director: "Quentin Tarantino" }, { year: "2009" }] }
```

**Project**  
A projection allows us to specify which fields we want to return in the result.
Return only **year** and **title** fields for the returned documents. By default every other field is set to 0 except for _id which is set to 1.  
```
{ year: 1, title: 1, _id: 0 }
```

**Sort**  
Sort by ascending order of **year**:  
```
{ year: 1 }
``` 

Sort by ascending order of **year** and descending order of **title**:  
```
{ year: 1, title: -1 }
```

**Skip and Limit**  
Skips x documents and limits to y documents.  

### More operators

**$ne**  
Return all documents in the movies collection with **year** NOT EQUAL to **1994**:
```
{ year: { $ne: "1994" } }
```

**$gt $gte**  
Return all documents in the movies collection with **year** GREATER THAN **1995**:  
```
{ year: { $gt: "1995" } }
```

Return all documents in the movies collection with **year** GREATER THAN or EQUAL to **1995**:  
```
{ year: { $gte: "1995" } }
```

**$lt $lte**  
Return all documents in the movies collection with **rate** LESS THAN **8.5**:  
```
{ rate: { $lt: "8.5" } }
```

Return all documents in the movies collection with **year** GREATER THAN or EQUAL to **1990** and LESS THAN **2000**:  
```
{$and: [{ year: { $gte: "1990" } }, { year: { $lt: "2000" } }]}
```

**$in**  
Return all documents in the movies collection where **director** is included in the **$in** array (["Quentin Tarantino", "Martin Scorsese"]):  
```
{director: { $in: ["Quentin Tarantino", "Martin Scorsese"] } }
```

Return all documents in the movies collection where *year* is included in the **$in** array:  
```
{year: { $in: ["1980", "1990", "2000"] } }
```

**$nin**  
Return all documents in the movies collection where **year** is NOT in the **$nin** array:  
```
{year: { $nin: ["1980", "1990", "2000"] } }
```

Return all documents in the movies collection where **genre** array doesn't have ANY of the values in the **$nin** array:  
```
{genre: { $nin: ["Crime", "Drama"] } }
```

**$all**  
Return all documents in the movies collection where **genre** array has ALL of the values in the **$all** array:  
```
{genre: { $all: ["Crime", "Drama"] } }
```

Documentation: https://docs.mongodb.com/manual/reference/operator/query/