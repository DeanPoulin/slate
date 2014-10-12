# Authentication

In order to use any of the HMS APIs, you must have security credentials consisting of a Key and Secret. This requires a contract with HMS. If you are with a company that currently has a contract with HMS, please contact your HMS sales representative for information on the options for API usage and to get the required security credentials; otherwise, for further information please contact HMS via e-mail:

E-mail Address: [apisupport@healthmarketscience.com](mailto:apisupport@healthmarketscience.com?subject=API Contract Information)

Subject Line: API Contract Information

Include any other contact information you want HMS to use in reaching you. An HMS representative will contact you within one business day with information regarding contract options and supplying security credentials so that you can make use of the HMS APIs.

#API Structure

HMS API services use the following RESTful web service call elements:

* `Method` = HTTP Method. The appropriate methods **GET**, **PUT**, etc. are identified in the section documenting each of the services provided.
* `URL` = URL for each service.
* `Body` = Request body. Not always present.
* `Response` = Possible responses will be documented with each service.

# URL Structure
URL elements for HMS APIs are generally structured as follows: `Root`/`Version`/`Request``Security`

Parameter | Description
--------- | -----------
Root | **https://api.hmsonline.com**
Version | **_Version Number_** (Initial Version Number=v1)
Request | **_Request being made to API_** (See specific commands for details)
Security | **_Security string to validate request_** (See **Security Credentials** for details)
