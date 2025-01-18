---
weight: 200
title: "Installation"
description: "How to install Filebrowser"
icon: "settings"
date: "2025-01-15"
lastmod: "2025-01-15"
draft: false
---

## Install

Using docker:

1. docker run (no persistent db):

```
docker run -it -v /path/to/folder:/srv -v $(pwd)/config.yaml:/home/filebrowser/config.yaml -p 80:80 gtstef/filebrowser
```

or optionally, as non-root filebrowser user:

```
docker run -u filebrowser -it -v $(pwd)/config.yaml:/home/filebrowser/config.yaml -v /path/to/folder:/srv -p 80:80 gtstef/filebrowser
```

1. docker compose:

  - with local storage

```
services:
  filebrowser:
    volumes:
      - '/path/to/folder:/srv' # required (for now not configurable)
      # optional if you want db to persist - configure a path under "database" dir in config file.
      - './database:/home/filebrowser/database'
      - './config.yaml:/home/filebrowser/config.yaml'
    ports:
      - '80:80'
    image: gtstef/filebrowser
    # optionally run as non-root filebrowser user
    #user: filebrowser
    restart: always
```

  - with network share

```
services:
  filebrowser:
    volumes:
      - 'storage:/srv' # required (for now not configurable)
      # optional if you want db to persist - configure a path under "database" dir in config file.
      - './database:/home/filebrowser/database'
      - './config.yaml:/home/filebrowser/config.yaml'
    ports:
      - '80:80'
    image: gtstef/filebrowser
    restart: always
volumes:
  storage:
    driver_opts:
      type: cifs
      o: "username=admin,password=password,rw" # enter valid info here
      device: "//192.168.1.100/share/"         # enter valid info here

```

Not using docker (not recommended), download your binary from releases and run with your custom config file:

```
./filebrowser -c <config.yaml or other /path/to/config.yaml>
```

## Example frontend config:
```
frontend:
  name: "Your custom name"
  disableDefaultLinks: true
  externalLinks:
    - text: "Your custom link"
      url: "https://example.com"
```

## Example advanced source config:
```
server:
  port: 80
  sources:
    - name: "default"
      path: "/mnt/folder"
      config:
        ignoreHidden: true
        ignoreZeroSizeFolders: true
        exclude:
          files:
            - "/myfile.txt" # for example, corresponds to "/mnt/folder/myfile.txt"
            - "/subfolder/another.txt"
          folders:
            - "/subfolder/ignoreMe"
          fileEndsWith:
            - ".zip" # excludes any files that end with ".zip"
            - ".tar.gz"
            - "-hidden.jpg"
```

## All possible configurations

Here is an expanded config file which includes all possible configurations:

```
server:
  CreateUserDir: false
  UserHomeBasePath: ""
  indexing: true
  numImageProcessors: 4
  socket: ""
  tlsKey: ""
  tlsCert: ""
  enableThumbnails: false
  resizePreview: true
  typeDetectionByHeader: true
  port: 80
  baseURL: "/"
  address: ""
  log: "stdout"
  database: "/database/database.db"
  root: "/srv"
  sources: # optional only needed for custom index configuration, can use root still for default index config.
auth:
  adminUsername: admin
  adminPassword: admin
  recaptcha:
    host: ""
    key: ""
    secret: ""
  tokenExpirationTime: 2h
  header: ""
  method: json
  signup: false
frontend:
  name: ""
  disableDefaultLinks: false
  disableUsedPercentage: true
  externalLinks:
    - text: "Your custom link"
      url: "https://example.com"
      title: "this text appears when hovering over link"
  color: ""
userDefaults:
  settingsAllowed: true
  darkMode: false
  scope: ""
  locale: "en"
  viewMode: ""
  singleClick: true
  permissions:
    admin: true
    execute: true
    create: true
    rename: true
    modify: true
    delete: true
    share: true
    download: true
    api: true
  hideDotfiles: false
  dateFormat: false
```

Here are the defaults if nothing is set:

```
server:
  enableThumbnails: true
  enableExec: false
  port: 80
  numImageProcessors: 4
  baseURL: ""
  database: database.db
  log: stdout
  root: /srv
  sources:
auth:
  adminUsername: admin
  adminPassword: admin
  method: password
  recaptcha:
    host: ""
userDefaults:
  settingsAllowed: true
  darkMode: false
  locale: "en"
  scope: "."
  lockPassword: false
  hideDotfiles: true
  permissions:
    create: true
    rename: true
    modify: true
    delete: true
    share: true
    download: true
    api: false
```

