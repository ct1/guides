# Realtime django

----------

### Websockets and Channels
1. Headings determine grp

    Django channels suppports websockets. Websockets is a permanent di-directional
    communication
    Django channels has a great tutorial to explain the basics and how to use it in Django
    Generally, when pages are activated they connect themselves to a group
    Messages within the group are broadcasted to every group member

### How it works?

    When a webpage is activated it subscribes as a websocket as pertaining to a certain group
    The server register this new member for the group
    Both server and websockets can initiate sending messsages. No need for replies
    This 'communication channel' is opened until the websocket dismisses or is dismissed
    messages can be to individual websockets (groupof 1?) or to all the group members

    The core manager is a 'customers.py' app thata analyzes and dispatches messages

    Django-channels uses redis (or other) to manage channels. Websockets subscribed in channel receive event directed at his group, or broadcasts messages to its peers.
    Django-signals can also generate messages to channels


### Corporate forms are websockets 
    
    Each form template has a jquery code to subscribe/unsubscribe and manage received/sent messages

### Corporate smartphone app 

    Smartphone app communicates via API like any other client


### Step2 Forms demo process

    Form demo consists in seamless, realtime, paper-free and safe process to pre-fill foms with user data.

    The user has a general purpose app on its smartphone
    The admin clerk has a corporate app on a smartphone and the corporate form processing application enabled by websocket technology

    The admin activates the form page on the corporate system (at this moment it has subscribed a websocket communication for certain type of document).
    The admin then, via smartphone, selects the form data to be requested (QR code).

    The user has a general-purpose app to process forms.vShe/he scans the barcode and fills the info required. If the info was previously used it will be auto-filled.When finished generates a QR code.

    The clerk, via mobile, reads this QR code, verifies the info, and if it's ok sends it to the server (via API).

    The server saves the data, generates a signal to channels (and cleans DB).

    Subscribed apps to that type of document, receive a message with the new data, auto-fill the form and auto-unsubscribe to prevent further incoming messages

