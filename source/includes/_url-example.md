# URL Example

Parameter | Example
--------- | -----------
Root | **https://api.hmsonline.com**
Version | **v1**
Request | **search/masterfile?**
Security | **timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg== &signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=**


Yields the following URL: 

`https://api.hmsonline.com/v1/search/masterfile?timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`

The `Body` in this case will determine the actual query to perform.  To query all records, use:

`Body` = **{"match_all":{}}**

<aside class="notice">
**Note: All security values shown here and throughout this documentation are for illustration purposes only and will not work as shown.  See [[Security Credentials|Security Credentials]] for more information.**
</aside>