## About each configuration

# Configuration Settings Documentation

## About each configuration

### Server configuration settings

- `indexingInterval`: This optional paramter disables smart indexing and specifies a time in minutes the system waits before checking for filesystem changes. See [indexing readme](indexing.md) for more information.

- `indexing`: This enables or disables indexing. (Note: search will not work without indexing) Default: `true`

- `numImageProcessors`: This is the number of image processors available. Default: `4`

- `socket`: This is the socket configuration.

- `tlsKey`: This is the TLS key configuration.

- `tlsCert`: This is the TLS certificate configuration.

- `enableThumbnails`: This boolean value determines whether thumbnails are enabled on ui. Default: `true`

- `resizePreview`: This boolean value determines whether preview resizing is enabled. Default: `false`

- `typeDetectionByHeader`: This boolean value determines whether type detection is based on headers.

- `port`: This is the port number on which the server is running. Default: `80`

- `baseURL`: This is the base URL for the server. Default: `""`

- `address`: This is the server address configuration. Default: `0.0.0.0`

- `log`: This specifies the log destination. Default: `stdout`

- `database`: This is the database file path + filename that will be created if it does not already exist. If it exists, it will use the existing file. Default `database.db`

- `root`: This is the root directory path. Default: `/srv`

- `CreateUserDir`: Boolean to create user directory on user creation. Default: `false`

- `UserHomeBasePath`: String to define user home directory base path. Default: `""`

- `externalUrl`: String to define a domain and potentially additional subpath for shared links to use. Example "https://mydomain.com/", Default: `""`


### Auth configuration settings

- `recaptcha`:

  - `host`: This is the host for reCAPTCHA.

  - `key`: This is the reCAPTCHA key.

  - `secret`: This is the reCAPTCHA secret.

- `header`: This is the authentication header.

- `method`: This is the authentication method used (e.g., "json"). Possible values:
  - `password` - username and password
  - `hook` - hook authentication
  - `proxy` - proxy authentication
  - `oath` - oath authentication
  - `noauth` - no authentication/login required.

- `Signup`: This boolean value indicates whether user signup is enabled on the login page. NOTE: Be mindful of `userDefaults` settings if enabled. Default: `false`

- `AdminUsername`: This is the username of the admin user. Default: `admin`

- `AdminPassword`: This is the password of the admin user. Default: `admin`

### Frontend configuration settings

- `name`: This is the name of the frontend.

- `disableExternal`: This boolean value determines whether external access is disabled.

- `disableUsedPercentage`: This boolean value determines whether used percentage is disabled.

- `files`: This is the files configuration.

- `theme`: This is the theme configuration.

- `color`: This is the color configuration.

### UserDefaults configuration settings

- `darkMode`: Determines whether dark mode is enabled for the user (`true` or `false`)

- `settingsAllowed`: Determines whether settings page is enabled for the user (`true` or `false`)

- `scope`: This is a scope of the permissions, "." or "./" means all directories, "./downloads" would mean only the downloads folder.

- `locale`: String locale configuration. Default: `en`

- `viewMode`: This is the view mode configuration. Possible values: `normal`, `compact`, `list`, and `gallery`. default: `normal`

- `singleClick`: This boolean value determines whether single-click is enabled. (`true` or `false`)

- `sorting`:

  - `by`: This is the sorting method used (e.g., "asc").

  - `asc`: This boolean value determines the sorting order is ascending or descending. (`true` or `false`)

- `permissions`:

  - `admin`: This boolean value determines whether admin permissions are granted.

  - `execute`: This boolean value determines whether execute permissions are granted.

  - `create`: This boolean value determines whether create permissions are granted.

  - `rename`: This boolean value determines whether rename permissions are granted.

  - `modify`: This boolean value determines whether modify permissions are granted.

  - `delete`: This boolean value determines whether delete permissions are granted.

  - `share`: This boolean value determines whether share permissions are granted.

  - `download`: This boolean value determines whether download permissions are granted.

  - `api`: Ability to create and manage API keys.

- `hideDotfiles`: This boolean value determines whether dotfiles are hidden. (`true` or `false`)

- `dateFormat`: This boolean value determines whether date formatting is enabled. (`true` or `false`)