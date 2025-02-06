# Dockerized Jottacloud Client
<!-- ALL-CONTRIBUTORS-BADGE:START - Do not remove or modify this section -->
[![All Contributors](https://img.shields.io/badge/all_contributors-1-orange.svg?style=flat-square)](#contributors-)
<!-- ALL-CONTRIBUTORS-BADGE:END -->
Docker image with Jottacloud client side backup daemon, using jotta-cli and jottad.

Jottacloud is a Cloud Storage (backup) service provider, which offers [unlimited storage space](https://www.jottacloud.com/en/pricing.html) for personal use.

Supported platforms: linux/amd64, linux/arm64

**IMPORTANT: Upstream BREAKING CHANGES in 0.12 (Version 0.12.50392 - 2021-10-26)**
https://docs.jottacloud.com/en/articles/1461561-release-notes-for-jottacloud-cli

## Repository
- GitHub: [bluet/docker-jottacloud](https://github.com/bluet/docker-jottacloud/)
- DockerHub: [bluet/jottacloud](https://hub.docker.com/r/bluet/jottacloud)

```
docker pull bluet/jottacloud
```

## Setup
### docker run
To start a long running jottacloud backup client, the easy way:
```
docker run \
   -e JOTTA_TOKEN=XXXXX \
   -e JOTTA_DEVICE=YYYY \
   -v /dockerdata/jottacloud/config:/data/jottad \
   -v /home/:/backup/home \
   bluet/jottacloud
```
To start a long running jottacloud backup client, with custom configs:
```
docker run \
   -e JOTTA_TOKEN=XXXXX \
   -e JOTTA_DEVICE=YYYY \
   -e JOTTA_SCANINTERVAL=12h \
   -e LOCALTIME=ZZZ/ZZZ \
   -v /data/jottacloud/config:/data/jottad \
   -v /data/jottacloud/ignore:/data/jottad/.ignore \
   -v /data/jottacloud/jottad.env:/data/jottad/jottad.env \
   -v /home/:/backup/home \
   --name jottacloud \
   bluet/jottacloud
```
### docker compose
1. Copy the example `docker-compose.yml` and `.env` files.
2. Change the values of the `.env` file to your liking.
3. Run `docker compose up -d` from the same folder that your compose and env files are in.

## Use
For debugging:
```
docker run -it bluet/jottacloud bash
```
For debugging a running container:
```
docker exec -it jottacloud bash
```

## Volume mount-point
Path | Description | Required |
------------ | ------------ | :------------: |
`/data/jottad` | Config and data. In order to keep login status and track backup progress, please use a persistent volume. | ✔ |
`/data/jottad/.ignore` | exclude pattern [#Exclude]
`/data/jottad/jottad.env` | Environment variables for jottad (jotta-cli) container
`/backup/` | Data you want to backup. ex, `-v /home/:/backup/home/`, or -v `/backup/:/backup/`. | ✔ |

## ENV
Environment variables loading sequence and priority:
1. Default values.
2. Set in docker environment, by `docker run -e` parameter or docker-compose environment. (Overrides all above)
3. `/data/jottad/jottad.env` file. (Overrides all above)
4. Docker secret key `jotta_token`. (Overrides all above)

Name | Value | Default | Required |
------------ | ------------ | :------------: | :------------: |
JOTTA_TOKEN | Your `Personal login token`. Please obtain it from Jottacloud dashboard [Settings -> Security](https://www.jottacloud.com/web/secure). This will only show once and can only be used in a short time, so please use persistent volume on `/data/jottad/` to save your login status. | `N/A` | ✔ |
JOTTA_DEVICE | Device name of the backup machine.  Used for identifying which machine these backup data belongs to. | `docker-jottacloud` | ✔ |
JOTTA_SCANINTERVAL | Interval time of the scan-and-backup. Can be `1h`, `30m`, or `0` for realtime monitoing. | `12h`
LOCALTIME | Local timezone. ex, `Asia/Taipei` | `Asia/Taipei`
STARTUP_TIMEOUT | How many seconds to wait before retrying startup. | `15`


## Exclude
**IMPORTANT: Since 0.12.50392 (2021-10-26) this no longer works. PRs welcome.**
https://docs.jottacloud.com/en/articles/1461561-release-notes-for-jottacloud-cli

It's recommend to exclude some files/folders from being upload, to avoid [triggering speed limit](https://docs.jottacloud.com/en/articles/3271114-reduced-upload-speed) or for security reasons.

To do so, jotta-cli supports two different ways:
- Global excludes
   - Mount or edit `/config/.ignore` directly.
- Folder specific excludes
  - Put a `.jottaignore` in that folder.

You can also check my sample [.jottaignore](https://github.com/bluet/docker-jottacloud/blob/main/.jottaignore) file.

**Syntax / Pattern**: `.ignore`, `ignorefile`, and `.jottaignore` are `.gitignore` compatible.  You can check templates in [github/gitignore](https://github.com/github/gitignore) or use [Gitignore.io](https://gitignore.io) to generate one for you.

**NOTE**: Adding a new pattern will also apply to files already backup. If you already have a backup which contains `/foo/bar/` and later adds a new pattern `bar/` in ignore list, the `bar/` folder will be removed from all your previous backups and moved to Trash.

## Result
![2021-05-21 09-37-19 的螢幕擷圖](https://user-images.githubusercontent.com/51141/119069168-32407a80-ba18-11eb-824d-82a60d13437a.png)

# Detailed official configuration guide of jotta-cli
- [Jottacloud CLI Configuration
](https://docs.jottacloud.com/en/articles/2750154-jottacloud-cli-configuration)
- [Ignoring files and folders from backup with Jottacloud CLI](https://docs.jottacloud.com/en/articles/1437235-ignoring-files-and-folders-from-backup-with-jottacloud-cli)

## Credit
This is a fork from [maaximal/jottadocker](https://github.com/maaximal/jottadocker) with some improvements and fixes to support latest jottacloud CLI and other additional features.

## Contributors ✨

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tr>
    <td align="center"><a href="https://github.com/haakemon"><img src="https://avatars.githubusercontent.com/u/2082481?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Håkon</b></sub></a><br /><a href="https://github.com/bluet/docker-jottacloud/commits?author=haakemon" title="Code">💻</a> <a href="#ideas-haakemon" title="Ideas, Planning, & Feedback">🤔</a></td>
  </tr>
</table>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!
