Doc Name - explanation
(Develop)
*server.js file configures express and passport middleware for authentication, and syncs models with db when app is fired up*
server - require express and express-session npm modules to handle user http requests
server - require passport.js from (config) folder to set up user authentication method
    (config)
    *passport.js file checks user's email and password inputs against the User table in the db for a match and returns an error if either field is null or invalid, if input is correct then app logs user in; file's method's are exported*
    passport - require passport middleware to authenticate requests
    passport - require passport-local middleware for server side authentication so the express app and db work together to keep current authentication status of each user visiting the app
    https://medium.com/@johnnysitu/node-js-user-authentication-with-passport-local-strategy-37605fd99715
    passport - require index and user files from (models) folder
        
        (models)
        *index.js file uses conditional to identify which db config user will use, whether sequelize from config json file, or from .env variables, then identifies the model to be used, and stores it into a 'db' array*
        index - require npm fs, path, and sequelize modules, declare basename to extract a filename, declares an oper sys envirnoment variable to activate application's behavior based on whether the deployment environment should be development, testing, or production, declares variable to conacatenate the directory and the file name, and assign an empty object to db
        index - conditional statement used to configure sequelize
        index - app passes an argument named 'file' into a function through the filter method nested in the readdirsync method from fs module, which checks a directory for .js file types
        index - app passes file argument into a function inside the foreach method, loops through each file and puts the sequelize model into the db array
        index - db array exported for use in other files
        https://stackoverflow.com/questions/55951821/how-config-use-env-variable-object-keysand-readdirsync-are-useful-in-finding-mo

        (models)
        *user.js file defines the column names and data types for the User table, also encrypts passwords before storing it in the table*
        user - require bcrypt to compare passwords and implement password hashing and salting, which encrypts password before saving it in database
        user - configure data types of values to be stored in sequelize table called 'user' with an email and password column
        user - create method to compare check if unhashed password can be compared to hashed password
        user - use addHook method to hash user's password before user is created

    passport - tell passport middleware to use a local strategy where the user inputs an email in an object with a usernamefield 
    passport - then an anonymous function is called that is passed an email, password, and a callback function 'done'
    passport - a sequelize query is run to search the db for a single instance where the user's email matches the email stored in the db
    passport - a promise is returned with a function that gets passed dbUser to check if the User variable is empty and return error message, to check if user provides incorrect email, or to return the 'done' callback if the correct password is provided
    https://www.freecodecamp.org/news/learn-how-to-handle-authentication-with-node-using-passport-js-4a56ed18e81e/
    passport - serializeUser, deserializeUser
    https://stackoverflow.com/questions/27637609/understanding-passport-serialize-deserialize
    passport - exports module as 'password' to grant access to methods from other files
            
             
server - set up listening port and require models used for syncing
server - initialize express app and configure middleware needed for authentication
server - Enable data parsing
server - Calls express.json method as a middleware allowing express to recognize the incoming request object as a json object
server - Calls express.static public to so express serves up the files in the order set in the static directories
server - calls session to track user's login status
https://medium.com/@evangow/server-authentication-basics-express-sessions-passport-and-curl-359b7456003d

server - initializes passport authentication middleware to authenticate requests
https://stackoverflow.com/questions/46644366/what-is-passport-initialize-nodejs-express#:~:text=answer%20was%20accepted%E2%80%A6-,passport.,the%20application's%20request%2Dresponse%20cycle.

server - requires routes from (routes) folder used to handle user requests
        (routes)
        *api-routes.js configures routes for login, signup, and logout pages; grabs user email and password, then stores in table and redirects to the login page if no error occurs; file also logs user out by redirecting to the home page*
        api-routes - require passport file methods and db array from ((models)) folder
        api-routes - exports route functions for use in other files
        api-routes - app uses passport.authenticate to configure the passport npm module 
        api-routes - sets up route for signup page and creates table storing email and password in db, returns promise that redirects user to login page, else returns an error if something fails
        api-routes - uses logout and redirect to terminate the login session, and redirects user to the home page
        api-routes - sets up get route to get user data, sending back an empty json object if user not logged in, or responding with user email and id if user is logged in

        (routes)
        *html-routes.js file sets up routes for home page and login page, checks if user is logged in and already has an account then sends them to the members page; file also calls middleware to route user back to login page if not already logged in*
        html-routes - requires path module to allow app to work with directories and file paths
        html-routes - requires custom 'isAuthenticated' middleware from (middleware) folder to check if user logged in
            (config) => (middleware)
            **isAuthenticated file routes user back to login page if not already logged in**
            isAuthenticated - exports method for use in other files
            isAuthenticated - passes req, res, into a function that checks if user is logged in, then passes control to the next middleware function
            isAuthenticated - returns user back to the login page if not logged in 

        html-routes - exports route functions for use in other files
        html-routes - sets up route for home page; checks if user is already logged in and redirects to the members page, if true, else if user not already logged in, app sends user to signup page
        html-routes - sets up route for login page and redirects user to members page if not already logged in, else user is sent to the login page
        html-routes - sets up route on members page that routes user back to the login page if not already logged in

server - calls sequelize sync to sync the models with the database when app gets fired up, starts server afterward
https://sequelize.readthedocs.io/en/1.7.0/articles/express/

    (Develop) => (public)
    *login.html file contains login input forms, document is linked to login.js*
    login - page contains input forms for email address, password, as well as a 'login' button and a link to sign up for current user that has not logged in

        (Develop) => (public) => (js)
        *login.js file .....*
        login - grabs email and password form field inputs from browser upon page / document load
        login - removes whitespace from email and password when login form submitted, and checks whether an email and password has been entered
        login - if email and password already entered, the loginUser callback is called which takes an email and password arguments, then uses the val method to set the email and password fields to blank
        login - app posts the user entered email and password to the login page, redirects user to the members page replacing the members resource with the user's page

    (Develop) => (public)
    *members.html file displays welcome message to members, document is linked to members.js*
    members - document displays welcome text to member after their login has been validated

        (Develop) => (public) => (js)
        *members.js file grabs user data and updates html with member info*
        members - grabs currently logged in user data, then uses 'text' method to grab user email from the member name field

    (Develop) => (public)
    *signup.html file.... *
    signup - document / page contains input forms for email address, password, as well as a 'login' button and a link to sign up, if not already a member

        (Develop) => (public) => (js)
        *signup.js file..... *
        signup - 
        signup - 
        signup - 
        signup - 

() = discussed below
(()) = discussed above