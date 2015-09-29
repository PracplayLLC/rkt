# rkt fetch

rkt uses HTTPS to locate and download remote ACIs and their attached signatures. If the ACI exists locally, it won't be re-downloaded.

## Fetch with Meta Discovery

The easiest way to fetch an ACI is through meta discovery. rkt will find and download the ACI and signature from a location that the creator has published on their website. This process is detailed in the [Application Container specification][appc-discovery].

If you have previously trusted the image creator, it will be downloaded and verified:

```
# rkt fetch coreos.com/etcd:v2.0.0
rkt: searching for app image coreos.com/etcd:v2.0.0
rkt: fetching image from https://github.com/coreos/etcd/releases/download/v2.0.0/etcd-v2.0.0-linux-amd64.aci
Downloading aci: [=======================================      ] 3.25 MB/3.7 MB
Downloading signature from https://github.com/coreos/etcd/releases/download/v2.0.0/etcd-v2.0.0-linux-amd64.sig
rkt: signature verified:
  CoreOS ACI Builder <release@coreos.com>
sha512-fa1cb92dc276b0f9bedf87981e61ecde
```

If you haven't trusted the creator, it will be downloaded but not verified:

```
# rkt fetch coreos.com/etcd:v2.0.0
rkt: searching for app image coreos.com/etcd:v2.0.0
rkt: fetching image from https://github.com/coreos/etcd/releases/download/v2.0.0/etcd-v2.0.0-linux-amd64.aci
Downloading aci: [=======================================      ] 3.25 MB/3.7 MB
Downloading signature from https://github.com/coreos/etcd/releases/download/v2.0.0/etcd-v2.0.0-linux-amd64.sig
rkt: fetching image from https://github.com/coreos/etcd/releases/download/v2.0.0/etcd-v2.0.0-linux-amd64.aci
sha512-fa1cb92dc276b0f9bedf87981e61ecde
```

## Fetch from Specific Location

If you already know where an image is stored, you can fetch it directly:

```
# rkt fetch https://github.com/coreos/etcd/releases/download/v2.0.0/etcd-v2.0.0-linux-amd64.aci
rkt: fetching image from https://github.com/coreos/etcd/releases/download/v2.0.0/etcd-v2.0.0-linux-amd64.aci
Downloading aci: [=======================================      ] 3.25 MB/3.7 MB
Downloading signature from https://github.com/coreos/etcd/releases/download/v2.0.0/etcd-v2.0.0-linux-amd64.sig
rkt: fetching image from https://github.com/coreos/etcd/releases/download/v2.0.0/etcd-v2.0.0-linux-amd64.aci
sha512-fa1cb92dc276b0f9bedf87981e61ecde
```

## Fetch from a Docker registry

If you want to run an existing Docker image, you can fetch from a Docker registry. rkt will download and convert the image to ACI.

```
# rkt --insecure-skip-verify fetch docker://busybox
rkt: fetching image from docker://busybox
rkt: warning: signature verification has been disabled
Downloading layer: 4986bf8c15363d1c5d15512d5266f8777bfba4974ac56e3270e7760f6f0a8125
Downloading layer: ea13149945cb6b1e746bf28032f02e9b5a793523481a0a18645fc77ad53c4ea2
Downloading layer: df7546f9f060a2268024c8a230d8639878585defcc1bc6f79d2728a13957871b
Downloading layer: 511136ea3c5a64f264b78b5433614aec563103b4d4702f3ba7d4d2698e22c158
sha512-c4010045aec65aefa74770ef2bb648d9
```

Docker images do not support signature verification.

## Authentication

If you want to download an image from a private repository, then you will often need to pass credentials to be able to access it. rkt currently supports authentication for fetching images via https:// or docker:// protocols. To specify credentials you will have to write some configuration files. You can find the format of the configuration file and examples in the [configuration documentation](../configuration.md). Note that the configuration kind for images downloaded via https:// and images downloaded via docker:// is different.