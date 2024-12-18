
![Tilefy](assets/tilefy-banner.jpg?raw=true "Tilefy Banner")  

<h1 align="center">Create Beautiful Tiles for Your Project</h1>

<div align="center">
<a href="https://www.tilefy.me" target="_blank"><img src="https://tiles.tilefy.me/t/tilefy-github-stars.png" alt="tilefy-github-stars" title="Tilefy GitHub Stars" height="50" width="200"/></a>
<a href="https://www.tilefy.me" target="_blank"><img src="https://tiles.tilefy.me/t/tilefy-docker.png" alt="tilefy-docker" title="Tilefy Docker Pulls" height="50" width="200"/></a>
</div>

## Table of Contents
- [Core Functionality](#core-functionality)
- [Screenshots](#screenshots)
- [Installation](#installation)
- [Configuration](#configuration)
- [API Requests](#api-requests)
- [Plugins](#plugins)
- [Donate](#donate)

## Core Functionality
- Dynamically create and recreate PNG tiles
- Showcase any project stats accessible over a public API
- Customize to your liking with your branding and color scheme
- Embed your tiles anywhere you can embed an image
- Self-hosted with Docker

## Screenshots
![Home Screenshot](assets/screenshot.png?raw=true "Tilefy Home Page")  

## Installation
Take a look at the example `docker-compose.yml` file provided. Tilefy depends on two containers:

### Tilefy
Main Python application to create and serve your tiles, built with Flask.
- Serves the interface on port `8000`
- Needs a volume at **/data** to store your tiles, custom fonts and logos, and your **tiles.yml** config file
- Set your Redis connection with the environment variables `REDIS_HOST` and `REDIS_PORT`
- Set the environment variable `TILEFY_HOST` to your full URL where you are hosting this application. Needed to build links and templates to embed your tiles. Do not add a trailing `/`
- Set your timezone with the `TZ` environment variable to configure the scheduler; defaults to *UTC*

### Redis JSON
Functions as a cache and holds your configurations.
- Needs a volume at **/data** to store your configurations permanently

## Configuration
Create a YAML config file at `/data/tiles.yml`. Take a look at the provided `tiles.example.yml` for the basic syntax. *tiles* is the top-level key; list your tiles below. The main key of the tile is your slug and will become your URL, so use no spaces or special characters. 

### Tile Name
Give your tile a unique, human-readable name.

### Background Color, Font Color
Hex color code for background and font. Make sure to add quotation marks to escape the *#* symbol. 

### Width, Height
Size of the tile in pixels.

### Logos
List of logos. Get a list of pre-installed logos:
```bash
docker exec -it tilefy ls logos
```
Add pre-installed logos by providing the file name. Contribute by adding additional commonly used logos. 

Provide your custom logos by adding them to `/data/logos/` in PNG file format with a transparent background. Add your custom logos by providing a relative path like `logos/file-name.png`.

### Font (Optional)
Font defaults to *Vera.ttf*. Use a pre-installed font from the *liberation* or *ttf-bitstream-vera* packages by providing the relative path from the truetype folder.
List available pre-installed fonts:
```bash
docker exec -it tilefy ls /usr/share/fonts/truetype/liberation
docker exec -it tilefy ls /usr/share/fonts/truetype/ttf-bitstream-vera
```

Provide your custom font by adding them to `/data/fonts` in TTF format only and add them with their relative path like `fonts/font-name.ttf`.

### Humanize (Optional)
Defaults to `true` for all numbers. Shorten long numbers into a more human-readable string, like *14502* to *14.5K*.

### Recreate (Optional)
Set the lifetime of your tiles and define when the tile will be recreated if requested. Defaults to *1d*, e.g., recreate every day.

Valid options:
- *120*: A number indicates seconds until expiration
- *10min*: Minutes until expiration
- *2h*: Hours until expiration
- *1d*: Days until expiration
- *on_demand*: Will recreate for every request

Note: 
- Be aware of any rate limiting and API quotas you might face with too short an expiration
- There is a failsafe in place to block recreating tiles faster than every 60 seconds

## API Requests
Get values from a public API by providing the URL and key map.

### URL
JSON API endpoint. If possible, filter and reduce the API response size by requesting only the required fields.

### Key Map
Navigate the JSON object to find your desired value. Each item in the list navigates one level deeper: a `string` accesses a key, and an `int` accesses an index in an array. 

*Example 1*
```json
{
    "pull_count": 269912,
    "star_count": 11
}
```
To access the *pull_count* key in the top level of the response:
```yml
key_map:
  - pull_count
```

*Example 2*
```json
{
    "results": [
        {
            "status": "success",
            "last_run": "timestamp"
        }
    ]
}
```

To access the *status* key in the first dictionary of the results list:
```yml
key_map:
  - results
  - 0
  - status
```

## Plugins
For all values not accessible over a JSON API endpoint, a dedicated solution will be needed, for example, by scraping the website. This is inherently less reliable as websites change more frequently than APIs.

### Chrome Extension Users
Get the number of active Chrome extension users:
```yml
plugin:
  name: chrome-extension-users
  id: jjnkmicfnfojkkgobdfeieblocadmcie
```

Please contribute and open feature requests to add more.

## Donate
The best donation to **Tilefy** is your time; contribute in any way you can to improve this project.  
The second-best way to support the development is to provide caffeinated beverages:
* [PayPal.me](https://paypal.me/bbilly1) for a one-time coffee
* [PayPal Subscription](https://www.paypal.com/webapps/billing/plans/subscribe?plan_id=P-03770005GR991451KMFGVPMQ) for a monthly coffee
* [Ko-fi.com](https://ko-fi.com/bbilly1) for an alternative platform
