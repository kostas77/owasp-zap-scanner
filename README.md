- https://mydeveloperplanet.com/2021/05/11/automate-zap-with-docker/
- https://www.zaproxy.org/docs/automate/automation-framework/

docker network create zapnet

docker run --name goatandwolf -p 8080:8080 -p 9090:9090 -d --net zapnet webgoat/goatandwolf

docker inspect goatandwolf | grep IPAddress
"SecondaryIPAddresses": null,
"IPAddress": "",
"IPAddress": "172.22.0.2",

docker run --net zapnet -v $(pwd):/zap/wrk/:rw \
-t owasp/zap2docker-stable zap-full-scan.py -I -j -m 10 -T 60 \
-t http://172.22.0.2:8080/WebGoat \
-r 20210417-zap-full-scan-without-user.html
