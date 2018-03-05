# FileBazaar

Sell digital files with Lightning!

- Simple setup and minimal configuration, just put a bunch of files in a directory and run the server.

- Lightweight web browsing interface, works without JavaScript.

- Generates previews for images, videos, audio, pdf and text documents.

## Quickstart

Setup [Lightning Charge](https://github.com/ElementsProject/lightning-charge), then:

```bash
# Install dependencies for EXIF extraction and preview generation
$ apt-get install exiftool ffmpeg graphicsmagick unoconv

# node-canvas dependencies, see https://github.com/Automattic/node-canvas#installation
$ apt-get install libcairo2-dev libjpeg8-dev libpango1.0-dev libgif-dev build-essential g++

# Install filebazaar
$ npm install -g filebazaar

# Prepare a directory with the files you wish to sell and cd to it
$ mkdir ~/ForSale && cd ~/ForSale

# Initialize the `_filebazaar.yaml` config file
$ filebazaar init

# Start filebazaar!
$ filebazaar
```

## Screenshots

<img src="https://i.imgur.com/Tdwcuhm.png" width="23%"></img>
<img src="https://i.imgur.com/6SFNRiq.png" width="23%"></img>
<img src="https://i.imgur.com/MDRaie2.png" width="23%"></img>
<img src="https://i.imgur.com/yjwjfT8.png" width="23%"></img>

## Configuration

FileBazaar's configuration options can be managed using the `_filebazaar.yaml` file or via environment variables.
All config options are optional and have sane defaults, except for `token_secret` which is required. See [`lib/config.js`](https://github.com/shesek/filebazaar/blob/master/lib/config.js) for more details.

Below is an example `_filebazaar.yaml` file:

```yaml
---

### Server settings

port: 9678
host: 127.0.0.1
env: production
url: http://my-public-url.com/

### Lightning Charge

charge_url: http://localhost:9112
charge_token: API_TOKEN_CONFIGURED_IN_CHARGE

### FileBazaar settings

# The directory containing the files for sale
# defaults to the directory containing the _filebazaar.yaml file
directory: /home/shesek/ForSale

# The default file price, can be overridden for individual files (see below)
default_price: 0.25 USD

# Expiry times
invoice_ttl: 3600 # lock-in exchange rate for 1 hour
download_ttl: 172800 # make download available for 2 days after payment

# Secret for generating HMAC access tokens (required)
token_secret: SOME_LONG_RANDOM_STRING

# Directory to keep cached preview files
# defaults to `{directory}/_filebazaar_cache`
cache_path: /path/to/filebazaar_cache

### Looks & feel

# See available themes on https://bootswatch.com
theme: yeti

# Add custom CSS
css: |
  body { background: blue }
  a { color: orange }

# Set custom views directory
views_dir: /path/to/custom/views

# Set custom static files directory
static_dir: /path/to/custom/static

### Files settings

files:
  Books/Mastering-Bitcoin.pdf:
    price: 5 USD
    button: Buy this book
    desc: >      
      Mastering Bitcoin is essential reading for everyone interested in learning about bitcoin.
      
      This field **supports markdown** and will show up on the file's page.

  # if you're only interested in setting the price, you can use:
  Books/Mastering-Bitcoin.pdf: 5 USD

  # if you want to configure multiple files inside the same directory, you can nest them:
  Media/: # (note the trailing slash)
    Books/Andreas/:
      Mastering-Bitcoin.pdf: 5 USD # /Media/Books/Andreas/Mastering-Bitcoin.pdf
      The-Internet-of-Money.pdf: 4 USD # /Media/Books/Andreas/The-Internet-of-Money.pdf
```

## CLI

You can use `$ filebazaar init [directory]` to initialize a new `_filebazaar.yaml` config file. A random `token_secret` will be added for you. If no `[directory]` is specified, the file will be created in the working directory.

To start FileBazaar, run `$ filebazaar [path]`. You can either specify the path to the files directory or to the `_filebazaar.yaml` file. If none is specified, defaults to the working directory.

## File Preview

FileBazaar can currently generate previews for the following file types:

- Images: a preview image will be generated by pixelating the left-half and adding watermark text using [node-canvas](https://github.com/Automattic/node-canvas) and [graphicsmagick](http://www.graphicsmagick.org) (see [example image](https://i.imgur.com/OmrUysL.png)).

- Videos & audio: a preview will be generated by slicing off the first 30 seconds using [ffmpeg](http://ffmpeg.org).

- Documents: a preview image of the first page of the document will be generated using [unoconv](https://github.com/dagwieers/unoconv) (supports pdf, doc, docx, odt, and many others).

In addition, EXIF metadata will be extracted using [exiftool](https://www.sno.phy.queensu.ca/~phil/exiftool/) and displayed for all file types.

## License

MIT
