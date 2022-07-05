# BOSH Release for Deploying the Lacework Agent
Plug-in for deploying the [Lacework](https://www.lacework.net/) agent into environments managed by [BOSH](https://bosh.io/), such as [Cloud Foundry](https://www.cloudfoundry.org/).

## Installation Pre-Requisites
* A functional BOSH environemnt
* Link to generated Lacework agent install.sh script for the Lacework dashboard

## Configuration
Clone the Lacework agent bosh release repository to your local disk.

```
$ git clone https://github.com/bvboe/lacework-agent-bosh-release
Cloning into 'lacework-agent-bosh-release'...
remote: Enumerating objects: 34, done.
remote: Counting objects: 100% (34/34), done.
remote: Compressing objects: 100% (19/19), done.
remote: Total 34 (delta 2), reused 31 (delta 2), pack-reused 0
Unpacking objects: 100% (34/34), 3.59 KiB | 409.00 KiB/s, done.
```
Go to the release directory
```
$ cd lacework-agent-bosh-release/
```
Create the BOSH release
```
$ bosh create-release --version=0.1
Adding job 'datacollector/4d4060a872d0e30625c20f06e2b9cc48457878abf9889d515e499c5860b942a5'...
Added job 'datacollector/4d4060a872d0e30625c20f06e2b9cc48457878abf9889d515e499c5860b942a5'

Added dev release 'lacework/0.1'

Name         lacework
Version      0.1
Commit Hash  1e37f6d

Job                                                                             Digest                                                                   Packages
datacollector/4d4060a872d0e30625c20f06e2b9cc48457878abf9889d515e499c5860b942a5  sha256:72519c4a69d31a3a2c2681e82f13124292c06e2f25c7c4dad7fd7ef68e2682f8  -

1 jobs

Package  Digest  Dependencies

0 packages

Succeeded
```
Upload the release into BOSH
```
$ bosh upload-release
Using environment 'xxx' as client 'admin'

[-----------------------------------------------------------------] 100.00%   0s
Task 16

Task 16 | 21:10:34 | Extracting release: Extracting release (00:00:00)
Task 16 | 21:10:34 | Verifying manifest: Verifying manifest (00:00:00)
Task 16 | 21:10:34 | Resolving package dependencies: Resolving package dependencies (00:00:00)
Task 16 | 21:10:34 | Processing 1 existing job: Processing 1 existing job (00:00:00)
Task 16 | 21:10:34 | Release has been created: lacework/0.1 (00:00:00)

Task 16 Started  Tue Jul  5 21:10:34 UTC 2022
Task 16 Finished Tue Jul  5 21:10:34 UTC 2022
Task 16 Duration 00:00:00
Task 16 done

Succeeded
```
Validate that the new release has been uploaded
```
$ bosh releases
Using environment 'xxx' as client 'admin'

Name       Version  Commit Hash
lacework   0.1      1e37f6d

(*) Currently deployed
(+) Uncommitted changes

6 releases

Succeeded
```
Use `bosh runtime-config` to validate if you have an existing runtime configuration or create a new one that looks as follows:
```
releases:
- name: lacework
  version: 0.1

addons:
- name: lacework-agent-install
  jobs:
  - name: datacollector
    release: lacework
    properties:
      lacework_install_script_url: <Put Lacework agent install script URL here>
```
Upload new release configuration
```
$ bosh update-runtime-config runtime-config.yml
Using environment 'xxx' as client 'admin'

+ releases:
+ - name: lacework
+   version: 0.1

+ addons:
+ - jobs:
+   - name: datacollector
+     properties:
+       lacework_install_script_url: "<redacted>"
+     release: lacework
+   name: lacework-agent-install

Continue? [yN]: y

Succeeded
```

Congratulations! You're now ready to update your existing deployments or deploy new applications with the Lacework agent built in.
