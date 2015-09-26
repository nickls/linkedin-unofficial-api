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
- **[<code>GET</code> /li/v1/people/person](#current-user-profile)**
- **[<code>GET</code> /li/v1/pages/you](#current-user-page)**
- **[<code>GET</code> /li/v2/profile/:id](#user-page)**
- **[<code>GET</code> /li/v1/people/:id/connections](#connections)**
- **[<code>GET</code> /li/v2/profile/:id/detail/background](#background)**
- **[<code>GET</code> /li/v2/people/:id/endorsements](#endorsements)**
- **[<code>GET</code> /li/v1/pages/mailbox](#messages)**


**TODO Endpoints**
- **[<code>GET</code> /li/v2/profile/:id/detail/recommendations](#recommendations)**
- **[<code>GET</code> /li/v2/notifications](#notifications)**
- **[<code>GET</code> /li/v2/wvmp](#visitors)**


**Headers**

| Name            | Value             |
| ----            | ----------------- |
| X-Li-User-Agent | 'LIAuthLibrary:3.2.4 com.linkedin.LinkedIn:8.8.1 iPhone:8.3'|
| User-Agent      | 'LinkedIn/8.8.1 CFNetwork/711.3.18 Darwin/14.0.0'|
| X-User-Language | en |
| X-User-Locale   | en_US |
| Accept-Language | en-us |

The top two headers are nessesary for user authentication, the other three make sure the results come back in English not Norwegian.


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
$ curl  https://www.linkedin.com/uas/authenticate  -d 'session_key=username%40gmail.com&session_password=sekrett&JSESSIONID=%22ajax%3A4847487595299993333%22' -H 'X-Li-User-Agent: LIAuthLibrary:3.2.4 com.linkedin.LinkedIn:8.8.1 iPhone:8.3' -A 'LinkedIn/8.8.1 CFNetwork/711.3.18 Darwin/14.0.0' -b cookie.jr -c cookie.jr
```

**Response**
```json
{"login_result":"PASS","challenge_url":""}
```

login_result:
- PASS -- success, you can now call the API.
- BAD_PASSWORD -- failure, self explanatory. 
- CHALLENGE -- the challenge_url will then be set, call it. solve the recaptcha, party on!


#### Current User Profile

<code>GET /li/v1/people/person </code>

Get summary data from the logged in user's profile.

**Request**
```sh
$ curl https://touch.www.linkedin.com/li/v1/people/person  -b cookie.jr -c cookie.jr
```

**Response**
```json
  {
    "response": {
      "authToken": "name:xxyz",
      "distance": 0,
      "email": "username@gmail.com",
      "firstName": "Nick",
      "formattedName": "Nick Smith",
      "hasPicture": true,
      "headline": "Code Monkey",
      "id": "293949",
      "lastName": "Smith",
      "picture": "https://media.licdn.com/mpr/mpr/shrinknp_100_100/p/1/000/202/111/123937.jpg"
    }
  }
```
-  authToken -- current not sure the use of this token.

#### Current User Page

<code>GET /li/v1/pages/you </code>

Get linkedin page from the logged in user's profile.

**Request**
```sh
$ curl https://touch.www.linkedin.com/li/v1/pages/you  -b cookie.jr -c cookie.jr
```

**Response**
```json
{
  "profile": {
    "person": {
      "lastName": "Lane-Smith",
      "originalPicture": "https://media.licdn.com/mpr/mpr/p/3/000/267/3dd/3c37294.jpg",
      "tType": "pt1",
      "distance": 0,
      "formattedName": "Nick Lane-Smith",
      "authToken": "name:xzzf",
      "industry": "Computer Networking",
      "picture": "https://media.licdn.com/mpr/mpr/shrinknp_100_100/p/3/000/267/3dd/3c37294.jpg",
      "phoneNumbers": [],
      "hasPicture": true,
      "firstName": "Nick",
      "location": "San Francisco Bay Area",
      "id": "7881116",
      "headline": "Founder at BloomAPI"
    },
    "detail": [...]
  }
}
```

This JSON blob is huge and not very well structured. It is tied very strongly to the UI of the linkedin app, I wonder why they made this design choice. It could be that the mobile app will adjust the UI to match how the data is layed out. It might also be tightly coupled to the code of the app -- which would suck.

- person -- profile details for the logged in user.
- detail -- has some useful data similar to the background page.


#### User Page

<code>GET /li/v2/profile/:id</code> 

Get linkedin page/profile for the user's id.

**Request**
```sh
$ curl https://touch.www.linkedin.com/li/v2/profile/7881116  -b cookie.jr -c cookie.jr
```

**Response**
```json
{
  "personTopCard": {
    "summary2": {
      "text3": "March 2011 - Present (4 years 7 months)",
      "tType": "sum4",
      "text1": "BloomAPI",
      "pictureUrl": "https://media.licdn.com/media/p/2/000/1a7/291/d8d8d8s.png",
      "text2": "Founder",
      "pictureLogo": "company_tc",
      "link": {
        "targetContextType": "experience1",
        "partialData": {
          "title": "Nick Lane-Smith"
        },
        "resourcePath": "/li/v2/profile/7881116/detail/background",
        "id": "7881116",
      }
    },
    "tType": "personTopCard",
    "summary1": {
      "tType": "sum1",
      "values": [
        {
          "tType": "sum2",
          "text1": "139",
          "text2": "connections",
          ...
        },
      ]
    },
    "text3": "San Francisco, California | Computer Networking",
    "text1": "Nick Lane-Smith",
    "text2": "Founder at BloomAPI",
    "isSelf": true
  }
}
```

This JSON blob is also huge and not very well structured. How rude!

- personTopCard -- this section has some useful summary data about the profile: name, id, picture, connections, etc.


### Connections

<code>GET /li/v1/people/:id/connections </code>

Get all of the connections for a specific user id.

**Parameters**
- count -- maximum number of connections to return

**Request**
```sh
$ curl "https://touch.www.linkedin.com/li/v1/people/7881116/connections?count=5000"  -b cookie.jr -c cookie.jr
```

**Response**
```json
  {
    "total": 1055,
    "count": 1055,
    "values": [{
      "authToken": "name:xxyz",
      "distance": 2,
      "firstName": "Mike",
      "formattedName": "Mike Smith",
      "headline": "Director of Product",
      "id": "10420023",
      "lastName": "Smith",
      "hasPicture": true,
      "picture": "https://media.licdn.com/mpr/mpr/shrinknp_200_200/AAEEAQAAAkmAAAAJDcdZzAFJRIDAKAAA84MFKFKKME.jpg",
      "tType": "pt3"
    }, 
    ...]
  }
```

### Background

<code>GET /li/v2/profile/:id/detail/background</code>

Get a user's background (eduction, work experience, patents, projects, etc.)


**Request**
```sh
$ curl https://touch.www.linkedin.com/li/v2/profile/39654029/detail/background  -b cookie.jr -c cookie.jr
```

**Response**
```json
{
  "values": [
    {
      "tType": "sect4",
      "values": [
        {
          "tType": "prt2",
          "text1": "B.A., English and History",
          "pictureUrl": "https://media.licdn.com/mpr/mpr/shrinknp_100_100/p/1/005/011/0ec/4deadbeef.png",
          "pictureLogo": "education_grey",
          "header": "Lewis and Clark College"
        }
      ],
      "contextType": "education1"
    },
  ...]
}    
```
-  contextType -- can filter response by contextType to select desired background details: experience1, education1, education2, etc.



### Endorsements

<code>GET /li/v2/people/:id/endorsements</code>

One of the worst LinkedIn features. Get the endorsements made for user with id.

**Request**
```sh
$ curl https://touch.www.linkedin.com/li/v2/people/39654029/endorsements  -b cookie.jr -c cookie.jr
```

**Response**
```json
{
  "values": [
    {
      "resourcePath": "/li/v2/people/endorsements?skill=Internal%20Communications",
      "values": [
        {
          "tType": "rt20",
          "text1": "Internal Communications",
          "count": 2,
          "id": "7881116",
          "type": "skill"
        },
        {
          "tType": "rt24",
          "representsCurrentUser": false,
          "text1": "Joshua Reynolds",
          "text2": "Head of Marketing at Quantifind, Strategic Advisor to Blanc & Otus and H+K, Holmes Report Top 25 Innovator",
          "pictureUrl": "https://media.licdn.com/mpr/mpr/shrinknp_200_200/p/5/005/08b/098/34424324.jpg",
          "link": {
            "resourcePath": "/li/v1/people/24631444/profile",
            "id": "24631444",
            "type": "person",
          }
        },
        ...
      ]
    },
```
-  values[0] -- this is going to be the skill name and count of endorsers.
-  values -- the next N array values are profile summaries (icon, name, blurb) for each of the endorsers.


### Messages

<code>GET /li/v1/pages/mailbox</code>

Messages and invitations for the current logged in user.

**Request**
```sh
$ curl https://touch.www.linkedin.com/li/v1/pages/mailbox -b cookie.jr -c cookie.jr
```

**Response**
```json
{
  "invitations": {
    "total": 1,
    "values": [{
      "isCustomMessage": false,
      "read": false,
      "folder": "inbox",
      "tType": "invitation",
      "subject": "Invitation to connect on LinkedIn",
      "from": {
        "hasPicture": true,
        "firstName": "Stephan",
        "lastName": "Jenkins",
        "authToken": "name:xxzy",
        "formattedName": "Stephan Jenkins",
        "id": "38373",
        "headline": "Singer at Third Eye Blind",
        "picture": "https://media.licdn.com/mpr/mpr/shrinknp_200_200/XXaSADADAJAKDJHHJJSJSJSJSJSJJSSJJS.jpg"
      },
      "id": "I60118598766633_500",
      "body": "Nick,\n\nI'd like to add you to my professional network on LinkedIn.\n\n- Stephan",
      "seen": true,
      "timestamp": 1433339090205
    }],
    "start": 0,
    "count": 20,
    "droppedCount": 0,
    "unseen": 0
  },
  "messages": {
    "total": 44,
    "unread": 0,
    "values": [{
      "read": true,
      "folder": "inbox",
      "tType": "message",
      "subject": "great to meet you last week",
      "from": {
        "hasPicture": true,
        "firstName": "Mike",
        "lastName": "Smith",
        "authToken": "name:aLS9",
        "formattedName": "Mike Smith",
        "id": "58708",
        "headline": "Co-Founder, Erros ",
        "picture": "https://media.licdn.com/mpr/mpr/shrinknp_200_200/XXaSADADAJAKDJHHJJSJSJSJSJSJJSSJJS.jpg"
      },
      "isMsgTypeInmail": false,
      "id": "I6043170444767420416_500",
      "to": [{
        "hasPicture": true,
        "firstName": "Nick",
        "lastName": "Smith",
        "authToken": "name:VCev",
        "formattedName": "Nick Smith",
        "id": "293949",
        "headline": "Code Monkey",
        "picture": "https://media.licdn.com/mpr/mpr/shrinknp_100_100/p/1/000/265/113/1b5f6fe.jpg"
      }],
      "body": "XXXZZZYYY",
      "seen": true,
      "timestamp": 1440804110685,
      "hasReplied": false
    }
    ...],
    "start": 0,
    "count": 20,
    "droppedCount": 0,
    "unseen": 0
  }
}
```


## Installation & Usage

XXX, link to JS and python APIs

## Disclamer

**Use at your own risk!** This API is unofficial and unsupported by LinkedIn.

## Credits

Thanks to the authors of:

- [Charles Proxy](http://www.charlesproxy.com/)
- [mitmproxy](https://mitmproxy.org/)

## License

[MIT License](https://github.com/nickls/linkedin-unofficial-api/blob/master/LICENSE)