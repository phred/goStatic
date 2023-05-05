# smolboi 🖭

A mixtape 🖭 playing the greatest hits from [goStatic](https://github.com/PierreZ/goStatic) with a little special sauce all my my own.

## The goals
- Wafer thin OCI image
- Aerogel-weight Nix build
- Handle the boring-ass job of Serving [my website](https://fff.red) to my 2 visitors a week (a guess, thanks for reading if you do! at present I don't track metrics on this and I hope I have >0 visitors)

The original project's goal was "to create to smallest docker container for my web static files." I'm tired of Docker and want something smaller, and simpler, if I can get it.

So far I have added virtualhost support and have been running my site on it for a couple years now.

I run this in a fly.io app and have very quick builds and deploys, tyvm. Few moving parts.

### Wait, can I use this code?

Go for it, take my changes and make your own remix. What you do with that code is up to you. I'm not going to add any features that aren't related to making my webspace cooler.

I'm planning to or remix features I don't need such as:
 * Specify custom response headers per path and filetype [(info)](./docs/header-config.md) 

## Features
 * A fully static web server embedded in a `SCRATCH` image
 * No framework
 * Web server built for Docker
 * Light container
 * More secure than official images (see below)
 * Log enabled
 * Virtual Hosting
 * (soon) custom 404 pages.

## Why?
Because Caddy2 is too complex, and I don't want to configure yet another instance of Apache or Nginx. I like Deno, but I like Go's production-grade standard library HTTP server better. Rust is cool but makes fat binaries too. Go also makes fat binaries, but this project has a minimal [set of dependencies](./go.mod)

The world is full of terrible, bloated software, fat containers, node_modules by the truckload. Big binaries have hidden externalities in transport, storage, compute time. My needs are simple and they fit in a single binary, [as things should](https://fossil-scm.org/).

Thank you, PierreZ, for showing me the way. Software complexity will eat us all, best to only use what you need for the job.

## How to use
```
docker run -d -p 80:8043 -v path/to/website:/srv/http --name smolboi phred/smolboi
```

## Usage 

```
./smolboi --help
Usage of ./smolboi:
  -append-header HeaderName:Value
        HTTP response header, specified as HeaderName:Value that should be added to all responses.
  -context string
        The 'context' path on which files are served, e.g. 'doc' will serve the files at 'http://localhost:<port>/doc/'
  -default-user-basic-auth string
        Define the user (default "gopher")
  -enable-basic-auth
        Enable basic auth. By default, password are randomly generated. Use --set-basic-auth to set it.
  -enable-health
        Enable health check endpoint. You can call /health to get a 200 response. Useful for Kubernetes, OpenFaas, etc.
  -enable-logging
        Enable log request
  -fallback string
        Default fallback file. Either absolute for a specific asset (/index.html), or relative to recursively resolve (index.html)
  -header-config-path string
        Path to the config file for custom response headers (default "/config/headerConfig.json")
  -https-promote
        All HTTP requests should be redirected to HTTPS
  -password-length int
        Size of the randomized password (default 16)
  -path string
        The path for the static files (default "/srv/http")
  -port int
        The listening port (default 8043)
  -set-basic-auth string
        Define the basic auth. Form must be user:password
```

### Fallback

The fallback option is principally useful for single-page applications (SPAs) where the browser may request a file, but where part of the path is in fact an internal route in the application, not a file on disk. goStatic supports two possible usages of this option:

1. Using an absolute path so that all not found requests resolve to the same file
2. Using a relative file, which searches up the tree for the specified file

The second case is useful if you have multiple SPAs within the one filesystem. e.g., */* and */admin*.


## Build

### Docker images
```bash
docker buildx create --use --name=cross
docker buildx build --platform=linux/amd64,linux/arm64,linux/arm/v5,linux/arm/v6,linux/arm/v7,darwin/amd64,darwin/arm64,windows/amd64 .
```
