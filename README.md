# Jetpack

:fire: A friendly package manager for R

- Designed for reproducibility (thanks to [Packrat](https://rstudio.github.io/packrat/), no more global installs!)
- Lightweight (adds just three files to your project)
- Secure by default
- Works from both R and the command line

![Screenshot](https://gist.github.com/ankane/b6988db2802aca68a589b31e41b44195/raw/bd6c163ef01a39aa3efc882fee5a82c75f002a61/jetpack.png)

Inspired by [Yarn](https://yarnpkg.com/), [Bundler](https://bundler.io/), and [Pipenv](https://docs.pipenv.org/)

[![Build Status](https://travis-ci.org/ankane/jetpack.svg?branch=master)](https://travis-ci.org/ankane/jetpack)

## Installation

Install Jetpack

```r
install.packages("devtools")
devtools::install_github("ankane/jetpack@v0.3.0")
```

## How It Works

Jetpack creates a `DESCRIPTION` file to store your project dependencies. It stores the specific version of each package in `packrat.lock`. This makes it possible to have a reproducible environment. You can edit dependencies in the `DESCRIPTION` file directly, but Jetpack provides functions to help with this.

## Getting Started

Open a project and run:

```r
jetpack::init()
```

## Commands

### Install

Install packages for a project

```r
jetpack::install()
```

This ensures all the right versions are installed locally. As dependencies change, collaborators should run this command to stay synced.

### Add

Add a package

```r
jetpack::add("randomForest")
```

Add multiple packages

```r
jetpack::add(c("randomForest", "DBI"))
```

Add a specific version

```r
jetpack::add("DBI@1.0.0")
```

Add from GitHub or another remote source

```r
jetpack::add("plyr", remote="hadley/plyr")
```

Supports [all of these remotes](https://cran.r-project.org/web/packages/devtools/vignettes/dependencies.html)

Add from a specific tag, branch, or commit

```r
jetpack::add("plyr", remote="hadley/plyr@v1.8.4")
```

Add from a local source

```r
jetpack::add("plyr", remote="local::/path/to/plyr")
```

> The local directory must have the same name as the package

### Update

Update a package

```r
jetpack::update("randomForest")
```

> For local packages, run this anytime the package code is changed

Update multiple packages

```r
jetpack::update(c("randomForest", "DBI"))
```

### Remove

Remove a package

```r
jetpack::remove("randomForest")
```

Remove multiple packages

```r
jetpack::remove(c("randomForest", "DBI"))
```

Remove remotes as well

```r
jetpack::remove("plyr", remote="hadley/plyr")
```

### Check

Check that all dependencies are installed

```r
jetpack::check()
```

### Info

Get info for a package

```r
jetpack::info("stringr")
```

Get info for a specific version

```r
jetpack::info("stringr@1.0.0")
```

### Search

Search for packages

```r
jetpack::search("xgboost")
```

Works with title, description, authors, maintainers, and more

## Source Control

Be sure to commit the files Jetpack generates to source control.

## Deployment

### Server

Install Jetpack on the server and run:

```r
jetpack::install(deployment=TRUE)
```

### Docker

Create an `init.R` with:

```r
install.packages("devtools")
devtools::install_github("ankane/jetpack@v0.3.0")
jetpack::install(deployment=TRUE)
```

And add it into your `Dockerfile`:

```Dockerfile
FROM r-base

RUN apt-get update && apt-get install -qq -y --no-install-recommends \
  libxml2-dev libssl-dev libcurl4-openssl-dev libssh2-1-dev

RUN mkdir -p /app
WORKDIR /app

COPY init.R DESCRIPTION packrat.lock ./
RUN Rscript init.R

COPY . .

CMD Rscript app.R
```

### Heroku

There’s [ongoing work](https://github.com/virtualstaticvoid/heroku-buildpack-r/issues/110) to get Packrat working with the [R buildpack](https://github.com/virtualstaticvoid/heroku-buildpack-r).

In the meantime, you can use [Docker Deploys on Heroku](https://devcenter.heroku.com/articles/container-registry-and-runtime).

## Command Line

Jetpack can also be run from the command line. To install the CLI, run:

```r
jetpack::cli()
```

> On Windows, add `C:\ProgramData\jetpack\bin` to your PATH. See [instructions](https://www.howtogeek.com/118594/how-to-edit-your-system-path-for-easy-command-line-access/) for how to do this.

All the Jetpack commands are now available

```sh
jetpack init
jetpack install
jetpack add randomForest
jetpack add DBI@1.0.0
jetpack add plyr --remote=hadley/plyr
jetpack update randomForest
jetpack remove DBI
jetpack check
```

You can also use it to manage global packages

```sh
jetpack global add randomForest
jetpack global update DBI
jetpack global remove plyr
jetpack global list
```

Or get info about packages

```sh
jetpack info stringr
jetpack info stringr@1.0.0
jetpack search xgboost
jetpack search "neural network"
```

For the full list of commands, use:

```sh
jetpack help
```

## Upgrading

To upgrade, rerun the [installation instructions](#installation).

### 0.3.0

Jetpack 0.3.0 greatly reduces the number of files in your projects. To upgrade a project:

1. Move `packrat/packrat.lock` to `packrat.lock`
2. Delete the `packrat` directory
3. Delete `.Rbuildignore` and `.gitignore` if they only contain Packrat references
4. Replace all Jetpack and Packrat code in your `.Rprofile` with:

  ```r
  if (requireNamespace("jetpack", quietly=TRUE)) {
    jetpack::load()
  } else {
    message("Install Jetpack to use a virtual environment for this project")
  }
  ```

5. Open R and run:

  ```r
  jetpack::install()
  ```

## History

View the [changelog](https://github.com/ankane/jetpack/blob/master/NEWS.md)

## Contributing

Everyone is encouraged to help improve this project. Here are a few ways you can help:

- [Report bugs](https://github.com/ankane/jetpack/issues)
- Fix bugs and [submit pull requests](https://github.com/ankane/jetpack/pulls)
- Write, clarify, or fix documentation
- Suggest or add new features
