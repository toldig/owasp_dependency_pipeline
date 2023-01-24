### Owasp dependency checker pipeline with custom image

Used alpine parent image  
```
docker pull alpine:latest
```

Create **app** directory, enter it  
Download [owasp dependency checker tool](https://github.com/jeremylong/DependencyCheck/releases/download/v8.0.1/dependency-check-8.0.1-release.zip) and unzip it
Run alpine image and map the **app** directory within the container to /app  
```
docker run --privileged -it --rm -v "$(pwd):/app" alpine:latest
```

Install JAVA and npm  
```
apk add openjdk11
apk add npm
npm i -g npm-audit-html@beta
```

Move dependency checker into /opt  
```
mkdir /opt/dependency_checker/
cp -R /app /opt/dependency_checker/
```

Initiate a dummy scan to update the database  
```
/opt/dependency_checker/bin/dependency-check.sh --scan /tmp/
rm dependency-check-report.html
```

Create **/report** directory  

Do **NOT** close this terminal, open another one, and get the container ID  
```
docker ps
```

Commit the changes to a new image  
```
docker container commit 324d957b3f01 owaspcustom:latest
```

Or if you have only one container running  
```
docker container commit $(docker ps -q) owaspcustom:latest
```

Tag and push image to your repo  
```
docker image tag owaspcustom:latest harbor.xxxxxxxxx.xxx/xxxx/owaspcustom:latest
docker image push harbor.xxxxxxxxx.xxx/xxxx/owaspcustom:latest
```
