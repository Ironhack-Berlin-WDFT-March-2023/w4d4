# Data models

Let's talk about different ways to model our data. A database entity is a thing, person, place, unit, object or any item which data should be captured and stored as properties.  
We have two ways of structuring relationships between documents: Embedding documents and referencing documents.

### Embedding documents

User collection:  
```js
{
    _id: ObjectId("593e7a1a4299c306d656200f"),
    name: "Willy",
    lastName: "Doe",
    email: "willydow@gmail.com",
    birthday: 1990-12-14 01:00:00.000,
    phone: "123412399",
    address: {
        street: "123 Fake Street",
        city: "Faketon",
        state: "MA",
        zip: "12345"
    }
}
```

- Useful to store related information in the same collection
- Fewer queries will be needed to retrieve the `contact` and `adress` data
- If we frequently access the contact and adress data, it is useful to embed the adress

### Referencing documents

User collection:  
```js
{
    _id: ObjectId("593e7a1a4299c306d656200f"),
    name: "Willy",
    lastName: "Doe",
    email: "willydow@gmail.com",
    birthday: 1990-12-14 01:00:00.000,
    phone: "123412399"
}
```

Adress collection:  
```js
{
   _id: ObjectId("59f30dd86f0b06a96e31bbb9"),
   user_id: ObjectId("593e7a1a4299c306d656200f"),
   street: "123 Fake Street",
   city: "Faketon",
   state: "MA",
   zip: "12345"
}
```
- Useful when you want to use the data on its own
- To represent more complex relationships

## Relationships

We have two main types of relationships:  
- One-to-one: One person has one id-card  
- One-to-many: One blog-post has multiple comments  

### One-to-one

Embedded document:  
```js
{
    _id: ObjectId("593e7a1a4299c306d656200f"),
    name: "Joe Bookreader",
    address: {
        street: "123 Fake Street",
        city: "Faketon",
        state: "MA",
        zip: "12345"
    }
}
```

Document reference:  
```js
{
    _id: ObjectId("593e7a1a4299c306d656200f"),
    name: "Joe Bookreader"
}

{
    _id: ObjectId("59f30dd86f0b06a96e31bbb9"),
    patron_id: ObjectId("593e7a1a4299c306d656200f"),
    street: "123 Fake Street",
    city: "Faketon",
    state: "MA",
    zip: "12345"
}
```

### One-to-many

Embedded documents:  
```js
{
    _id: ObjectId("593e7a1a4299c306d656200f"),
    name: "Joe Bookreader",
    addresses: [
        {
            street: "123 Fake Street",
            city: "Faketon",
            state: "MA",
            zip: "12345"
        },
        {
            street: "1 Some Other Street",
            city: "Boston",
            state: "MA",
            zip: "12345"
        }
    ]
}
```
- Embedding the adresses makes sense if you often need to retrieve name and adresses together

Document references:  
```js
{
    _id: ObjectId("593e7a1a4299c306d656200f"),
    name: "Joe Bookreader"
}

{
    _id: ObjectId("59f30dd86f0b06a96e31bbb8"),
    patron_id: ObjectId("593e7a1a4299c306d656200f"),
    street: "123 Fake Street",
    city: "Faketon",
    state: "MA",
    zip: "12345"
}

{
    _id: ObjectId("59f30dd86f0b06a96e31bbb9"),
    patron_id: ObjectId("593e7a1a4299c306d656200f"),
    street: "1 Some Other Street",
    city: "Boston",
    state: "MA",
    zip: "12345"
}
```

### When to use document references

Embedded documents:  
```js
// Book 1
{
    _id: ObjectId("1"),
    title: "MongoDB: The Definitive Guide",
    author: [ "Kristina Chodorow", "Mike Dirolf" ],
    pages: 216,
    language: "English",
    publisher: {
        name: "O'Reilly Media",
        founded: 1980,
        location: "CA"
    }
}

// Book 2
{
    _id: ObjectId("2"),
    title: "50 Tips and Tricks for MongoDB Developer",
    author: "Kristina Chodorow",
    pages: 68,
    language: "English",
    publisher: {
        name: "O'Reilly Media",
        founded: 1980,
        location: "CA"
    }
}
```
- Embedding the publisher document inside the book document leads to repetition of the publisher data
- Therefore its better to keep the `publisher` information in a separate collection from the `book` collection

If the number of books per publisher is small with limited growth, we can store the book reference inside the publisher document:
```js
{
    name: "O'Reilly Media",
    founded: 1980,
    location: "CA",
    books: ["593e7a1a4299c306d656200f", "593e7b2b4299c306d656299d", ...]
}
 
{
    _id: ObjectId("593e7a1a4299c306d656200f"),
    title: "MongoDB: The Definitive Guide",
    author: [ "Kristina Chodorow", "Mike Dirolf" ],
    published_date: ISODate("2010-09-24"),
    pages: 216,
    language: "English"
}
 
{
    _id: ObjectId("593e7b2b4299c306d656299d"),
    title: "50 Tips and Tricks for MongoDB Developer",
    author: "Kristina Chodorow",
    published_date: ISODate("2011-05-06"),
    pages: 68,
    language: "English"
}
```

To avoid mutable, growing arrays, we store the publisher reference inside the book document:
```js
{
    _id: ObjectId("593e7a1a2312c306d321323g"),
    name: "O'Reilly Media",
    founded: 1980,
    location: "CA"
}
 
{
    _id: ObjectId("593e7a1a4299c306d656200f"),
    title: "MongoDB: The Definitive Guide",
    author: [ "Kristina Chodorow", "Mike Dirolf" ],
    published_date: ISODate("2010-09-24"),
    pages: 216,
    language: "English",
    publisher_id: ObjectId("593e7a1a2312c306d321323g")
}
 
{
    _id: ObjectId("593e7b2b4299c306d656299d"),
    title: "50 Tips and Tricks for MongoDB Developer",
    author: "Kristina Chodorow",
    published_date: ISODate("2011-05-06"),
    pages: 68,
    language: "English",
    publisher_id: ObjectId("593e7a1a2312c306d321323g")
}
```

### Many-to-many

- If your books can also have multiple publishers, storing the reference in the book document could also lead to growing arrays

In this situation you could use an intermediate collection to store relations between the two collections:  

```js
{
    _id: ObjectId("593e7a1a7312c306d393846p"),
    book_id: ObjectId("593e7a1a4299c306d656200f"),      // MongoDB: The Definitive Guide
    publisher_id: ObjectId("593e7a1a2312c306d321323g")  // O'Reilly Media
}
 
{
    _id: ObjectId("593e7a1a7312c306d393847p"),
    book_id: ObjectId("593e7a1a4299c306d656200f"),      // MongoDB: The Definitive Guide
    publisher_id: ObjectId("593e7a1a2312c306d321324g")  // SitePoint Media
}
 
{
    _id: ObjectId("593e7a1a7312c306d393848p"),
    book_id: ObjectId("593e7b2b4299c306d656299d"),      // 50 Tips and Tricks for MongoDB Developer
    publisher_id: ObjectId("593e7a1a2312c306d321323g")  // O'Reilly Media
}
```

## Some considerations

- Schema design is tricky, because there are no magic rules to just stick to
- Think about your specific usecases: Do you need the documents on their own or do you mainly access them together (for example: a user and his adress)? How is the relationship between the documents?  
- Often its better to embed your documents so you don't have to perform separate queries   
