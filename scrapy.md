# Scrapy configuration

----------

### Rotating proxies
1. Install scrapy-rotating-proxies module
```
pip install scrapy-rotating-proxies
```

In your scrapy project settings file add configuration
```
ROTATING_PROXY_LIST_PATH = '/Users/ctavares/dev/scrapy/<projpath>/proxies.txt'

...

DOWNLOADER_MIDDLEWARES = {
    'rotating_proxies.middlewares.RotatingProxyMiddleware': 610,
    'rotating_proxies.middlewares.BanDetectionMiddleware': 620,
#    'eci.middlewares.EciDownloaderMiddleware': 543,
}
```

If needed, configure maximun retries per request (default=5)
```
ROTATING_PROXY_PAGE_RETRY_TIMES = 10
```


2. Create proxies list
Use an existing javascript library. Install module globally via npm
    ```
    npm install -g proxy-lists
    ```
Create file 'proxies.txt' in current directory
    ```
    proxy-lists getProxies --sources-white-list="gatherproxy,sockslist"
    ```

### Scrapy with selenium
Scrapy middleware to handle javascript pages using selenium.

1. Installation
    ```
    pip install scrapy-selenium
    ```

2. In your scrapy project settings file add the SeleniumMiddleware to the downloader middlewares:
    ```
    DOWNLOADER_MIDDLEWARES = {
        'scrapy_selenium.SeleniumMiddleware': 800
    }
    ```

3. Usage refer to the scrapy_selenium project [Here](https://github.com/clemfromspace/scrapy-selenium)


### Deploying - Scrapyd server
Scrapyd is a service for running Scrapy spiders on a server.
1. Installation
    ```
    pip install scrapyid
    ```
Must be always running.

### Deploying - Scrapyd-client
Scrapyd-client is a client for scrapyd. Provides the scrapyd-deploy utility to deploy projects to scrapyd server.

1. Scrapyd-client installation
    ```
    pip install scrapyid-client
    ```

### Deploying - setup to deploy

1. Don't need to create scrapyd.cfg. Scrapyd defaults are fine
2. Need to define file output name on each project settings
Output files created on folder data
    ```
    FEED_URI='/Users/ctavares/Dev/scrapy/data/retailer.json'
    ```
3. Spiders make no management to backup previous runs. Files management done on scripts

### Deploying - spider tasks script
Script makes file-backup management on previous runs, cleans unnecessary scrapy files (backup/eggs), and launches spider curl processes.
    ```
    #!/bin/bash

    # script to launch spider for country/project. Requires two arguments
    COUNTRY=$1
    PROJ=$2

    SCRAPY_DIR="/Users/ctavares/Dev/scrapy"
    PROJ_DIR=$SCRAPY_DIR/$COUNTRY/$2
    FILE_BACKUP=$SCRAPY_DIR/data/$2_old.json
    FILE_OUT=$SCRAPY_DIR/data/$2.json
    #LOG=$SCRAPY_DIR/log

    FINISHED="false"

    cd $PROJ_DIR

    # loop until launch or cancel
    while  [  "$FINISHED" != "true" ]; do

        # if scrpyd is running launch curl process
        if [[ $(ps axo pid,command | grep "[s]crapyd") ]]; then

            # if previous backup exists remove it
            if [ -f $FILE_BACKUP ]; then
               rm $FILE_BACKUP
            fi

            # if previous spider result exists rename it
            if [ -f $FILE_OUT ]; then
               mv $FILE_OUT $FILE_BACKUP
            fi

            # remove previous eggs and logs files
            rm $SCRAPY_DIR/eggs/$PROJ/* 
            rm $SCRAPY_DIR/logs/$PROJ/$PROJ/*

            # deploy project
            /Library/Frameworks/Python.framework/Versions/3.5/bin/scrapyd-deploy -p $PROJ
            # launch deamon spider
            curl http://localhost:6800/schedule.json -d project=$PROJ -d spider=$PROJ

            FINISHED="true"
            # notify spider launched
            /usr/local/bin/terminal-notifier -message "Launched spider $1/$2" -title "Spider"

        else
            # popup
            ACTION="$(osascript -e 'display dialog "Missing scrapyd. Run scrapy directly?" buttons {"Repeat","Yes","Later"} default button "Later"')"

            if [ "$ACTION" = "button returned:Later" ]; then
                FINISHED="true"

            else
                if [ "$ACTION" = "button returned:Yes" ]; then

                    # if previous backup exists remove it
                    if [ -f $FILE_BACKUP ]; then
                       rm $FILE_BACKUP
                    fi

                    # if previous spider result exists rename it
                    if [ -f $FILE_OUT ]; then
                       mv $FILE_OUT $FILE_BACKUP
                    fi

                    # remove previous eggs and logs files
                    rm $SCRAPY_DIR/eggs/$PROJ/* 
                    rm $SCRAPY_DIR/logs/$PROJ/$PROJ/*

                    /usr/local/bin/terminal-notifier -message "Launched scrapy for $1/$2" -title "spider"
                    /Library/Frameworks/Python.framework/Versions/3.5/bin/scrapy crawl $PROJ

                    FINISHED="true"

                else
                    # irrelevant if -> only to prevent error messages
                    if [ "$ACTION" = "button returned:Repeat" ]; then
                        FINISHED="false"
                    fi
                fi
            fi
        fi
    done
    ```

Ensure that the script is executable
    ```
    chmod +x ~/Dev/scrapy/launchd_spider.sh
    ```

How it works?
- If scrapyd running a deamon process for the spider project is launched.

- If scrapyd is not running a popup is launched. Possibilities are:

    1) launch scrapyid in a terminal session and reply Repeat. the script validates that now srapyd is running and launch the deamon process for spider.

    2) execute scrapy in background without scrapyid. Confirm porcess is running with:
    ```
    sudo ps -e | grep scrapy
    ```


