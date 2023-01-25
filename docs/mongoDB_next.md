# MongoDB with NextJS
![MongoDB](./svgs/mongo.svg "Mongo") ![NextJS](./svgs/next_blue.svg "NextJS")
*Sometimes you need a little soy*

## My folder structure
```
src/
  MongoDB/
    model/
      prices.ts         -- holds the model data
    conn.ts             -- connects to the DB
    controller.ts       -- all of the function for API routing
  pages/
    api/
      prices/
        [priceId].ts    -- handles requests for priceId slugs
        index.ts        -- handles generic http requests for prices
```

Next pages API routes requests through an /api/\* endpoint that is treated as an endpoint instead of a page. 

For example http://localhost:3000/api/prices will route a http request to the index.ts file and if there is a 
handler function present it will accept a request and emit a response.
```
export default function handler(req,res){

  }
```
Using mongoose you can connect to the database, mongoose is useful as a Object Data Modeling library. 
You can use MongoDB's api but mongoose gives a convienient way to model objects and type check them before commiting
them to your mongoDB instance. 
```
import mongoose from "mongoose";

export const connectMongoDB = async () => {
  try {
    const {connection} = await mongoose.connect(process.env.MONGODB_URI as string)
    if(connection.readyState == 1) {
      console.log("Database Connected!")
    }
  } catch(err) {
    return Promise.reject(err)
  }
}
```
You template your data using schemas, Shemas relate to the type of data stored in MongoDB documents. 
```
import { Schema, models, model } from 'mongoose'

const pricesSchema = new Schema({
  haircut: String,
  description: String,
  price: String,
})

export const Prices = models.Prices || model('prices', pricesSchema)
```
You get access to your MongoDB collection by making a new collection with model function, passing in the 
collection name and the schema you're using: 
`model('prices', pricesSchema)`

Access your collection using:
`models.Prices`

The export object: 
`export const Prices = models.Prices || model('prices', pricesSchema)`

First checks if the model exists, and if it doesn't it creates it using priceesSchema.
