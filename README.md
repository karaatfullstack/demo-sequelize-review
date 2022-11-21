# demo-sequelize-review
## Day 10 - Rounding Out and Pre-Pillars review
### What we’re covering

My Rounding Out [Supplimental Slides](https://docs.google.com/presentation/d/1pbAXsmBuILy6pYLvgd4gwDsopSXgIo-OxcBtOUjzkfE/edit#slide=id.p1)

** Express: ** 404 Not Found Page and Custom Error Handling

** Sequelize: ** Eager Loading, Class and Instance Methods, Many-to-Many Relationships

## Express
### NUMBER 1 - Walk through Codebase
NOTE: The example uses Harry Potter, which I loved as a kid even though we now know that the author is a soggy popsickle stick
1. seed.js file
We see we have two tables (Student and House)
  - Students each having a name, pet, and houseID
  - House table has many students in each
  - QUESTION: How do we write that relation in Sequelize?   
    Answer: House.hasMany(Student) and Student.belongsTo(House) <- will have FK
In order for any of this to work, we need to define a connection object, db.
- In `database.js ` - We initialize the DB connection object with the URL for the database to connect to. OF NOTE:
  - It uses the postgres protocol, rather than http
  - The host is localhost. In a production app, the DB will most likely be hosted on a different server
  - We've specified a port of 5432. This is the port that Postgres runs on.
  - The database name is hogwarts
Sequelize will utilize pg in order to interact specifically with a PostgreSQL database.
(HOW DID I MAKE THE DB? In my regular terminal, I ran createdb hogwarts. Then, since I have a seed file with objects to add, I can `run npm run seed` to have sequelize generate the table data and save it to my DB)    
    
2. db > house.js file, student.js file, index.js
Each house contains a name, points, team colors, and ghost
Show students.js file 
 -> We DONT need to add a FK here, the association is in the `db > index.js`
  - Also have access to what? Additional methods come with that as well.
  - We see we can export at the bottom, which gives us access in our Routes

3. routes > houses.js
- houses and students have the same code
- Route to get all, route to get by student's primary key
- Keep in mind, the routes existing doesn't do ANYTHING until we have an express server running!

4. app.js
The setup function establishes the use of Morgan for logging and the Routes
We can think of app.js as a book intro - we bring in all the things we need that aren't specific to our code, like publishing information (logging) and create a table of contents to all the chapters of our code
- The app.use() function is used to mount the specified middleware function(s) at the specified path ( app.use(path, callback) )
- We then sync the DB (before listening to the port)
Now, we can run the command `nodemon` and see the message "Castings spells on port 8080"


### NUMBER 2 - Checking Tables and Adding Custom 404
1. Check tables - first in psql server, SELECT * FROM students;
  - Then, go to browser: http://localhost:8080/students (NOTE: if your json isn't pretty, there are extensions [like JSON Formatter](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa/related?hl=en)
2. Now demonstrate routes: http://localhost:8080/students/1 and http://localhost:8080/houses/4
3. What if I just want to hit port 8080? -> ERROR! That's embarrasing. Cat sits on computer -> /sguidphgpaug -> also error!
  - In app.js, can add a custom URL response - put at bottom of others
 ``` 
app.use((req, res) => {
    res.status(404).send("Uh oh SpaghettiOs")
});
```    
4. That looks great! But what about /houses/dgdug? -> Not great!
  - We are hitting the houses route and don't have an associated house ID, but are STILL sending a 200 status
  - We need to make sure we're sending back a 400 response if we can't find information for that specific ID

### NUMBER 3 - Changing Routes to Add Custom Error Handling
1. Go to ```routes > houses.js``` and show the code for specific id route (get /:id)
2. After const house is defined, add a console.log("my house variable: ", house) -> What's defined?
  - It's 'null' -> QUESTION: how can we put in a check for this?
  - With something like this: ```
 if(house === null) {
        throw new Error("that's not a house!");
    } ```
3. Error is an object built into JS. We could also do it in two lines to get rid of the rest of the message ```
  let err = new Error("that's not a house!");
   throw err.message; ```  // uses the Error prototype and throws only the message portion, no line numbers
  - Then, we'll want to wrap `else{ } ` around the res.send(house);
4. ALSO note that by throwing this error, we hit the CATCH statement at the end catch(e) { (use console.log here) }
  - Inside the catch, the next(e) is accepting this error parameter, so if we did have error handling middleware it'd be passed there (it would go look in our app.js file)
  - next will look for the next route to handle this unless it has a parameter, which it knows need to be redirected to error middleware
  - We don't so Express's default error handler manages this, which is the logging to the screen that we see.

## NUMBER 4 - Creating Error-Handling Middleware
Regular middleware looks like this: (req, res, next) => { }
  - Only thing we need to add to make it error handling is an err parameter: (err, req, res, next) => { }
  - In our ```app.js``` we are going to create an error handling middleware
```
app.use((err, req, res, next) => {
        console.log("I'm the new error middleware!", err);
        res.send("Hello from middleware!");
    })
```
Show that those console, then show how we would actually do it by replacing the second line with this: 
```
// res.send("Hello from middleware!");
   const status = err.status || 500;  // lets us pass in other types of errors from other files
   res.status(status).send(err);
```

## Sequelize
