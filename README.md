Red Yum
==========

This git repo creates a YUM repository with all packages and group
install information necessary to create all of the Redgates server types.
The default target it to create this YUM repository, but there is also
a target for pushing the entire YUM repository to a yum S3 bucket.

The build process gathers the rpms for the various parts of the
servers from the latest github release of each of the packages.
It uses the github API to determine the latest release and the URI
from which to download the rpm asset associated with the release.

The YUM groups used to install each server are defined in the
`comps.xml` file, which is copied into the yum repository before
generating the repository metadata.

## Use Cases

There are two consumers of this repo.

 1. Deployment pipeline for automated testing
 2. Engineers testing

An engineer should be able to clone this repo and run:

```
make
# or
make push
```

To build yum repositories and/or push them to S3.  It should be possible
to alter the S3 bucket that the yum repo is pushed to, in order to allow
development testing.

The default push target should be the deployment pipeline.  The first step
in the deployment pipeline should be a `dev` yum repo.  The promotion of
the repo to the `test`, `stage`, `prod` yum repos is performed by a command
like `aws s3 cp ...` 

The structure of the S3 data is:

```
http://yum.redgates.s3.amazonaws.com/dev/{packages,repodata}
http://yum.redgates.s3.amazonaws.com/test/{packages,repodata}
http://yum.redgates.s3.amazonaws.com/stage/{packages,repodata}
http://yum.redgates.s3.amazonaws.com/prod/{packages,repodata}
```

To connect to S3, an Access key, Secret key, and sufficient role is required.
We can have this handled a few ways:

 1. We will have a server give out keys after making an initial request. 
    Git ssh access can be "key only" and so a request with a particular key 
    will yield a response with a corresponding access and secret key that are
    time sensitive.
 2. We have a role that provides time sensitive keys when a policy is provided.

## TODO

 * [ ] Add spec file for yum repo rpm
 * [ ] Add cloud-init for the other server types
 * [ ] Add tools/rules for pushing yum repo data to S3 bucket.
 * [ ] Add mechanism to get S3 keys for the upload.
 * [ ] Add release variable usage to the spec file.

> The keys may just be for the current user.
> If we use jenkins, the jenkins server would need these keys also.
> The AWS keys **MUST NOT** be checked in to source control.

## Later

 * Add similar config for the Skynet server
