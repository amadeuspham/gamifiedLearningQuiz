# BWA/TIETA12 coursework assignment: Gamified Learning Quiz

Project work of my Basic Web Applications course. A gamified learning platform for students and teachers, which is a fun and convenient solution for online learning.

## Table of contents
- [Repository structure](#repository-structured)
- [Installation](#installation)
- [Game](#game)
- [Management view](#management-view)
- [Tests and documentation](#tests-and-documentation)
- [Security concerns](#security-concerns)
- [Coding conventions](#coding-conventions)
- [Keywords](#keywords)

## Repository structure

```
.
├── app.js                          --> express app
├── index.js                        --> bwa app
├── package.json                    --> app info and dependencies
├── controllers                     --> controllers (handle e.g. routing)
│   ├── db.js                       --> communicate with db to perform CRUD
│   ├── game.js                     --> controls the game functionalities & routing
│   ├── hello.js                    --> the same as "minimal viable grader"
│   ├── managementView.js           --> controls the management view functionalities
│   └── user.js                     --> controls the user management functionalities 
├── models                          --> models that reflect the db schemes
│                                       and take care of storing data
├── public                          --> location for public (static) files
│   ├── img                         --> for images
│   ├── js                          --> for javascript
│   │   ├── management.js           --> dynamically builds the management view UI based on data
│   │   └── mathGenerator.js        --> generate random math questionnaire
│   └── css                         --> for styles
│─ routes                          --> a dir for router modules
│   ├── game.js                     --> handle game routing
│   ├── hello.js                    --> / (root) router
│   ├── managementView.js           --> handle management view routing
│   └── users.js                    --> /users router
│── views                           --> views - visible parts
│   ├── game                        --> game related views
│   │   └── chooseQuestionaire.hbs  --> view to choose questionaire to start the Game
│   │   └── endGame.hbs             --> view when player click grade button
│   │   └── game.hbs                --> the startmenu view of game
│   │   └── gameView.hbs            --> main game view (where users can play)
│   │   └── rules.hbs               --> view showing instruction how to play the game
│   ├── partials                    --> Management partials are on the partials folder descriped below.
│   │   └── editView.hbs            --> The right side of the page is called the editView holds forms etc. 
│   │   └── editPage.hbs            --> Defines the html when we are editing an existing question. 
│   │   └── display_question.hbs    --> defines how individual questions are shown in the editview 
│   │   └── question_listing.hbs    --> Html for aside panel li elements. 
│   │   └── questionaire_listing.hbs--> Defines the html for the aside element questionnaires. 
│   ├── confirmation_message.hbs    --> shows a confirmation messsage before allowing the removal of a questionaire
│   ├── error.hbs                   --> error view
│   ├── hello-graded.hbs            --> show graded response view
│   ├── hello.hbs                   --> main view - "minimal viable grader"
│   ├── layouts                     --> layouts - handlebar concept
│   │   └── default.hbs             --> layout view, "template" to be rendered
│   ├── managementView.hbs          --> management view layout with aside bar and editView or editPage
│   ├── user                        --> views related to users
│   └── game                        --> views related to the game
└── test                            --> tests
│   ├── assignment                  --> unit tests written by the group
│   │   ├── dbFunctionalities.testjs--> test database communications CRUD functions
│   │   ├── game.test.js            --> test Game A+ protocol
│   │   ├── game.reply.test.js      --> test Game grading A+ protocol
│   │   ├── game.reply.test.js      --> test Game functionalities (generate questions, etc.)
│   │   └── management.integration.test.js      --> integration test for CRUD operations in management view
│   ├── integration                 --> integration tests
│   │   ├── hello.reply.test.js     --> test Hello A+ protocol
│   │   ├── hello.test.js           --> test Hello view requirement
│   │   ├── security.test.js        --> test for security issues
│   │   └── users.test.js           --> test user-related functionalities
│   ├── mocha.opts                  --> 
│   ├── models                      --> unit tests for models
│   │   ├── db.test.js              --> test database connection
│   │   ├── game.test.js            --> test game model
│   │   ├── hello.test.js           --> test hello model
│   │   ├── questionnaire.test.js   --> test questionnaire model
│   │   └── user.test.js            --> test user model
│── └── setup.test.js               --> set up all tests
└── screenshots                     --> some screenshots of the app interface

```

## Installation

1. Install `nodejs` and `npm`, if not already installed.

2. Execute in the root, in the same place, where _package.json_-file is):

    ```
    npm install
    ```

3. **Copy** `.env.dist` in the root with the name `.env` (note the dot in the beginning of the file)

    ```
    cp -i .env.dist .env
    ```

    **Obs: If `.env`-file already exists, do not overwrite it!**

    **Note: Do not save `.env` file to the git repository - it contains sensitive data!**

    **Note: Do not modify `.env.dist` file. It is a model to be copied as .env, it neither must not contain any sensitive data!**

    Modify `.env` file and set the needed environment variables.

    In the real production environment, passwords need to be
    long and complex. Actually, after setting them, one does
    not need to memorize them or type them manually.

4. `Vagrantfile` is provided. It defines how the vagrant
   environment is set up, commands to be run:

    `vagrant up` //sets up the environment
    `vagrant ssh` //moves a user inside vagrant

    Inside vagrant, go to the directory `/bwa` and start the app:
    `npm start`

5. As an other alternative, `Dockerfile` is provided as well.
   Then, docker and docker-compile must be installed.
   In the root, give:

    ```
    docker-compose build && docker-compose up
    ```

    or

    ```
    docker-compose up --build
    ```

    The build phase should be needed only once. Later on you should omit the build phase and simply run:

    ```
    docker-compose up
    ```

    The container is started in the terminal and you can read what is written to console.log. The container is stopped with `Ctrl + C`.


    Sometimes, if you need to rebuild the whole docker container from the very beginning,
    it can be done with the following command:

    ```
    docker-compose build --no-cache --force-rm && docker-compose up
    ```

6. Docker container starts _bwa_ server and listens `http://localhost:3000/`

7. Docker container is stopped in the root dir with a command:

    ```
    docker-compose down
    ```


## Game
First, you have to login. Please register an account if you don't have one yet.
 ![Log in please](/screenshots/login.png) 
From the homepage, click on "Who wants to be a millionaire" to access the game.
 ![Logged in as admin](/screenshots/logged_in.png)
You can choose to start the game rightaway, or to read the rules first.
 ![Choose to play or read rules](/screenshots/game.png)
The game is similar to the famous television show "Who wants to be a millionnaire", with a few differences. The student will answer a randomly-generated multiple-choice questions one-by-one, from a questionnaire of his/her choice. Each question will be randomly picked from all questions of the questionnaire, with 4 options to choose from - they are in turn randomly picked from all of the question's options.
 ![Play the game](/screenshots/play.png)
During one gameplay, the student have 2 "helps": 
  **50/50** - remove 50% of the answers, 
  **Skip** - skip the current question
  **Double points** - double the maxpoint for current question
Each of these helps can only be used once in each game.
 ![Question in game](/screenshots/question.png)
The game **will not stop** until the player press "Grade". After pressing the button, the player's answers will be graded, The results will then be displayed. The "maximum points" in this case is the total number of points worth from all the questions generated and displayed.
 ![Graded](/screenshots/grade.png)

## Management view

The management view enables the admin/teacher to perform the 4 CRUD operations: Create, Read, Update and Delete. To access this view, first, you need to login as an admin/teacher.

From the main window of the management view, you can create a new questionnaire, by filling in the required information in the form. The sidebar lists all existing questionnaires. Clicking on a questionnaire's name will show you the questions' titles in that questionnaire. You can update each questionnaire (by clicking on the pencil icon) or delete it (by clicking on the trash icon).
 ![Management main view](/screenshots/mana_main.png)
If you decide to edit a questionnaire, the main window will be updated. The new window will let you add a new question to the questionnaire by filling in a form, as well as the questionnaire title by clicking on the pencil icon next to the current title.
 ![Questionnaire edit](/screenshots/questionnaire_edit.png)
Below that form, you can see a list of all questions in that questionnaire, all options of each question will be listed, as well as the question's maximum points and options' hints. To delete or update a question, click on the trash can icon or the pencil icon, respectively. This is how the editing window for a question looks like:
 ![Question edit](/screenshots/question_edit.png)

## Tests and documentation

Apart from the tests already provided by the course in folders test/integration and test/models, our team has written a few more tests in test/assignment to test game functionalities, management view and database communications. Please refer to the project structure to see descriptions of said tests, which are all in ./test/assignment.

## Security concerns

Below are some common security threats and how this project work is protected against them.

1. Cross-site scripting (XSS): A method used to injecting client-side scripts into web pages. If hackers can run JavaScript code on your webpage, a lot of damage can be done.

To prevent this threat, we use [Helmet](https://www.npmjs.com/package/helmet), which secures the app by setting various HTTP headers. Regarding XSS, Helmet contains an [XSS Filter](https://helmetjs.github.io/docs/xss-filter/) middleware, which adds some small XSS protections. Helmet contains up to 14 middlewares, which [protect against a wide variety of attacks](https://expressjs.com/en/advanced/best-practice-security.html#use-helmet).

2. CSRF (Cross-Site Request Forgery): A hacker can create an AJAX Button on a form that makes a request against the site from an external site. This can result in data theft.

To prevent this threat, we use CSRF Tokens mechanism:
  * First, the server sends a client a token.
  * Client then submit a form with the token. 
  * If the token is invalid, the server rejects the request.

We use [csurf](https://www.npmjs.com/package/csurf), which is a middleware for CSRF token creation and validation. The token is added into a hidden field in each form, this token is then validated against the visitor's session or csrf cookie.

3. NoSQL Injections: A hacker may try to inject code in an input that, in order to change existing data in the databse or to get restricted access. The common solution is to sanitize the input before using them.

However, for this project, because we use Mongoose with mongoDB, sanitizing the input is not required. Since we defined Mongoose Schema for all types of input (User, Questionnaire), the input will be converted to its corresponding type beforehand, and no damage will be done.

4. Regarding passwords: the password should not be stored as plain text since the database can be hacked.

We use [bcrypt](https://www.npmjs.com/package/bcryptjs) to hash password before storing, and to check the password for authentication. Bcrypt is designed to protect against rainbow table attacks (which is a common way to hack hashed passwords), and it can keep up with Moore's law, therefore it remains resistant to brute-force search attacks even with increasing computation power.

## Coding conventions

Project uses _express_ web app framework (https://expressjs.com/).
The application starts from `index.js` that in turn calls other modules.  
The actual _express_ application is created and configured in `app.js` and
routes in `router.js`.

The application complies with the _MVC_ model, where each route has
a corresponding _controller_ in the dir of `controllers`.
Controllers, in turn, use the models for getting and storing data.
The models centralize the operations of e.g. validation, sanitation
and storing of data (i.e., _business logic_) to one location.
Having such a structure also enables more standard testing.

As a _view_ component, the app uses _express-handlebars_;
actual views are put in the dir named `views`. It has two subdirectories:
`layouts` and `partials`.
`layouts` are whole pages, whereas `partials` are reusable smaller
snippets put in the `layouts` or other views. Views, layouts, and partials
use _handlebars_ syntax, and their extension is `.hbs`.
More information about _handlebars_ syntax can be found in: http://handlebarsjs.com/

Files such as images, _CSS_ styles, and clientside JavaScripts are under the `public` directory. When the app is run in a browser, the files are located under the`/`path, at the root of the server, so the views must refer to them using the absolute path. (For example, `<link rel =" stylesheet "href =" / css / style.css ">`) ** Note that `public` is not part of this path. **

The _mocha_ and _chai_ modules are used for testing and the tests can be found under the `test` directory.

## Keywords
web applications, web development, fullstack, node, javascript, bootstrap, mongoose, mocha, chai, MVC, express