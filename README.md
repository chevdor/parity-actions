# Github Actions

A collection of custom GitHub actions and examples.

## GHA Directory

-   `Download` from `Wilfried Kopp <chevdor@gmail.com>`: Download artifacts

-   `Upload` from `Wilfried Kopp <chevdor@gmail.com>`: Upload artifacts

-   `matrix-message` from `Martin Pugh (pugh@s3kr.it)`: Send a message to a matrix channel

## matrix-message

Send a message to a matrix channel

### Inputs

-   `access_token`: Access token required to send to matrix server

-   `message`: Message to send in plaintext format

-   `room_id`: Matrix room ID, specified in channels advanced settings

-   `[server]`: Matrix server hostname

### Outputs

No outputs.

## Download

Download artifacts

### Inputs

-   `chain`: Name of the chain, ie. polkadot

### Outputs

-   `[json]`: Foobar

# Upload GHA

## Description

This Github Action (GHA) helps upload artifacts to S3. it does it in a predictable way in order to provide a consistent structure of the artifacts.

This GHA also contains some helpers to help you:
- overwrite or prevent overwrite of existing artifacts
- generate checksums

## GHA Modes

The GHA offers 3 modes. It is critical that you understand those before using the action.
You can find a summary below or check the latest documentation with:

    alias release-scripts='podman run --rm -it paritytech/releng-scripts'
    release-scripts upload --help

While the `release-scripts upload` command allows 3 modes, this GHA adds a layer on top iot it and expose `modes` that may slighty differ or hide the options of the underlying tool.

-   `release REPOSITORY VERSION`: Upload the GitHub Release artifacts for a given REPOSITORY and VERSION

-   `gha`: This operation is meant to be used from inside of GitHub Workflows. It does
    not require arguments because it leverages the environment variables present
    in GitHub Workflows

-   `standard PRODUCT VERSION [ARCH]`: While the underlying tool allows any custom directory, the GHA takes care of building a standard custom path based on the product name, its version and architecture

-   `custom DIRECTORY`: This option is currenlty not supported and will likely not be available

## Upload

Upload artifacts

### Inputs

-   `[arch]`: arch

-   `aws_access_key_id`: The AWS Key to be used. Make sure it has the required access rights

-   `aws_bucket`: AWS Bucket

-   `aws_region`: The AWS region

-   `aws_secret_access_key`: The AWS secret key

-   `file_name`: input file

-   `image`: Docker image to be used. This is usually not something you want to change unless you know what you are doing

-   `mode`: Upload mode. Can be only "standard" for now.

-   `[overwrite]`: Allows overwriting if the file already exists

-   `product`: Product

-   `[sha256]`: Generate a sha256 checksum

-   `target_path`: Target path

-   `version`: version

### Outputs

-   `[url]`: URL of the uploaded artifact

## Dev notes

Here are a few snippets to help testing with S3:
You should have the creds in your env.

    AWS_BUCKET=eu-central-1
    PRODUCT=product

    # Make sure to use a rootful image if you want to export files with cp or sync

    alias awscli="podman run --label=disable --rm  -e AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY -e AWS_REGION -e AWS_BUCKET -e AWS_CONFIG_FILE -v /tmp:/tmp paritytech/awscli"
    awscli aws s3 ls ${AWS_BUCKET}/${PRODUCT} --human-readable --recursive

    awscli aws s3 sync s3://${AWS_BUCKET}/${PRODUCT} /tmp
