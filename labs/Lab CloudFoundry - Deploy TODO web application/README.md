![](./images/cloudfoundry.png)

# Introduction

In this lab, you’ll gain a high level understanding of the architecture, features, and development concepts related to the Cloud Foundry runtimes and Bluemix service. Throughout the lab, you’ll create a sample application built with a CLEAN stack (CLoudant NoSQL database, Express, Angular and Node.js).

![Todo](./images/screenshot.png)


# Objective

In the following lab, you will learn:

+ How to deploy a new Cloud Foundry app based on Node.js runtime
+ How to create a new service Cloudant DB to store NoSQL data
+ How to set up a source code repository to collaborate
+ How to manage Continuous Integration and Deployment
+ How to use the Cloud Foundry Command Line


# Pre-Requisites

+ Get a [Bluemix IBM id](https://bluemix.net), or use an existing account.
+ Install the [Bluemix CLI](http://clis.ng.bluemix.net)
+ Install a [Git client](https://git-scm.com/downloads)
+ Install [Node.js](https://nodejs.org)


# Step 1 - Checkout the code locally

1. Open a terminal or a command prompt to clone the repository of the to-do app that you forked. This is the repo that is stored under your username.

For example : [this archive](./solution/node-todo-master.zip)

    ```
    git clone <URL-OF-YOUR-GIT-REPO>
    ```

1. This command creates a directory of your project locally on your disk.


# Step 2 - Change to the to-do app directory

1. Change to the directory of the checkout. Make sure you know what the directory was called when you did a git clone.

    ```
    cd todo-apps/node
    ```

# Step 3 - Push your app to Bluemix

Cloud Foundry relies on the *manifest.yml* file to know what to do when you push the app on Bluemix.
A default manifest.yml file was generated for our app. It looks like:

  ```yml
  applications:
  - path: .
    memory: 256M
    instances: 1
    domain: mybluemix.net
    name: todo-[your-initials]
    host: todo-[your-initials]
    disk_quota: 1024M
  ```

It basically defines one application taking its content from the current directory,
being deployed with **256MB**, with **one** instance, under the **mybluemix.net** domain.
The app is named **todo-[your-initials]** and it is using **todo-[your-initials]** as host name.
It has **1024MB** of disk space available.

1. Specifying the buildpack to be used when pushing a Cloud Foundry app is always faster than relying on buildpack detection. Modify the generated Manifest to specify the **buildpack** by adding one line as follows:

    ```yml
    applications:
    - path: .
      memory: 256M
      instances: 1
      domain: mybluemix.net
      buildpack: sdk-for-nodejs
      name: todo-[your-initials]
      host: todo-[your-initials]
      disk_quota: 1024M
    ```

1. Connect to Bluemix by passing the Bluemix endpoint of the URL region where you created your app.

    ```
    bx api https://api.ng.bluemix.net
    ```

1. Login to Bluemix

    ```
    bx login
    ```

1. Push the app to Bluemix

    ```
    bx cf push
    ```

1. When the command completes, access the application running in the cloud to confirm your change was deployed

    ```
    requested state: started
    instances: 1/1
    usage: 256M x 1 instances
    urls: todo-[your-initials].mybluemix.net
    last uploaded: Mon Jan 16 21:20:54 UTC 2017
    stack: cflinuxfs2
    buildpack: SDK for Node.js(TM) (ibm-node.js-4.6.2, buildpack-v3.9-20161128-1327)

         state     since                    cpu    memory          disk          details
    #0   running   2017-01-16 10:22:17 PM   4.6%   22.1M of 256M   65.8M of 1G    
    ```

Changing files locally and pushing them worked but we can do better.
In a previous step we set up a Git repository and a build pipeline was automatically configured.

# Step 4 - Create and bind a Cloudant service

In order to store the todo, we will need a persistent storage. To do so, we will use a Cloudant NoSQL database, a JSON document oriented store, compatible with CouchDB.

1. Back to the Bluemix console, go to your application **Overview**.

1. Click **Connect New** to add a service to your application

1. Search for **Cloudant** in the catalog

1. Select the free **Lite** plan

1. Give the service a name such as **todo-cloudant-[your-initials]**

1. Click **Create**. Bluemix will provision a Cloudant service and connect it to your application.

1. Select **Restage** when prompted to do so.

    Your application will restart and the service connection information will be made available to your application.

    Note: All the steps above could have been scripted using the three commands below:
    ```
    cf create-service cloudantNoSQLDB Lite todo-cloudant-[your-initials]
    cf bind-service todo-[your-initials] todo-cloudant-[your-initials]
    cf restage todo-[your-initials]
    ```


Congratulations! You completed this lab. You can get familiar with the application code content.

## Source code

### Back-end

| File | Description |
| ---- | ----------- |
|**package.json**|Lists the node.js dependencies|
|**.cfignore**|List of files and directories ignored when calling **cf push**. Typically we ignore everything that can be retrieved with bower or npm. This speeds up the push process.|
|**manifest.yml**|Used by Cloud Foundry when pushing the application to define the application environment, connected services, number of instances, etc.|
|**app.js**|Web app backend entry point. It initializes the environment and imports the Todo API endpoints|
|**todos.js**|Todo API implementation. It declares endpoints for PUT/GET/DELETE (create/retrieve/delete) and handles the *in-memory* storage.

### Front-end

| File | Description |
| ---- | ----------- |
|**.bowerrc**|Configuration file for the [bower](http://bower.io/) web package manager to put our web dependencies under public/vendor|
|**bower.json**|Web dependencies (bootstrap, angular)|
|**index.html**|Web front-end implementation. It displays the todo list and has a form to submit new todos.|
|**todo.js**|Declares the Angular app|
|**todo.service.js**|Implements the connection between the front-end and the back-end. It has methods to create/retrieve/delete Todos|
|**todo.controller.js**|Controls the main view, loading the current todos and adding/removing todos by delegating to the Todo service|


# Resources

For additional resources pay close attention to the following:

- [GitHub Guides](https://guides.github.com/)
- [Get started guides for your favorite runtimes](https://www.ibm.com/blogs/bluemix/2017/03/runtimes-get-started-guides/?social_post=829410659&fst=Learn&linkId=35308736)
