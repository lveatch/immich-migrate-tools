# immich-migrate-tools
Collection of custom tools to migrate data between account and/or Immich instances.

**!!! You will need to build your own docker image based on the information below !!!**

## Tools Overview
1. Exports / Imports people names / faces associated with images from one immich account to another account.
  - This can be between two accounts on the same instance or seperate instances. Matching between accounts is done on asset name. I did not have any issues with duplicate asset names as the face match is based on the position (the square) of the face detected.
2. Exports / Imports album assets from one immich account to another account.
  - This can be between two accounts on the same instance or seperate instances. Matching between accounts is done on asset name and created date/time (fileCreatedAt) attribute as determined by Immich with the hopes of dealing with possible duplicate asset names. Assets must be previsous imported into Immich and all jobs must be complete before importing.
3. Export metadata from assets to Sidecar (XMP) files.
  - Exports Place, Faces, People to the photos xmp sidecar file.
4. Exports assets filtered on the originalPath attribute to assign asset to existing album.

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
1. Download dockerfile and docker-compose.yml found under assets/docker
2. Copy the immich.export.config and immich.import.config contents from below
3. Edit the configuration files setting your http://immich:2283 sever name and appropiate API keys. This must connect to your reverse proxy or execute from another docker host other than your Immich docker host.
  - immich.export.config
  - immich.import.config
4. Build the docker image
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

## Testing

