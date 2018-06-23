# Ionic template

Structure

----------

### Create application
1. create app
    ```
    ionic start <mobileblue> blank
    collect all files from template and copy them into <mobileblue> directory
    cd mobileblue
    npn update

    ionic serve
    ``` 
2. create development platform
    ```
    ionic cordova platform add android
    ionic cordova platform add ios
    ``` 
When creating the platforms, the plugins defined in config.xml are installed


### Services controllers

Services are files that control applications. An application can have several pages. Services configures application menu options and their pages parameters
Menu-service is the root menu structure where all applications are referenced

Structure of a service:
1. getId
    ```
    token       service unique id, links father & child. 
                If father in Menu-service, then id = Menu-service.getAllThemes.theme
    ``` 
2. getTitle
    ```
    token       
    ``` 
3. getAllThemes
    ```
    Text        Text display in list menus
    Theme       Child service id (getId from child service). Convention names layoutX
    Icon        Icon to display in list menus
    ListView    If present, indicate this theme is menu
    Component
    ``` 
3. getAllThemes
    ```
    getDataForLayout1   Function to define parameters passed to the requested page
    ``` 

### Main menu - app.component
app.component manages main menu.
On selection, calls ItemsPage with selected page id (page.theme passed as componentName)

### Navigation controller - Items page
Items page controls navigation.
Receives a page id
1. Translates page id in a service class
2. gets pages and title from the service (used by html page to produce a sub-menu)
3. On selection
    ```
    requests itself, if selected option was menu (has key listView on his page)
    requests a page, passing arguments: father service, current page
    ```

### Pages
Page is the master manager of an application. An application can have one or more  components (screen page). Logic code exists at both page and component level

Page components are configured via page.html (tag component id corresponds to component.ts selector)

### New application
1. Create a new application entry in service file Menu-service.ts
2. Create application service file
3. Create application page
    ```
    Clone from another application
    Align all files names
    In file.ts rename class name, and @Component keys
    In file.html rename component keys
    ``` 
4. Create application components
    ```
    Clone from another component
    Align all files names
    In file.scss rename scss group
    In file.ts rename class name, and @Component keys
    ``` 
5. Register page and components in app.module.ts
5. Register service and page in Item page

