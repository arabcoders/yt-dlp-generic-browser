A [yt-dlp](https://github.com/yt-dlp/yt-dlp) extractor [plugin](https://github.com/yt-dlp/yt-dlp#plugins) that override yt-dlp generic extractor to use
playwright or selenium for extracting media from websites.

Pass `-use-extractors generic --extractor-args "generic:url=(server_url)"` to use this plugin.

# How to use the extractor?

This is mainly useful for sites where the regular extractor or generic page parsing does not expose the final media url but a real browser session does.

## Environment variables

You can define the remote browser endpoint globally with the following environment variable.

### Selenium example

```env
YTP_BROWSER_URL=selenium+http://selenium:4444/wd/hub
```

### Playwright example

For a native Playwright server, point to the Playwright websocket endpoint:

```env
YTP_BROWSER_URL=playwright+ws://playwright:3000/
```

### Playwright CDP example

To connect Playwright over the Chrome DevTools Protocol, use either the shorthand `playwright+cdp://` form or an explicit transport form:

```env
YTP_BROWSER_URL=playwright+cdp://chrome:9222/ # same as playwright+cdp+http://chrome:9222/
```

- Supported forms are:
  - `selenium+http://...`
  - `selenium+https://...`
  - `playwright+ws://...`
  - `playwright+wss://...`
  - `playwright+cdp://...`  alias of `playwright+cdp+http://...`
  - `playwright+cdp+https://...`
  - `playwright+cdp+ws://...`
  - `playwright+cdp+wss://...`

## yt-dlp usage

If you want to set the browser extractor options directly on the yt-dlp side, you can also use `--extractor-args` with `generic:url=...`:

### Selenium example

```bash
--use-extractors "generic" --extractor-args "generic:url=selenium+http://127.0.0.1:4444/wd/hub"
```

### Playwright example

```bash
--use-extractors "generic" --extractor-args "generic:url=playwright+ws://127.0.0.1:3000/"
```

### Playwright CDP example

```bash
--use-extractors "generic" --extractor-args "generic:url=playwright+cdp://127.0.0.1:9222/"
```

The explicit `--extractor-args` value takes priority over `YTP_BROWSER_URL`.

## Example compose for testing

```yaml
services:
  selenium:
    image: selenium/standalone-chrome:latest
    container_name: selenium
    restart: unless-stopped
    shm_size: 2gb
    ports:
        - "4444:4444"
    
  playwright:
    image: mcr.microsoft.com/playwright:v1.59.0-noble
    container_name: playwright
    restart: unless-stopped
    command: /bin/sh -c "npx -y playwright@1.59.0 run-server --port 3000 --host 0.0.0.0"
    ports:
        - "9222:3000"
```

> [!NOTE]
> The browser extractor is slower than the normal extractor flow and should only be used when a site actually needs a real browser session.
> playwright require same version for both the server and the client, so make sure to use the same version in the container and in your local environment if you want to test it locally.


## Installation  

Requires yt-dlp `2023.01.01` or above. You can install this package with pip:  
```
python3 -m pip install -U https://github.com/arabcoders/yt-dlp-generic-browser/archive/master.zip
```
See [yt-dlp installing plugins](https://github.com/yt-dlp/yt-dlp#installing-plugins) for the many other ways this plugin package can be installed.  

## Disclaimer

The generic_browser.py file is extracted from the original project at [ytptube](https://github.com/arabcoders/ytptube).