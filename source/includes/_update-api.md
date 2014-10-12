# Update API

## Update

```shell
curl https://api.hmsonline.com/v1/update/masterfile?time=2000-05-29T16:24:08.412Z&pageSize=20&page=1&type=full&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=
```

The **Update API** is used to get updates to practitioners previously returned to a Client through the **Search API** that have been updated since a given date / time value.  

### Method

* Method: **GET**

### URL

* URL Structure: `Root`/`Version`/`Request`&`Security`

Within that structure, this section documents how the `Request` portion is used for update requests.  The following points are relevant to structuring update requests:

* Results are paginated
* The default is 10 returns per page
* The maximum page size is 250 returns per page
* Index names are used to define search universes. Currently, use **masterfile** for all queries.
* A given client can only reference a single index
* Returns can be either full practitioner data or identifiers only
* The request requires a date / time value.  Returns will be of active records updated since the specified date / time value.
* The Client should keep track of the current date / time each time this service is invoked, using that value as the input date / time the next time the service is called.
* Only currently active practitioners will be returned by this service.  The **Inactivate API** should be used to identify practitioners inactivated by HMS since a given date / time.

### Request
The Request portion of an update request consists of the command, three required parameters, and two optional parameters: `Command`/`Index`?`Time`&`Type`&`Page Size`&`Page Number`

* `Command` = **update**

Required:

* `Index` - Names the index used for updates.  Currently, use **masterfile** for all queries.
* `Time` -  An ISO formatted date string indicating the date / time after which updates will result in a return (e.g., **time=2000-05-29T16:24:08.412Z**).
* `Type` - Indicates the type of return values.  Either **full** or **ids**.
`type=full` indicates that full practitioner data for changed records should be returned.
`type=ids` indicates that only ids for changed records should be returned.

Optional:

`Page Size` - Defines the number of returns per page. Default = 10. Max. = 250.

* Format: `pageSize=n`
* Example: `pageSize=20`

`Page Number` - Specifies the page to return. If not specified, returns the first page of results. 

* Format: `page=n`
* Example: `page=3` - Returns the 3rd page of results.

Any parameters after the first must be separated by an ampersand (&).  An ampersand (&) must precede the `Security` portion of the URL.

### Update API URL Example

The following URL would return the first page of full records updated since the date / time given, with a page size of 20 records per page:

`https://api.hmsonline.com/v1/update/masterfile?time=2000-05-29T16:24:08.412Z&pageSize=20&page=1&type=full&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`

<aside class="notice">
**Note: All security values shown here and throughout this documentation are for illustration purposes only and will not work as shown.  See [Security Credentials](#security-credentials) for more information.**
</aside>

### Body

* None for this API