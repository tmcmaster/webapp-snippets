# Demo Notes

## Prerequisites

1. Install npm package manager
  * See: https://nodejs.org/en/download/package-manager/
2. Install bower package manager
  * ```npm install -g bower```

## Setup Polymer

1. Install the Polymer command line tools
  * ```npm install -g polymer-cli```

## Setup Firebase

1. Install the Firebase command line tools
  * ```npm install -g firebase-tools```

## Initialise Polymer Project

1. Make a directory for you Project
  * ```mkdir demo1```
  * ```cd demo1```
2. Template out a project structure
  * ```polymer init starter-kit```
3. Test the current state of the Project
  * ```polymer serve```
  * open a browser: http://localhost:8080/

## Initialise Git Repository

1. Initialise the Git reportiory locally
  * ```git init```
  * ```git add .```
  * ```git commit -m 'Initalised project.'```
2. Create a new repository in GitHub
  * Login to your GitHub account
  * Create a new repository at: https://github.com/new
3. Push to GitHub Repository
  * Assuming GitHub repository path: https://github.com/username/demo1
  * ```git remote add origin https://github.com/username/demo1.git```
  * ```git push -u origin master```

## Initialise Firebase Database

1. Create a Firebase Database
  * Login to the Firebase Console: https://console.firebase.google.com/
  * Create a new database.
    * For these notes, the database will be 'Demo'.
2. Get the Firebase Database connection details
  * go to the home screen of the database
  * select 'Add Firebase to your web app'.
  * copy down the connection details.
  * For these notes:
    * apiKey: "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA",
    * authDomain: "incandescent-heat-1234.firebaseapp.com",
    * databaseURL: "https://incandescent-heat-1234.firebaseio.com",
    * storageBucket: "incandescent-heat-1234.appspot.com",
    * messagingSenderId: "500709393366"
3. Login to Firebase with command line tools
  * Login to Firebase Database: ```firebase login```
  * If there are issues with proxies and firewalls:
    + Login to Firebase Database: ```firebase --no-localhost login```
    + The webbrowser will give you a key to give to the commandline tool.
4. Initialise the Firebase command line tools.
  * Initialise: ```firebase init```
  * Select ```Hosting: Configure and deploy Firebase Hosting sites```
  * Select the database you want to connect to.
  * Use defaults for all except ```What do you want to use as your public directory?```
    * Set the public directory to: ```build/unbundled```
5. Commit changes to Git
  * ```git add .```
  * ```git commit -m 'Initialised the Firebase command line tools.'```

## Deploy Polymer project to Firebase Hosting

1. Deploy the Polymer project into Firebase Hosting
  * Package the Polymer project ready for deployment: ```polymer build```
  * Deploy project: ```firebase deploy```
2. Test the deployment
  * In the Firebase Console, select the Hosting section
  * Select the hosting URL

## Setting up some data in the Firebase Database

1. In the Firebase Console, select the Database section
2. Add a top level node called demo1, with value true.
3. Download database data to local JSON file:
  * From the commmand line: ```firebase database:get /demo1 -o database-data.json```
4. Initalise the database with some useful data
  * Edit the database-data.json file and replace contents with:
```json
{
    "details": {
        "title": "Firefly",
        "director": "Joss Whedon",
    },
    "characters": [{
        "name": "Malcolm 'Mal' Raynolds",
        "actor": "Nathan Fillion"
    }, {
        "name": "Zoe Washburne",
        "actor": "Gina Torres"
    }, {
        "name": "Alan Tudyk",
        "actor": "Hoban 'Wash' Washburne"
    }, {
        "name": "Jayne Cobb",
        "actor": "Adam Baldwin"
    }, {
        "name": "Inara Serra",
        "actor": "Morena Baccarin"
    }, {
        "name": "Kaylee Frye",
        "actor": "Jewel Staite"
    }, {
        "name": "River Tam",
        "actor": "Summer Glau"
    }, {
        "name": "Dr. Simon Tam",
        "actor": "Sean Maher"
    }, {
        "name": "Shepherd Book",
        "actor": "Ron Glass"
    }]
}
```
5. Upload local data to the Firebase database
  * From the command line: ```firebase database:set /demo1 database-data.json```

6. Commit changes to Git
    ```shell
    ## from the project base directory
    git add .
    git commit -m 'Added some data to the Firebase database.'
    ```

## Connect Polymer to the Firebase Database

1. Install the Firebase and Polymer web components

  ```shell
  ## from the project base directory
  bower install --save PolymerElements/paper-elements
  bower install --save PolymerElements/iron-elements
  bower install --save firebase/polymerfire
  ```

2. Add a firebase-app and firebase-auth

  * Add imports to the import section
    ```xml
    <link rel="import" href="../bower_components/polymerfire/firebase-app.html">
    ```
    ```xml
    <link rel="import" href="../bower_components/polymerfire/firebase-auth.html">
    ```

  * Add elements to my-app.html template section
    ```xml
    <firebase-app
      name="incandescent-heat-1234"
      app="{{app}}"
      auth-domain="incandescent-heat-1234.firebaseapp.com"
      database-url="https://incandescent-heat-1234.firebaseio.com/"
      api-key="AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA">
    </firebase-app>
    ```

    ```xml
    <firebase-auth
      id="auth"
      app-name="incandescent-heat-1234"
      user="{{user}}"
      status-known="{{statusKnown}}"
      provider="google"
      on-error="_authOnError">
    </firebase-auth>
    ```

3. Add login button to the

  * Import the button in the import section
  ```xml
  <link rel="import" href="../bower_components/paper-button/paper-button.html">
  ```

  * Add the following button to app-toolbar element
    ```xml
    <paper-button raised on-tap="_login">Login</paper-button>
    ```
  * add login function to the Polymer object
    ```javascript
    _login: function() {
      this.$.auth.signInWithPopup();
    },
    ```

4. Add details to first view

  * Add imports to the import section
  ```xml
  <link rel="import" href="../bower_components/polymerfire/firebase-document.html">
  ```

  * Add firebase-document element to the template section of view2
  ```xml
    <firebase-document
      app-name="incandescent-heat-1234"
      path="demo1/details"
      data="{{details}}">
    </firebase-document>
  ```

  * Import the paper-input in the import section
  ```xml
  <link rel="import" href="../bower_components/paper-input/paper-input.html">
  ```

  * Add the following paper-input to to the first view
    ```xml
    <paper-input label="Title" value="{{details.title}}" disabled></paper-input>
    <paper-input label="Director" value="{{details.director}}" disabled></paper-input>
    ```

  * Add property for the details object
    ```javascript
    details: {
      type: Object,
      notify: true
    }
    ```

5. Add list characters to the second view

  * Add imports to the import section
  ```xml
  <link rel="import" href="../bower_components/polymerfire/firebase-query.html">
  ```

  * Add firebase-query element to the template section of view2
  ```xml
  <firebase-query
    app-name="incandescent-heat-1234"
    path="/demo1/characters"
    data="{{characters}}">
  </firebase-query>
  ```

  * Add the following paper-list to to the first view
    ```xml
    <ul>
      <template is="dom-repeat" items="[[characters]]">
        <li>{{item.name}} ({{item.actor}})</li>
      </template>
    </ul>
    ```

  * Add property for the details object
    ```javascript
    characters: {
      type: Array,
      notify: true
    }
    ```

6. Commit Firebase integration to GitHub

  ```shell
  ## from the project base directory
  git add .
  git commit -m 'Integrated Firebase into Polymer application.'
  git push
  ```
