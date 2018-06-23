# Scrapy configuration

----------

### Rotating proxies
1. Configure rotating-proxies in scrapy
Install scrapy-rotating-proxies module
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
Configure maximun retries per request (default=5) if you want
    ```
    ROTATING_PROXY_PAGE_RETRY_TIMES = 10
    ```

Home project scrapy-rotating-proxies [here](https://github.com/TeamHG-Memex/scrapy-rotating-proxies)


2. Create proxies list
Use an existing javascript library. Install module globally via npm
    ```
    npm install -g proxy-lists
    ```
Create file 'proxies.txt' in current directory
    ```
    proxy-lists getProxies --sources-white-list="gatherproxy,sockslist"
    ```

Home project proxy-lists [here](https://github.com/chill117/proxy-lists)


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

4. Usage refer to the scrapy_selenium project page [Here](https://github.com/clemfromspace/scrapy-selenium) 