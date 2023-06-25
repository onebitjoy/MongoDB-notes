*TOP LEVEL DECLARATION*

```javascript
const { MongoClient } = require("mongodb"); // get a MDB client
const url = "mongodb://127.0.0.1:27017/" // url upon which mongodb listens to
const client = new MongoClient(url); // creates a new client
const db_name = "kartavyaDB" // given database name
```

INSERT ONE( insertOne( { } ) )
--------------------------------

The `insertOne( )` function is used to insert a single document object. We can specify several fields and their values. If the insertion is successful, the insertOne( ) function inserts an `insertedId` and then provides this value in **_id_**  field of that document. 
```javascript
/* INCLUDE TOP LEVEL DECLARATION */
async function insert_one(name, age) {

    try {
        const database = client.db(db_name)
        const user_collection = database.collection("users")
        
        const doc = { name, age }
        
        const result = await user_collection.insertOne(doc)
        console.log("A document has been successfully inserted with id: " + result.insertedId )
        
    } catch {
        console.error("Failure in insertion!");
    } finally {
        await client.close()
    }

}

  
/*USAGE*/
// insert_one("abhishek", 22)
```

INSERT MANY( insertMany ( [ {} , {} ,{} ] )
------------------------------------------------

This function lets us insert many documents at once.
1. It takes an array of objects and inserts them.
2. If the insertion is successful, it returns -
	1. *insertedIds* - IDs of all the documents
	2. *acknowledged* - (Boolean) If the documents are inserted or not
	3. *insertedCount* - Number of docs inserted
3. We can supply an option of <` ordered:true `> to prevent inserting the remaining documents if the insertion failed for a previous document in the array


```javascript
async function insert_many(){
    try {
        const database = client.db(db_name)
        const user_collection = database.collection("users")
        const docs = [
            {name:"jatin", age:21},
            {name:"a", age:23},
            {name:"ad", age:25},
            {name:"asdjbs", age:26},
            {name:"nsjf", age:27},
        ]
        options = {
            ordered: true,
        }

        const result = await user_collection.insertMany(docs, options)

        console.log(`${(await result).insertedCountordered} documents has been inserted!`);

        console.log(`All documents inserted?: ${result.acknowledged}`);

    } catch(error) {
        console.error("Failure in insertion!\n" + error);
    } finally {
        await client.close()      
    }
}

// USAGE
// insert_many()
```

