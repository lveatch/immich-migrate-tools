# immich-migrate-tools
Collection of custom tools to migrate data between account and/or Immich instances.

**!!! You will need to build your own docker image based on the information below !!!**

## Tools Overview
1. Exports / Imports people names / faces associated with images from one immich account to another account.
    - This can be between two accounts on the same instance or seperate instances. Matching between accounts is done on asset name. I did not have any issues with duplicate asset names as the face match is based on the position (the square) of the face detected.
    - export.people
    - import.people
2. Exports / Imports album assets from one immich account to another account.
    - This can be between two accounts on the same instance or seperate instances. Matching between accounts is done on asset name and created date/time (fileCreatedAt) attribute as determined by Immich with the hopes of dealing with possible duplicate asset names. Assets must be previsous imported into Immich and all jobs must be complete before importing.
    - export.albums
    - import.albums
3. Export metadata from external library only assets to Sidecar (XMP) files.
    - Exports Place, Faces, People to the photos xmp sidecar file.
    - export.asset.info.2.xmp
    - Volume `/mnt:/app/external/mnt` is where your external library is located.
4. Exports assets filtered on the originalPath attribute to assign asset to existing album.
    - export.assets

## Requirements:
- Each immich account requires an api key
   - Navigate to your Account settings
   - Expand **API Keys** section
   - Click **New API Key** button
   - Provide a name and click **Create**
   - Click **Copy to Clipboard**
   - Save to a secure location
   - Click **Done**
## Installing
1. Download [dockerfile](assets/docker/dockerfile) and [docker-compose.yml](assets/docker/docker-compose.yml) found under assets/docker
2. Copy the immich.export.config and immich.import.config contents from below
3. Edit the configuration files setting your http://immich.domain.tld:2283 sever name and appropiate API keys. This must connect to your reverse proxy or execute from another docker host other than your Immich docker host.
  - immich.export.config
  - immich.import.config
4. Make the following sub-directories
    - ./config
    - ./data
5. Build the docker image
   ```
   docker build -f dockerfile  -t immich-migrate-tools .
    ```

### immich.export.config
```ini
server=https://photo.domain.tld

# from@domain.tld
apiKey=QKLLxISmpuvJAzCjxQbCWg1FRPsuI8ndvMyti9sQ

```

### immich.import.config
```ini
server=https://photo.domain.tld

# to@domain.tld
apiKey=bIln15UFJwZrKJy9TeNK8BASO7t4IS9Fmof8YByd9s

```

## Executing

Base command line execution of included scripts is:
> docker compose -f docker-compose.yml run --rm shell [program function] [arguments]

### Common Arguments
- -c configuration_file 
- -configFile configuration_file
  - immich.*config file containing immich connection information
  - optional if using default names
- -test
  - Do not make changes on import-* functions. i.e. a dry run
  - optional
- -f
- -fileName
- -fileIn
- -fileOut
  - file to process for either export (outOut) or import (fileName). sorry for the lack of consistancy :(
  - optional if using default names
- -a
- -albumName
  - album name when appropiate
  - must supply one
  - use multipe -a 'albumname' as needed
- -f
- -filter
  -  filter for originalPath attribute when appropiate
 
### Executing

#### export.people
~~~
docker compose -f docker-compose.yml run --rm shell export.people [-c config_file] [-f people.export]
~~~
 
#### import.people
~~~
docker compose -f docker-compose.yml run --rm shell import.people [-c config_file] [-f people.export]
~~~ 

#### export.albums
~~~
docker compose -f docker-compose.yml run --rm shell export.albums [-c config_file] -a 'albumname 1' [-a 'albumename 2] [-f album.export]
~~~ 
#### import.albums
~~~
docker compose -f docker-compose.yml run --rm shell import.albums [-c config_file] [-f album.export]
~~~ 

#### export.assets
~~~
docker compose -f docker-compose.yml run --rm shell export.assets [-c config_file] -f 'originalPath_search_text' -a 'albumname 1' [-o assets.export]
~~~ 
> *Note:* To import run ***import.albums -f assets.export***

#### export.asset.info.2.xmp
~~~
docker compose -f docker-compose.yml run --rm shell export.asset.info.2.xmp [-c config_file]  -a 2024-04-01 -b 2024-12-31
~~~
> *Notes:*
> If `-a` is not supplied, defaults to -8 days from now.
> If `-b` is not supplied, defaults to -1 hour from now. This is useful for background scheduling to keep sidecar files updated.


## Testing

