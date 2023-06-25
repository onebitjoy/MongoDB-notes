*TOP LEVEL DECLARATION*

```javascript
const { MongoClient } = require("mongodb"); // get a MDB client
const url = "mongodb://127.0.0.1:27017/" // url upon which mongodb listens to
const client = new MongoClient(url); // creates a new client
const db_name = "kartavyaDB" // given database name
```

FIND ONE(findOne())
-------------------------
We can query for a single document in a collection with the `collection.findOne()` method. The `findOne()` method uses a query document that you provide to match only the subset of the documents in the collection that match the query.
*If you don't provide a query document or if you provide an empty document, MongoDB matches all documents in the collection.*  *The `findOne()` operation only returns the first matched document.

The following snippet finds a single document from the `users` collection. It uses the following parameters:

- A **query document** that configures the query to return only users with the title of exactly the text `'Vinay'`.
    
- A **sort**  that organizes matched documents based on users age, so if the query matches many documents by age, it get sorted by the value(descending or ascending). 
    
- A **projection** that explicitly excludes the `_id` field from returned documents and explicitly includes only the `name` and `age` object (and its embedded fields).

The below code does -
1. An **async** function that finds the documents based on the query.
2. The query uses fields inside of the document and the value to fetch the matching values.
3. The option object is used to fix few things - 
	1. **sort** - defines if the query matches many documents, it will sort them(1 for ascending, -1 for descending).
	2. **projection** - The projection hides unnecessary data to be shown, limits are defined(0 to hide, 1 to show).
4. The options are sent to **findOne()** that takes two `params` - *query* and *options*
5. **await** is used as a placeholder for a promise because an async task for finding the document is taking place.
6. If there is an error `catch{....}` will catch it.
7. The `finally{....}` will be executed always whether the query is successful or not.

```javascript
/* INCLUDE TOP LEVEL DECLARATION */
async function find_one(username) {
    try {
        const database = client.db(db_name) // creates a pipe to db
        const collection_of_users = database.collection('users');
        
        const query = {
            name: username
        }

        const options = {
            sort: { "age": -1 },
            projection: { _id: 0, name: 1, age: 1 }
        }

		const user = await collection_of_users.findOne(query, options)
        console.log(user);
    }
    catch {
        console.log("Can't find the user!")
    }
    finally{
        await client.close()
    }
}

find_one("Manohar") // function is called here

```

**FIND ONE(findOne())**
------------------------

You can query for multiple documents in a collection with `collection.find()`. The `find()` method uses a query document that you provide to match the subset of the documents in the collection that match the query.
*If you don't provide a query document (or if you provide an empty document), MongoDB returns all documents in the collection*.
We can specify **sort** and **projection** options.
**This function returns a CURSOR, instead of a list of documents.** *We can use the <`for await ... of`> syntax to iterate through all the documents or one of the following CURSOR methods - *
1. next()
2. toArray()

**If no documents match the query, `find()` returns an empty cursor.**

```javascript
/* INCLUDE TOP LEVEL DECLARATION */
async function find_all(username) {
    try {
        const database = client.db(db_name) // creates a pipe to db
        const collection_of_users = database.collection('users');
        
        const query = {
            name: username
        }

        const options = {
            sort: { "age": -1 },
            projection: { _id: 0, name: 1, age: 1 }
        }

		/* ASYNC ITERATION*/
		const cursor = collection_of_users.find(query, options)

		
		for await(const doc of cursor) { //async iteration
			console.log(doc);
		}
		/* ================== */
/* DON'T TRY MULTIPLE ASYNC CALLS ON A CURSOR, THEY MODIFY WHEN THEY ARE RUN,
	HENCE WILL MODIFY IT UNPREDICTABLLY */
		/* MANUAL ITERATION */
		const cursor_two = collection_of_users.find({})
		while (await cursor_two.hasNext()) { //manual iteration
	        console.log(await cursor_two.next());
	    }
	    /* ================== */
    }
    catch {
        console.log("Can't find the user!")
    }
    finally{
        await client.close()
    }
}

find_all("Manohar") // function is called here

```

Some other function to use -

1. **toArray( )**: This function converts the cursor into an array.(A good idea would be to include a projection that hides the unique ID).
```javascript
    const cursor_two = collection_of_users.find({}) // give all documents
	const all_documents = await cursor_two.toArray()
```

2. **close( )**: Cursors consume memory and network resources both in the client application and in the connected instance of MongoDB. 

```javascript
	await cursor.close()
```
