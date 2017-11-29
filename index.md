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

Application code is located in the imports/ directory and the client/main.js and server/main.js that imports the appropriate code in the proper order for the client and server are in compliance with the Meteor 1.4 guidelines .

All imports/ subdirectory contains Javscript or HTML files that heach have top-level index.js files, which are responsble for importing the correct files to the current directory it is located in.    

Other directories are imported by the client/main.js and server/main.js. For instance, this is the contents of the client/main.js file of CampusBeats: 

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
import '/imports/ui/pages/beats';
import '/imports/ui/pages/home';
import '/imports/ui/pages/admin';
import '/imports/ui/pages/landing';
import '/imports/ui/pages/user';
import '/imports/ui/stylesheets/style.css';
import '/imports/api/base';
import '/imports/api/profile';
import '/imports/api/interest';
```

All of the directories import their specified index.js file, except for the style.css which is imported directly.

This approach is necessary in order to understand the order and type of code that is loaded in our application. It makes it easier to debug code or tempaltes that are not loaded, meaning they may not appear when the application is built. Ideally, top-level imports are located in two places: the main.js files in client/ and server/. Other index.js files are used to import subdirectories. 

Although the top-level import structure guarantees all of the necessary code and templates are loaded, it does not mean symbols are accessisble within a file. For instance, a symbol would need to be imported into a specific file if it is bounded to a collection. 
 
## Naming conventions

The naming conventions adhered for this application are:

  * Lowercase used for files and directories are named in all lowercase with hyphened words. E.g. accounts-config.js
  * Capitilazation used for"global" Javascript variables (such as collections). E.g. Interests.
  * Camel-case used for all other Javascript variables. E.g. collectionList.
  * Capitalization also used for pages represented by templates with underscores separating multiple words. E.g. Home_Page. Files within templates are lower lower case, with hyphens used to separate words. E.g. directory-page.html, user-home.js.
  * Route names are the same as their corresponding page, captilaization used with underscores separating words. E.g. Directory_Page.


## Data model

The CampusBeats data model is implemented by two Javascript classes: [ProfileCollection](https://github.com/campusbeats/campusbeats/blob/master/app/imports/api/profile/ProfileCollection.js) and [InterestCollection](https://github.com/campusbeats/campusbeats/blob/master/app/imports/api/interest/InterestCollection.js). Both of these classes encapsulate a MongoDB collection with the same name and export a single variable (Profiles and Interests)that provides access to that collection. 

Any part of the system that manipulates the CampusBeats data model imports the Profiles or Interests variable, and invokes methods of that class to get or set data.

Within the application, two collections using MongoDB exist: the ProfileCollection and InterestCollection classes. These are inherited from the [BaseCollection](https://github.com/campusbeats/campusbeats/blob/master/app/imports/api/base/BaseCollection.js) class.

The [BaseUtilities](https://github.com/campusbeats/campusbeats/blob/master/app/imports/api/base/BaseUtilities.js) file contains functions that operate across both classes. 

The ProfileCollection and InterestCollection contain Mocha unit tests in [ProfileCollection.test.js](https://github.com/campusbeats/campusbeats/blob/master/app/imports/api/profile/ProfileCollection.test.js) and [InterestCollection.test.js](https://github.com/campusbeats/campusbeats/blob/master/app/imports/api/interest/InterestCollection.test.js). 

Currently, these collections are a part of the current model, but will be updated as new collections are added.

## CSS

The [Semantic UI](http://semantic-ui.com/) CSS framework is used within this application. To learn more about the Semantic UI theme integration with Meteor, see [Semantic-UI-Meteor](https://github.com/Semantic-Org/Semantic-UI-Meteor).

The [app/client/lib/semantic-ui](https://github.com/ics-software-engineering/meteor-application-template/tree/master/app/client/lib/semantic-ui) directory holds the Semantic UI theme files. To load, these files do need to be imported because they are located in the client/ directory and not the imports/ directory as Meteor automatically loads all files located in the client/ directory into the client. 

A menu is fixed at the top of all user pages, which means body elements will have padding and adjusted margins to make the page look visibly pleasing. The landing page, however, does not Note that the user pages contain a menu fixed to the top of the page, and thus the body element needs to have padding attached to it.  However, the landing page does not have a menu and does not need padding in the body. "Triggers" are needed in the [router](https://github.com/campusbeats/campusbeats/blob/master/app/imports/startup/client/router.js) to add and remove the appropriate classes from the body element upon visiting the page. 

## Routing

The application uses [Flow Router](https://github.com/kadirahq/flow-router) to display and navigate through the four main pages and additional pages. 

Routing used in this application is defined in [imports/startup/client/router.js](https://github.com/ics-software-engineering/meteor-application-template/blob/master/app/imports/startup/client/router.js).

The following routes are defined in CampusBeats:

  * The `/` route goes to the public landing page.
  * The `/directory` route goes to the public directory page.
  * The `/<user>/profile` route goes to the profile page associated with `<user>`, which is the UH account name.
  * The `/<user>/home` route goes to the home page associated with `<user>`, which is the UH account name.
  * The `/<user>/admin` route goes to the admin home page associated with `<user>`, which is the UH account name.
  * The `/<user>/beats` route goes to the beats page associated with `<user>`, which is the UH account name.

## Authentication

In this application, the University of Hawaii CAS test server is used for authenticating users. The derived approach for authentication is shown in [meteor-example-uh-cas](http://ics-software-engineering.github.io/meteor-example-uh-cas/).

In order for the application to run successfully, it needs the CAS configuration information in a configuration file such as  [config/settings.development.json](https://github.com/ics-software-engineering/meteor-application-template/blob/master/config/settings.development.json). 

Those with a valid UH account can login and use CampusBeats to find musically inclined people. If a profile does not exist for the current user, one is created for them upon accessing the application. 

## Authorization

Public pages are the landing and directory pages, thus, these pages can be accessed by anyone.

To use the profile and filter pages, authentication is required. The user must log in (i.e. be authenticated) through the UH test CAS server and the returned authenticated username within CAS must be an exact match to the username specified in the URL. For example, the authenticated user `bob` is the only user who can access his pages such as `http://localhost:3000/bob/profile`.

