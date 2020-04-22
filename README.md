# Zap Running as a Docker Proxy
Based on this: https://nullsweep.com/dynamic-security-scanning-in-a-ci-zap-scanning-with-jenkins/

## Fixing the docke compose file
The first issue was the docker compose file, speficialluy with webgoat container. The post says it came from the offical owasp release however the version was changed from 2.1 to 3. I've seen issues with docker-compose 2.1 to 3 specifically with the health checks when doing the dpkg-scan-engine in docker form. Once that was fixed the webgoat image was able to come up.

## Fixing the Zap proxy image
When moving on to the steps to actually execute the zap proxy commands on the container, it failed due to a python3 locale error. 

```RuntimeError: Click will abort further execution because Python 3 was configured to use ASCII as encoding for the environment. ```

To fix this, We needed to install the locale package and set the locale in the environment so it would work. This is done in the docker file. So we had to rebuild the zap image from this docker file to get that package included. It is unclear why this is an issue in the dockerhub container since it should be expected to work.

Once the new image is built: (This was modified in the docker-compose.yml aswell to build the image now.
```docker build --tag=z2 .```

We still had the error, but as documented [here](https://stackoverflow.com/questions/36651680/click-will-abort-further-execution-because-python-3-was-configured-to-use-ascii), we need to export the proper environment vars indicating the locale and after that it worked.

```
export LC_ALL=en_US.utf-8
export LANG=en_US.utf-8
```
