# linkedin-unofficial-api
Documentation and libraries for the unofficial LinkedIn API.

As of May 19th, 2015, [LinkedIn](https://www.linkedin.com/), gutted their Developer Program. Leaving only a small number of rather uninteresting API Calls. Details here [Developer Program Transition](https://developer.linkedin.com/support/developer-program-transition)

This project documents the API used by the [LinkedIn Mobile App](https://itunes.apple.com/us/app/linkedin/id288429040). It offers access to most of the original API and a variety of endpoints that were never available.

## API Reference

**Domains**
- `www.linkedin.com` -- Authentication Only
- `touch.www.linkedin.com` -- Everything else


**Endpoints**
- **[<code>GET</code> /authenticate](#user-auth)**
- **[<code>POST</code> /authenticate](#user-auth)**
- **[<code>GET</code> /authenticate](#user-auth)**

**Headers**

| Name            | Value             |
| ----            | ----------------- |
| X-Li-User-Agent | 'LIAuthLibrary:3.2.4 com.linkedin.LinkedIn:8.8.1 iPhone:8.3'|
| User-Agent      | 'LinkedIn/8.8.1 CFNetwork/711.3.18 Darwin/14.0.0'|

There are many other headers but these are nessesary for user authentication.


#### User Auth

Part One: get some cookies

<code>GET /uas/authenticate </code>

**Request**
```sh
$ curl https://www.linkedin.com/uas/authenticate -c cookie.jr -H 'X-Li-User-Agent: LIAuthLibrary:3.2.4 com.linkedin.LinkedIn:8.8.1 iPhone:8.3' -A 'LinkedIn/8.8.1 CFNetwork/711.3.18 Darwin/14.0.0'
```

**Response**
```json
{"status":"success"}
```

Part Two: pass in username, password, and a previously obtained session cookie.

<code>POST /uas/authenticate </code>

**Parameters**
- session_key -- LinkedIn Username
- session_password -- LinkedIn Password
- JSESSIONID -- session token, found in cookies from first call.

Currently not sure why require you to pass in a cookie as a query paramerter. seems unnecessary.

**Request**
```sh
$ curl  https://www.linkedin.com/uas/authenticate  -d 'session_key=username%40gmail.com&session_password=SOMESECRET&JSESSIONID=%22ajax%3A4847487595299993333%22' -H 'X-Li-User-Agent: LIAuthLibrary:3.2.4 com.linkedin.LinkedIn:8.8.1 iPhone:8.3' -A 'LinkedIn/8.8.1 CFNetwork/711.3.18 Darwin/14.0.0' -b cookie.jr -c cookie.jr
```

**Response**
```json
{"login_result":"PASS","challenge_url":""}
```

login_result:
- PASS -- success, you can now call the API.
- CHALLENGE -- the challenge_url will then be set, untested.
- BAD_PASSWORD -- self explanatory. 


## Installation & Usage

XXX, TODO

## Disclamer

**Use at your own risk!** This API is unofficial and unsupported by LinkedIn.

## Credits

Thanks to the authors of:

- [Charles Proxy](http://www.charlesproxy.com/)
- [mitmproxy](https://mitmproxy.org/)

## License

[MIT License](https://github.com/nickls/linkedin-unofficial-api/blob/master/LICENSE)