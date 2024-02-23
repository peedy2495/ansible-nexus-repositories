# ansible-nexus-repositories
This external role is to comply project related dependencies of proxy cached content.

## Prerequisites

- You have to load the user's password into the variable `nexusPwd`
- You have to load json content defining your required repositories into the variable `nexusRepos`. 

For example loading the content from a json file:
```yaml
- name: import repositories
  set_fact:
    nexus_repos: "{{ lookup('file','vars/my_nexus_repos.json') | from_json }}"
```

Refer your nexus-instance in Setup/System/API for reqired json structs.

Example json content:
```json
[
  {
    "name": "ubuntu-jammy-ppa-golang-backports",
    "url": "http://192.168.123.180:8081/repository/ubuntu-jammy-ppa-golang-backports",
    "online": true,
    "storage": {
      "blobStoreName": "ubuntu-jammy",
      "strictContentTypeValidation": true,
      "writePolicy": "ALLOW"
    },
    "cleanup": {
      "policyNames": ["18-weeks-usage"]
    },
    "apt": {
      "distribution": "jammy",
      "flat": false
    },
    "proxy": {
      "remoteUrl": "https://ppa.launchpadcontent.net/longsleep/golang-backports/ubuntu/",
      "contentMaxAge": 1440,
      "metadataMaxAge": 1440
    },
    "negativeCache": {
      "enabled": true,
      "timeToLive": 1440
    },
    "httpClient": {
      "blocked": false,
      "autoBlock": true,
      "connection": {
        "retries": null,
        "userAgentSuffix": null,
        "timeout": null,
        "enableCircularRedirects": false,
        "enableCookies": false,
        "useTrustStore": false
      },
      "authentication": null
    },
    "routingRuleName": null,
    "format": "apt",
    "type": "proxy"
  },
  {
    "name": "nuget.org-proxy",
    "url": "http://192.168.123.180:8081/repository/nuget.org-proxy",
    "online": true,
    "storage": {
      "blobStoreName": "default",
      "strictContentTypeValidation": true,
      "writePolicy": "ALLOW"
    },
    "cleanup": null,
    "proxy": {
      "remoteUrl": "https://api.nuget.org/v3/index.json",
      "contentMaxAge": 1440,
      "metadataMaxAge": 1440
    },
    "negativeCache": {
      "enabled": true,
      "timeToLive": 1440
    },
    "httpClient": {
      "blocked": false,
      "autoBlock": false,
      "connection": {
        "retries": null,
        "userAgentSuffix": null,
        "timeout": null,
        "enableCircularRedirects": false,
        "enableCookies": false,
        "useTrustStore": false
      },
      "authentication": null
    },
    "routingRuleName": null,
    "nugetProxy": {
      "queryCacheItemMaxAge": 3600,
      "nugetVersion": "V3"
    },
    "format": "nuget",
    "type": "proxy"
  }
]
```

Tip: you can create a backup of your current structure calling the api-url with  
`{url to your nexus instance}/service/rest/v1/repositorySettings`.  
You can use it as a loaded JSON variable to restore your repository, group, and blobstore structure, or simply for cheating on the structure ;-)

## How does it work?

- Every defined blob store within the repos will be build as fileblob, if it doesn't exist.
- Every defined repository or group will be created, if it's absent.
- Every defined repository or group will be updated with the json's content.

## Usage

Import this role to your project with fulfied prerequisites.