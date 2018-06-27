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
2. In your scrapy project settings file add your browser config
    ```
    from shutil import which

    SELENIUM_DRIVER_NAME='chrome'
    SELENIUM_DRIVER_EXECUTABLE_PATH=which('geckodriver')
    SELENIUM_DRIVER_ARGUMENTS=['--headless']  # '-headless' if using firefox instead of chrome
    ```
3. Add the SeleniumMiddleware to the downloader middlewares:
    ```
    DOWNLOADER_MIDDLEWARES = {
        'scrapy_selenium.SeleniumMiddleware': 800
    }
    ```

4. Usage refer to the scrapy_selenium project [Here](https://github.com/clemfromspace/scrapy-selenium)


### Deploying - Scrapyd server
Scrapyd is a service for running Scrapy spiders on a server
1. Installation
    ```
    pip install scrapyid
    ```

### Deploying - Scrapyd-client
Scrapyd-client is a client for scrapyd. It provides the scrapyd-deploy utility to deploy your project to a scrapyd server.

1. Scrapyd-client installation
    ```
    pip install scrapyid-client
    ```

2. Deploy the spider to the scrapyd server
First cd into your project's root, then deploy your project
    ```
    Edit scrapy.cfg and uncomment url in the deploy section
    ```
Create a new deploy group for remote execution (if needed)
    ```
    [deploy]
    url = http://46.101.85.44
    username = scrapy
    password = secret
    project = yourproject
    ```
Finally deploy your project
    ```
    scrapyd-deploy -p <project> 
    ```

Repeat this process for all your projects. This completes project uploading to the server

3. Submit job in the development machine
    ```
    cd ~/Dev/scrapy/es

    curl http://localhost:6800/schedule.json -d project=aldi -d spider=aldi -o aldi.json
    ```
The output file is created in the current directory


4. Visit http://localhost:6800 host to monitor job progress



### Project references

Home project scrapy-rotating-proxies [here](https://github.com/TeamHG-Memex/scrapy-rotating-proxies)

Home project proxy-lists [here](https://github.com/chill117/proxy-lists)

Home project scrapy_selenium project page [Here](https://github.com/clemfromspace/scrapy-selenium)


Home project scrapyd project page [Here](https://github.com/scrapy/scrapyd)

Home project scrapyd-client project page [Here](https://github.com/scrapy/scrapyd-client)