4. Visit http://localhost:6800 host to monitor job progress on spiders execution. Only works for the scrapyd option


### Deploying - Spider scheduler

Spiders sschedule is managed through launchd. Each spider has a .plist file controlling it's schedule launch.

1. Each spider is launched on a different day-of-the-week/hour

.plist file example
    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
        <dict>

            <key>Label</key>
                <string>com.es.spider6.shoppingadvisor</string>

            <key>ProgramArguments</key>
                <array>
                    <string>/Users/ctavares/Dev/scrapy/launchd_spider.sh</string>
                    <string>es</string>
                    <string>retailer</string>
                </array>

            <key>StartCalendarInterval</key>
                <array>
                    <!--  Weekdays are 1 - 5; Sunday is 0 and saturday is 7   -->
                    <dict>
                        <key>Weekday</key>
                            <integer>7</integer>
                        <key>Hour</key>
                            <integer>9</integer>
                        <key>Minute</key>
                            <integer>0</integer>
                    </dict>
                </array>
        </dict>
    </plist>
    ```

2. plists location
plsts can be loaded at machine boot, any user login or a certain user login.
It is currently configured to be loaded with a certain user login. This means
the plists files are located at
    ```
    ~/Library/LaunchAgents
    ```

3. Manage plists

Load plist

```
    # -w flag permanently adds the plist to the Launch Daemon
    launchctl load -w ~/Library/LaunchAgents/com.es.spider6.shoppingadvisor.plist
```

Unload plist

```
    # -w flag permanently remove the plist to the Launch Daemon
    launchctl unload ~/Library/LaunchAgents/com.es.spider6.shoppingadvisor.plist
```


### Project references

Home project scrapy-rotating-proxies [here](https://github.com/TeamHG-Memex/scrapy-rotating-proxies)

Home project proxy-lists [here](https://github.com/chill117/proxy-lists)

Home project scrapy_selenium project page [Here](https://github.com/clemfromspace/scrapy-selenium)


Home project scrapyd project page [Here](https://github.com/scrapy/scrapyd)

Home project scrapyd-client project page [Here](https://github.com/scrapy/scrapyd-client)
