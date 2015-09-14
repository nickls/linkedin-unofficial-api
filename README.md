# linkedin-unofficial-api
Documentation and libraries for the unofficial LinkedIn API.

As of May 19th, 2015, [LinkedIn](https://www.linkedin.com/), gutted their Developer Program. Leaving only a small number of rather uninteresting API Calls. Details here [Developer Program Transition](https://developer.linkedin.com/support/developer-program-transition)

This project documents the API used by the [LinkedIn Mobile App](https://itunes.apple.com/us/app/linkedin/id288429040). It offers access to most of the original API and a variety of endpoints that were never available.

## API Reference

**Domains**

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

https://www.linkedin.com/uas/authenticate

```sh
$ curl -H 'X-Li-User-Agent: LIAuthLibrary:3.2.4 com.linkedin.LinkedIn:8.8.1 iPhone:8.3' -A 'LinkedIn/8.8.1 CFNetwork/711.3.18 Darwin/14.0.0' https://www.linkedin.com/uas/authenticate -c cookie.jr
{"status":"success"}
```

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