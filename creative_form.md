# Ionic template

Structure

----------

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
### app.component
app.component manages main menu
On selection, calls ItemsPage with selected page id (page.theme passed as componentName)

### Navigation controller - Items page
Items page controls navigation
Receives a page id
1. Translates page id in a service class
2. gets pages and title from the service (used by html page to produce a sub-menu)
3. On selection
    ```
    requests itself if selected option was menu (has key listView on his page)
    requests a page passing arguments: father service, current page
    ```

### Pages
Page is the master manager of an application. An application can have one or more  components (screen page). Logic code exists at both page and component level

Page components are configured via page.html (tag component id corresponds to component.ts selector)

