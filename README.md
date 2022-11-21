# demo-sequelize-review
## Day 10 - Rounding Out and Pre-Pillars review
### What we’re covering

My Rounding Out [Supplimental Slides](https://docs.google.com/presentation/d/1pbAXsmBuILy6pYLvgd4gwDsopSXgIo-OxcBtOUjzkfE/edit#slide=id.p1)

**Express:** 

404 Not Found Page

Custom Error Handling

**Sequelize:** 

- Eager Loading: Making 1 DB call to rule them all (2 at a time)
- Class and Instance Methods:
- Many-to-Many Relationships: 
- 

## Walk through Codebase
NOTE: The example uses Harry Potter, which I loved as a kid even though we now know that the author is a soggy popsickle stick/ snollygoster
1. seed.js file
We see we have two tables (Student and House)
  - Students each having a name, pet, and houseID
  - House table has many students in each
  - QUESTION: How do we write that relation in Sequelize? 
    Answer: House.hasMany(Student) and Student.belongsTo(House) <- will have FK
    
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
- 
