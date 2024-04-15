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

- THIS WORKS
  sudo chmod -R 777 $(pwd)
kostastseronis@mini-pc:~/CODE$ docker run --name zap -v $(pwd):/zap/wrk/:rw -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py -t https://www.us.elsevierhealth.com/ -r report.html
Using the Automation Framework
Total of 253 URLs
PASS: Vulnerable JS Library (Powered by Retire.js) [10003]
PASS: In Page Banner Information Leak [10009]
PASS: Re-examine Cache-control Directives [10015]
PASS: Content-Type Header Missing [10019]
PASS: Anti-clickjacking Header [10020]
PASS: X-Content-Type-Options Header Missing [10021]
PASS: Information Disclosure - Debug Error Messages [10023]
PASS: Information Disclosure - Sensitive Information in URL [10024]
PASS: Information Disclosure - Sensitive Information in HTTP Referrer Header [10025]
PASS: HTTP Parameter Override [10026]
PASS: Open Redirect [10028]
PASS: Cookie Poisoning [10029]
PASS: User Controllable Charset [10030]
PASS: User Controllable HTML Element Attribute (Potential XSS) [10031]
PASS: Viewstate [10032]
PASS: Directory Browsing [10033]
PASS: Heartbleed OpenSSL Vulnerability (Indicative) [10034]
PASS: HTTP Server Response Header [10036]
PASS: Server Leaks Information via "X-Powered-By" HTTP Response Header Field(s) [10037]
PASS: X-Backend-Server Header Information Leak [10039]
PASS: Secure Pages Include Mixed Content [10040]
PASS: HTTP to HTTPS Insecure Transition in Form Post [10041]
PASS: HTTPS to HTTP Insecure Transition in Form Post [10042]
PASS: User Controllable JavaScript Event (XSS) [10043]
PASS: Big Redirect Detected (Potential Sensitive Information Leak) [10044]
PASS: Retrieved from Cache [10050]
PASS: X-ChromeLogger-Data (XCOLD) Header Information Leak [10052]
PASS: CSP [10055]
PASS: X-Debug-Token Information Leak [10056]
PASS: Username Hash Found [10057]
PASS: X-AspNet-Version Response Header [10061]
PASS: PII Disclosure [10062]
PASS: Hash Disclosure [10097]
PASS: Weak Authentication Method [10105]
PASS: Reverse Tabnabbing [10108]
PASS: Dangerous JS Functions [10110]
PASS: Authentication Request Identified [10111]
PASS: Verification Request Identified [10113]
PASS: Private IP Disclosure [2]
PASS: Session ID in URL Rewrite [3]
PASS: Stats Passive Scan Rule [50003]
PASS: Insecure JSF ViewState [90001]
PASS: Java Serialization Object [90002]
PASS: Insufficient Site Isolation Against Spectre Vulnerability [90004]
PASS: Charset Mismatch [90011]
PASS: Application Error Disclosure [90022]
PASS: WSDL File Detection [90030]
WARN-NEW: Cookie No HttpOnly Flag [10010] x 1
https://www.us.elsevierhealth.com/idplus-customer/login/index/uenc/aHR0cHM6Ly93d3cudXMuZWxzZXZpZXJoZWFsdGguY29tLw%2C%2C/ (200 OK)
WARN-NEW: Cookie Without Secure Flag [10011] x 1
https://www.us.elsevierhealth.com/idplus-customer/login/index/uenc/aHR0cHM6Ly93d3cudXMuZWxzZXZpZXJoZWFsdGguY29tLw%2C%2C/ (200 OK)
WARN-NEW: Cross-Domain JavaScript Source File Inclusion [10017] x 12
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/free-shipping (200 OK)
WARN-NEW: Information Disclosure - Suspicious Comments [10027] x 12
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/free-shipping (200 OK)
WARN-NEW: Strict-Transport-Security Header Not Set [10035] x 11
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/checkout/cart/ (200 OK)
https://www.us.elsevierhealth.com/free-shipping (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/ (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/index/uenc/aHR0cHM6Ly93d3cudXMuZWxzZXZpZXJoZWFsdGguY29tLw%2C%2C/ (200 OK)
WARN-NEW: Content Security Policy (CSP) Header Not Set [10038] x 10
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/free-shipping (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/ (200 OK)
https://www.us.elsevierhealth.com/medicine/allergy (200 OK)
https://www.us.elsevierhealth.com/medicine/anatomy (200 OK)
WARN-NEW: Non-Storable Content [10049] x 11
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/catalogsearch/advanced/ (302 Found)
https://www.us.elsevierhealth.com/checkout/cart/ (200 OK)
https://www.us.elsevierhealth.com/free-shipping (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/ (200 OK)
WARN-NEW: Cookie with SameSite Attribute None [10054] x 3
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/robots.txt (200 OK)
https://www.us.elsevierhealth.com/sitemap.xml (301 Moved Permanently)
WARN-NEW: Permissions Policy Header Not Set [10063] x 11
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/checkout/cart/ (200 OK)
https://www.us.elsevierhealth.com/free-shipping (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/ (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/index/uenc/aHR0cHM6Ly93d3cudXMuZWxzZXZpZXJoZWFsdGguY29tLw%2C%2C/ (200 OK)
WARN-NEW: Timestamp Disclosure - Unix [10096] x 12
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/checkout/cart/ (200 OK)
WARN-NEW: Cross-Domain Misconfiguration [10098] x 11
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/checkout/cart/ (200 OK)
https://www.us.elsevierhealth.com/free-shipping (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/ (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/index/uenc/aHR0cHM6Ly93d3cudXMuZWxzZXZpZXJoZWFsdGguY29tLw%2C%2C/ (200 OK)
WARN-NEW: Source Code Disclosure - Java [10099] x 13
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/checkout/cart/ (200 OK)
https://www.us.elsevierhealth.com/free-shipping (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/ (200 OK)
WARN-NEW: Modern Web Application [10109] x 11
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/checkout/cart/ (200 OK)
https://www.us.elsevierhealth.com/free-shipping (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/ (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/index/uenc/aHR0cHM6Ly93d3cudXMuZWxzZXZpZXJoZWFsdGguY29tLw%2C%2C/ (200 OK)
WARN-NEW: Session Management Response Identified [10112] x 17
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/checkout/cart/ (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/ (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/index/uenc/aHR0cHM6Ly93d3cudXMuZWxzZXZpZXJoZWFsdGguY29tLw%2C%2C/ (200 OK)
WARN-NEW: Absence of Anti-CSRF Tokens [10202] x 16
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/medicine/anatomy (200 OK)
https://www.us.elsevierhealth.com/medicine/anatomy (200 OK)
https://www.us.elsevierhealth.com/medicine/anatomy (200 OK)
WARN-NEW: Sub Resource Integrity Attribute Missing [90003] x 12
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/checkout/cart/ (200 OK)
https://www.us.elsevierhealth.com/free-shipping (200 OK)
https://www.us.elsevierhealth.com/idplus-customer/login/ (200 OK)
WARN-NEW: Loosely Scoped Cookie [90033] x 4
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/ (200 OK)
https://www.us.elsevierhealth.com/robots.txt (200 OK)
https://www.us.elsevierhealth.com/sitemap.xml (301 Moved Permanently)
FAIL-NEW: 0     FAIL-INPROG: 0  WARN-NEW: 17    WARN-INPROG: 0  INFO: 0 IGNORE: 0       PASS: 47