A template-based authorization following the recommendations in [Implementing Auth Logic and Permissions](https://kadira.io/academy/meteor-routing-guide/content/implementing-auth-logic-and-permissions) is used to restrict the contents of the application to authorized users only.  

An If_Authorized template, defined in [If_Authorized.html](https://github.com/bowfolios/bowfolios/blob/master/app/imports/ui/layouts/user/if-authorized.html) and [If_Authorized.js](https://github.com/bowfolios/bowfolios/blob/master/app/imports/ui/layouts/user/if-authorized.js) is implemented for the template-based authorization.

A seperate authorization for admin permissions will also be implemented at a future date. The admins will be allowed to see and access the special admin home page and controls.

## Configuration

The settings files are held in the [config](https://github.com/bowfolios/bowfolios/tree/master/config) directory.  There is one file in the directory, which is [config/settings.development.json](https://github.com/campusbeats/campusbeats/blob/master/config/settings.development.json).

To prevent a file named settings.production.json and other miscellaneous files from being committed to the repository, the [.gitignore](https://github.com/campusbeats/campusbeats/blob/master/.gitignore) was created. 

Upon startup, CampusBeats checks to see if the database is empty in [initialize-database.js](https://github.com/campusbeats/campusbeats/blob/master/app/imports/startup/server/initialize-database.js). If it is, the specified file from the configuration file, like [settings.development.json](https://github.com/campusbeats/campusbeats/blob/master/config/settings.development.json), is loaded.  A sample initialization for the databse is in [initial-collection-data.json](https://github.com/campusbeats/campusbeats/blob/master/app/private/database/initial-collection-data.json) for development purposes.

## Quality Assurance

### ESLint

A eslintrc file is included in Campusbeats to define the coding style that was followed in the development of this application. ESLint can be invoked from the command line as noted here:

```
meteor npm run lint
```

ESLint should run without generating any errors.  


### Data model unit tests

The script named 'test,' defined in the package.json file, can be invoked to run uni tests on the data model:

```
meteor npm run test
```

The results of the test output to the console. For example, a successful run with timestamps removed should look like:

```
[~/github/campusbeats/app]-> meteor npm run test

> campusbeats@ test /Users/campusbeats_squad/github/campusbeats/app
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

#### Milestone 1 was implemented as CampusBeats Github Milestone M1:
![](images/M1.png)  

#### The Project Page for Milestone 1 of CampusBeats can be found at [Milestone 1 Project Page](https://github.com/campusbeats/campusbeats/projects/1)

This is our timeline of Milestone 1.
![](images/timelineM1.png)

This illustrates the commits and various branches/merges created during the development of Milestone 1.

#### Milestone 1 consists of 9 issues, which are being managed using the CampusBeats GitHub Project M1:

Each issue was assigned to one person so that work can be delegated efficently to each team member. A branch named "issue" with its issue number was created for each issue. When every issue was finished/specific mockup page was completed, the branch was merged into master.
![](images/M1milestone.PNG)

## Milestone 2 Functionality
This milestone started on November 23, 2017 and ended on December 7, 2017. 

The task was to be able to use the CAS authentication and improve multiple areas of our application, including quality, software egineering process, and most importantly functionality. Our goal was essentially to improve our application in any way possible, whether it was the funtionality or adding features to make the app more user friendly.  

Some of the features that we are working on Campus beats are the following:
 - Implement a filtering function, so profiles are sortable to find through filters like "Style", "Experience", "Abilities", "Goals". 
 - Create a favorite function that allows users to follow each other. 
 - The ability for our profile page to collect data from the user and successfully create the profile that can be sorted and filterd.
 - The ability to create admin users who can ban "troublesome users" and create new categories. 
 - Implement a calendar that shows users when events are held and who are participating in them.
 
#### Milestone 2 has been implemented as CampusBeats Github Milestone M2:

![](images/M2.png)

#### The Project Page for Milestone 2 of Campus Beats can be found at [Milestone 2 Project Page](https://github.com/campusbeats/campusbeats/projects/2)

At this time, we currently do not have source code for Milestone 2 since we have yet to work on it.

#### Milestone 2 currently consists of 9 issues, which are being managed using the CampusBeats GitHub Project M2:

More issues will be added as they become apparent during the development process.
![](images/M2milestone.PNG)

# Contact us:
Developers of CampusBeats: Blaine Wataru, Innika Pang, and Lancen Daclison.

Email: watarub@hawaii.edu / innikakp@hawaii.edu / lancenhd@hawaii.edu
