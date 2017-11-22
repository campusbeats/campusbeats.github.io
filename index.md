---
title: Campus Beats
---

## Table of Contents
- [About Campus Beats](#about-campus-beats)  
- [Installation](#installation)
- [Application design](#application-design)
  - [Directory structure](#directory-structure)
  - [Import conventions](#import-conventions)
  - [Naming conventions](#naming-conventions)
  - [Data model](#data-model)
  - [CSS](#css)
  - [Routing](#routing)
  - [Authentication](#authentication)
  - [Authorization](#authorization)
  - [Configuration](#configuration)
  - [Quality Assurance](#quality-assurance)
    - [ESLint](#eslint)
    - [Data model unit tests](#data-model-unit-tests)
- [Development history](#development-history)
  - [Initial Mockup Pages](#initial-mockup-pages)
  - [Milestone 1: Mockup development](#milestone-1-mockup-development)
  - [Milestone 2: Functionality](#milestone-2-functionality)
  


# About Campus Beats

Campus Beats can be found at [https://campusbeats.meteorapp.com/](https://campusbeats.meteorapp.com/)

## What will Campus Beats Do?
Campus Beats will provide a hub for UH Manoa musicians to seek each other out so they can collaborate and play with each other. Users will be able to search each other out by various factors such as musical styles (e.g. jazz, rock, punk), abilities (e.g. songwriting, guitar, music production), and experience (e.g. <1 month, <6 months, 1 year), and then they can contact each other through use of the app or external means such as phone/email. 

Users can also choose to create events or projects to recruit for musicians, for example a user may need someone to play at an event like a wedding, or a soundtrack for an animation or game. From there, they can search for musicians to fill that need using the app, and meet up together to collaborate.

## How will Campus Beats Work?
Campus Beats will prompt each user (after logging in with UH ID) to create a profile, where they can list their name, musical styles, abilities, experience, and musical goals, as well as links to Youtube, Soundcloud, Spotify, and Itunes, and any personal information like phone, address, or email. 

From there, users will be brought to a directory page, where they can input their search conditions into the search bars, and run the search, where profile cards will be brought up that match those search conditions. Then, the user can click on cards to bring up full profiles of those users, and see further information about that person, or choose to contact them inside or outside the app.

## User Experience of Campus Beats

Upon entering Campus Beats, you will be met with the landing page:

![](images/mockup-6.png)

An authorized UH login is needed to enter Campus Beats. Click the "Login" button to access the application, however, the proper credentials need to be entered in the CAS authentication screen that appears up if you wish to use the application. 

![](images/CASlogin.PNG)

A new user will be taken to their Edit Profile page, where they can edit and add information to their profile, ranging from basic information like Name, Address, or Phone Number, to Campus Beats information like their musical abilities and goals, or links to their external pages such as YouTube or SoundCloud.

![](images/mockup-1.png)

After the user has entered their information and saved it using the "Update" button, they can click on the "Home" tab in their menubar to go to the user homepage. From the homepage, the user can view their favorited users and any other users who have elected to contact them. 

![](images/mockup-2.png)
![](images/mockup-3.png)

The user can also choose to visit the "Beats" page. Here, they are presented with a filter (left side) and a directory (right side). The filter contains a multitude of search fields such as "Goals" or "Abilities" that will narrow down the directory based on what the user inputs in the filter. Once the directory is filtered, the user can click on the cards that show up to view a more in-depth profile page, favorite that person, which will add them to the user's "Favorites" in their homepage, or contact that person, which will show the user in that person's "Interested In You" section on their own homepage.

![](images/beats_page.png)

# Installation
(1) Install [Meteor](https://www.meteor.com/install).

(2) Download a [zip file of Campus Beats](https://github.com/campusbeats/campusbeats/archive/master.zip) or clone it using git.
  
(3) Change into the app/ directory using the cd command and install Node libraries with:

```
$ meteor npm install
```

(4) Run the application using:

```
$ meteor npm run start
```

The application will appear at [http://localhost:3000](http://localhost:3000) when it is running correctly. You can login to the application if you have an account on the UH test CAS server.  

# Application Design 

## Directory structure

At the top-level directory, the structure of CampusBeats looks similar to:

```
app/        # holds the Meteor application sources
config/     # holds configuration files, such as settings.development.json
.gitignore  # don't commit IntelliJ project files, node_modules, and settings.production.json
```

Within this structure, the configuration files and the Meteor application hosting CampusBeats are separated into two directories: the config/ directory and the app/ directory respectively. 

The top-level structure of the app/ directory looks like this:

```
client/
  lib/           # holds Semantic UI files.
  head.html      # the <head>
  main.js        # import all the client-side html and js files. 

imports/
  api/           # Define collection processing code (client + server side)
    base/
    interest/
    profile/
  startup/       # Define code to run when system starts up (client-only, server-only)
    client/        
    server/        
  ui/
    components/  # templates that appear inside a page template.
    layouts/     # Layouts contain common elements to all pages (i.e. menubar and footer)
    pages/       # Pages are navigated to by FlowRouter routes.
    stylesheets/ # CSS customizations, if any.

node_modules/    # managed by Meteor

private/
  database/      # holds the JSON file used to initialize the database on startup.

public/          
  images/        # holds static images for landing page and predefined sample users.
  
server/
   main.js       # import all the server-side js files.
```

## Import conventions

This system adheres to the Meteor 1.4 guideline of putting all application code in the imports/ directory, and using client/main.js and server/main.js to import the code appropriate for the client and server in an appropriate order.

This system accomplishes client and server-side importing in a different manner than most Meteor sample applications. In this system, every imports/ subdirectory containing any Javascript or HTML files has a top-level index.js file that is responsible for importing all files in its associated directory.   

Then, client/main.js and server/main.js are responsible for importing all the directories containing code they need. For example, here is the contents of client/main.js:

```
import '/imports/startup/client';
import '/imports/ui/components/form-controls';
import '/imports/ui/components/directory';
import '/imports/ui/components/user';
import '/imports/ui/components/landing';
import '/imports/ui/layouts/directory';
import '/imports/ui/layouts/landing';
import '/imports/ui/layouts/shared';
import '/imports/ui/layouts/user';
import '/imports/ui/pages/directory';
import '/imports/ui/pages/filter';
import '/imports/ui/pages/landing';
import '/imports/ui/pages/user';
import '/imports/api/base';
import '/imports/api/profile';
import '/imports/api/interest';
import '/imports/ui/stylesheets/style.css';
```

Apart from the last line that imports style.css directly, the other lines all invoke the index.js file in the specified directory.

We use this approach to make it more simple to understand what code is loaded and in what order, and to simplify debugging when some code or templates do not appear to be loaded.  In our approach, there are only two places to look for top-level imports: the main.js files in client/ and server/, and the index.js files in import subdirectories. 

Note that this two-level import structure ensures that all code and templates are loaded, but does not ensure that the symbols needed in a given file are accessible.  So, for example, a symbol bound to a collection still needs to be imported into any file that references it. 
 
## Naming conventions

This system adopts the following naming conventions:

  * Files and directories are named in all lowercase, with words separated by hyphens. Example: accounts-config.js
  * "Global" Javascript variables (such as collections) are capitalized. Example: Profiles.
  * Other Javascript variables are camel-case. Example: collectionList.
  * Templates representing pages are capitalized, with words separated by underscores. Example: Directory_Page. The files for this template are lower case, with hyphens rather than underscore. Example: directory-page.html, directory-page.js.
  * Routes to pages are named the same as their corresponding page. Example: Directory_Page.


## Data model

The BowFolios data model is implemented by two Javascript classes: [ProfileCollection](https://github.com/bowfolios/bowfolios/blob/master/app/imports/api/profile/ProfileCollection.js) and [InterestCollection](https://github.com/bowfolios/bowfolios/blob/master/app/imports/api/interest/InterestCollection.js). Both of these classes encapsulate a MongoDB collection with the same name and export a single variable (Profiles and Interests)that provides access to that collection. 

Any part of the system that manipulates the BowFolios data model imports the Profiles or Interests variable, and invokes methods of that class to get or set data.

There are many common operations on MongoDB collections. To simplify the implementation, the ProfileCollection and InterestCollection classes inherit from the [BaseCollection](https://github.com/bowfolios/bowfolios/blob/master/app/imports/api/base/BaseCollection.js) class.

The [BaseUtilities](https://github.com/bowfolios/bowfolios/blob/master/app/imports/api/base/BaseUtilities.js) file contains functions that operate across both classes. 

Both ProfileCollection and InterestCollection have Mocha unit tests in [ProfileCollection.test.js](https://github.com/bowfolios/bowfolios/blob/master/app/imports/api/profile/ProfileCollection.test.js) and [InterestCollection.test.js](https://github.com/bowfolios/bowfolios/blob/master/app/imports/api/interest/InterestCollection.test.js). See the section below on testing for more details.

## CSS

The application uses the [Semantic UI](http://semantic-ui.com/) CSS framework. To learn more about the Semantic UI theme integration with Meteor, see [Semantic-UI-Meteor](https://github.com/Semantic-Org/Semantic-UI-Meteor).

The Semantic UI theme files are located in [app/client/lib/semantic-ui](https://github.com/ics-software-engineering/meteor-application-template/tree/master/app/client/lib/semantic-ui) directory. Because they are located in the client/ directory and not the imports/ directory, they do not need to be explicitly imported to be loaded. (Meteor automatically loads all files into the client that are located in the client/ directory). 

Note that the user pages contain a menu fixed to the top of the page, and thus the body element needs to have padding attached to it.  However, the landing page does not have a menu, and thus no padding should be attached to the body element on that page. To accomplish this, the [router](https://github.com/bowfolios/bowfolios/blob/master/app/imports/startup/client/router.js) uses "triggers" to add an remove the appropriate classes from the body element when a page is visited and then left by the user. 

## Routing

For display and navigation among its four pages, the application uses [Flow Router](https://github.com/kadirahq/flow-router).

Routing is defined in [imports/startup/client/router.js](https://github.com/ics-software-engineering/meteor-application-template/blob/master/app/imports/startup/client/router.js).

BowFolios defines the following routes:

  * The `/` route goes to the public landing page.
  * The `/directory` route goes to the public directory page.
  * The `/<user>/profile` route goes to the profile page associated with `<user>`, which is the UH account name.
  * The `/<user>/filter` route goes to the filter page associated with `<user>`, which is the UH account name.


## Authentication

For authentication, the application uses the University of Hawaii CAS test server, and follows the approach shown in [meteor-example-uh-cas](http://ics-software-engineering.github.io/meteor-example-uh-cas/).

When the application is run, the CAS configuration information must be present in a configuration file such as  [config/settings.development.json](https://github.com/ics-software-engineering/meteor-application-template/blob/master/config/settings.development.json). 

Anyone with a UH account can login and use BowFolio to create a portfolio.  A profile document is created for them if none already exists for that username.

## Authorization

The landing and directory pages are public; anyone can access those pages.

The profile and filter pages require authorization: you must be logged in (i.e. authenticated) through the UH test CAS server, and the authenticated username returned by CAS must match the username specified in the URL.  So, for example, only the authenticated user `johnson` can access the pages `http://localhost:3000/johnson/profile` and  `http://localhost:3000/johnson/filter`.

To prevent people from accessing pages they are not authorized to visit, template-based authorization is used following the recommendations in [Implementing Auth Logic and Permissions](https://kadira.io/academy/meteor-routing-guide/content/implementing-auth-logic-and-permissions). 

The application implements template-based authorization using an If_Authorized template, defined in [If_Authorized.html](https://github.com/bowfolios/bowfolios/blob/master/app/imports/ui/layouts/user/if-authorized.html) and [If_Authorized.js](https://github.com/bowfolios/bowfolios/blob/master/app/imports/ui/layouts/user/if-authorized.js).

## Configuration

The [config](https://github.com/bowfolios/bowfolios/tree/master/config) directory is intended to hold settings files.  The repository contains one file: [config/settings.development.json](https://github.com/bowfolios/bowfolios/blob/master/config/settings.development.json).

The [.gitignore](https://github.com/bowfolios/bowfolios/blob/master/.gitignore) file prevents a file named settings.production.json from being committed to the repository. So, if you are deploying the application, you can put settings in a file named settings.production.json and it will not be committed.

BowFolios checks on startup to see if it has an empty database in [initialize-database.js](https://github.com/bowfolios/bowfolios/blob/master/app/imports/startup/server/initialize-database.js), and if so, loads the file specified in the configuration file, such as [settings.development.json](https://github.com/bowfolios/bowfolios/blob/master/config/settings.development.json).  For development purposes, a sample initialization for this database is in [initial-collection-data.json](https://github.com/bowfolios/bowfolios/blob/master/app/private/database/initial-collection-data.json).

## Quality Assurance

### ESLint

BowFolios includes a [.eslintrc](https://github.com/bowfolios/bowfolios/blob/master/app/.eslintrc) file to define the coding style adhered to in this application. You can invoke ESLint from the command line as follows:

```
meteor npm run lint
```

ESLint should run without generating any errors.  

It's significantly easier to do development with ESLint integrated directly into your IDE (such as IntelliJ).

### Data model unit tests

To run the unit tests on the data model, invoke the script named 'test', which is defined in the package.json file:

```
meteor npm run test
```

This outputs the results to the console. Here is an example of a successful run, with timestamps removed:

```
[~/github/bowfolios/bowfolios/app]-> meteor npm run test

> bowfolios@ test /Users/philipjohnson/github/bowfolios/bowfolios/app
> TEST_WATCH=1 meteor test --driver-package meteortesting:mocha

[[[[[ Tests ]]]]]                             

=> Started proxy.                             
=> Started MongoDB.  
                         
(STDERR) Note: you are using a pure-JavaScript implementation of bcrypt.
(STDERR) While this implementation will work correctly, it is known to be
(STDERR) approximately three times slower than the native implementation.
(STDERR) In order to use the native implementation instead, run
(STDERR) 
(STDERR)   meteor npm install --save bcrypt
(STDERR) 
(STDERR) in the root directory of your application.

 --------------------------------
 ----- RUNNING SERVER TESTS -----
 --------------------------------
   
=> Started your app.

=> App running at: http://localhost:3000/
    InterestCollection
    ✓ #define, #isDefined, #removeIt, #dumpOne, #restoreOne (69ms)
    ✓ #findID, #findIDs  
    ProfileCollection
    ✓ #define, #isDefined, #removeIt, #dumpOne, #restoreOne (66ms)
    ✓ #define (illegal interest)
    ✓ #define (duplicate interests)

   5 passing (178ms)

Load the app in a browser to run client tests, or set the TEST_BROWSER_DRIVER environment variable. See https://github.com/DispatchMe/meteor-mocha/blob/master/README.md#run-app-tests
```

# Development History

## Initial Mockup Pages

Landing page mockup page

<img height="300" src="images/CampusBeatsLandingPage.png">

Profile mockup page

<img height="300" src="images/CampusBeatsProfile.png">

Directory mockup page

<img height="300" src="images/CampusBeatsFindUsersPage.png">

## Milestone 1: Mockup Development
This milestone started on November 4, 2017 and ended on November 22, 2017. 

The task was to create four HTML mockup pages of CampusBeats and a complete implementation of the landing page, and to deploy our app to Galaxy. Other than the landing page, the four mockup pages consisted of the User Profile, User Home, Admin Home, and Search/Browse page (otherwise known as the Beats page). These mockup pages were created using the Meteor app using templates and FlowRouter to route specific pages.    

### HTML Mockup for Landing:
<img src="images/mockup-6.png">

### HTML Mockup for User Profile:

<img src="images/mockup-7.png">
<img src="images/mockup-1.png">
<img src="images/mockup-5.png">

### HTML Mockup for User Home:

<img src="images/mockup-7.png">
<img src="images/mockup-2.png">
<img src="images/mockup-3.png">
<img src="images/mockup-5.png">

### HTML Mockup for Admin Home:

<img src="images/mockup-7.png">
<img src="images/mockup-4.png">
<img src="images/mockup-2.png">
<img src="images/mockup-3.png">
<img src="images/mockup-5.png">

### HTML Mockup for Beats:

![](images/beats_page.png)

##### Milestone 1 was implemented as CampusBeats Github Milestone M1:

The Project Page for Milestone 1 of CampusBeats can be found at [Milestone 1 Project Page](https://github.com/campusbeats/campusbeats/projects/1)
![](images/M1milestone.PNG)

##### Milestone 1 consisted of 9 issues, which was managed using the CampusBeats GitHub Project M1:

Each issue was assigned to one person so that work can be delegated efficently to each team member. A branch named "issue" with its issue number was created for each issue. When every issue was finished/specific mockup page was completed, the branch was merged into master.

## Milestone 2 Functionality

##### Milestone 2 has been implemented as CampusBeats Github Milestone M2:

The Project Page for Milestone 2 of Campus Beats can be found at [Milestone 2 Project Page](https://github.com/campusbeats/campusbeats/projects/2)

At this time, we currently do not have source code for Milestone 2 since we have yet to work on it.

##### Milestone 2 currently consists of 9 issues, which are being managed using the CampusBeats GitHub Project M2:

More issues will be added as they become apparent during the development process.
![](images/M2milestone.PNG)

# Contact us:
Developers of CampusBeats: Blaine Wataru, Innika Pang, and Lancen Daclison.

Email: watarub@hawaii.edu / innikakp@hawaii.edu / lancenhd@hawaii.edu
