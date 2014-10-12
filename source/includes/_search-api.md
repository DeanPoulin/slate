# Search API
The Search API is the main tool in the HMS API suite. It is used to perform searches of the HMS practitioner information within the limits of a client contract.
## Search

### HTTP Request
`POST https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2`

### URL

* URL Structure: `Root`/`Version`/`Request`&`Security`

Within that structure, this section documents how the `Request` portion is used for search requests as well as the `Body` of the request.  The following points are relevant to structuring search requests:

* Results are paginated
* The default is 10 returns per page
* The maximum page size is 250 returns per page
* Index names are used to define search universes.  Currently, use **masterfile** for all queries.
* A given client can only reference a single index
* Searching is performed using Elastic Search and use that syntax.  Information on Elastic Search queries can be found here: [http://www.elasticsearch.org/guide/reference/query-dsl/](http://www.elasticsearch.org/guide/reference/query-dsl/)

### Request
The `Request` portion of a search request consists of the search command, the `Index`, and two optional parameters and has this format: `Command`/`Index`?`Page Size`&`Page Number`

* `Command` = **search**

Required:

* `Index` - Names the index used in the search.  Currently, use **masterfile** for all queries.

Optional:
If both optional parameters are given, they must be separated by an ampersand (&).

* `Page Size` - Defines the number of returns per page.  Default = 10.  Max. = 250.
Format: `pageSize=n`, Example: `pageSize=20`

* `Page Number` - Defines the page to return.  If not specified, returns the first page of results. 
Format: `page=n`, Example: `page=3`  Returns the 3rd page of results.

If either optional parameter is given, an ampersand must precede the `Security`portion of the URL.


### Search API URL Example

The full URL for the **Search API** is built as: `Root`/`Version`/`Request`&`Security`

Using the following values:

Component | Example
--------- | -----------
`Root`| **https://api.hmsonline.com**
`Version` | **v1**
`Request` | **search/masterfile&pageSize=20&page=2**
`Security` | **&timestamp=1369844777731<br />&key=EU2BD6eHBQeUMpMxDW9dmg==<br />&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=**

Yields the following URL:

`https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`

<aside class="notice">
Note: 
All security values shown here and throughout this documentation are for illustration purposes only and will not work as shown.  See See [Security Credentials](#security-credentials) for more information.
</aside>

### Body

The search to be performed is defined in the `Body` of the API call.  The `Body` consists of a JSON string defining the search.

<aside class="notice">
**Important Note:** 
The JSON `Body` of the **Search API** is case sensitive and must be entered in lower case.
</aside>

## Example: Query All
Query to return all records:

```shell
curl -X POST -H "Accept:application/json" -H "Content-Type:application/json" -d '{"match_all":{}}' https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2×tamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=
```

```java
import java.io.UnsupportedEncodingException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.HashMap;
import java.util.Map;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;

import org.apache.commons.codec.binary.Base64;
import org.junit.Test;

import com.sun.jersey.api.client.Client;
import com.sun.jersey.api.client.WebResource;

private static final String DEFAULT_ENCODING = "UTF-8";
private static final String BASE_PATH = "https://api.hmsonline.com";

@Test
public void testPost() throws InvalidKeyException, NoSuchAlgorithmException, UnsupportedEncodingException {
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("timestamp", "" + System.currentTimeMillis());
    params.put("key", "myKey");
    System.out.println(post("/v1/search/masterfile", "mysecret", params));
}

private String post(String path, String secret, Map<String, String> params) throws InvalidKeyException,
                NoSuchAlgorithmException, UnsupportedEncodingException {
    Client c = Client.create();
    String paramString = "?";
    boolean first = true;
    for (String paramKey : params.keySet()) {
        if (first) {
            paramString = paramString + paramKey + "=" + params.get(paramKey);
            first = false;
        } else {
            paramString = paramString + "&"  + paramKey + "=" + params.get(paramKey);
        }
    }
    String context = path + paramString;
    String sig = generateSignature(secret, context);
    WebResource r = c.resource(BASE_PATH + context + "&signature=" + sig);

    System.out.println("POST: " + BASE_PATH + context + "&signature=" + sig);

    String result = r.post(String.class, "{\"match_all\":{}}");
    return result;
}

public static String generateSignature(String secretKey, String contentTosign) throws NoSuchAlgorithmException,
                InvalidKeyException, UnsupportedEncodingException {
    byte[] key = Base64.decodeBase64(secretKey);
    SecretKeySpec sha1Key = new SecretKeySpec(key, "HmacSHA1");
    Mac mac = Mac.getInstance("HmacSHA1");
    mac.init(sha1Key);
    byte[] bytes = mac.doFinal(contentTosign.getBytes(DEFAULT_ENCODING));
    return Base64.encodeBase64String(bytes);
}
```

```php
#!/usr/bin/php -q
<?php
$key = '<CHANGE ME>';
$secret = '<CHANGE ME>';
$timestamp = time() * 1000;
$context_path = '/v1/search/masterfile?timestamp=' . $timestamp . '&key=' . $key;
$url = 'https://api.hmsonline.com/' . $context_path;

// generate signature
$secret_bytes = base64_decode($secret);
$sig_hash = hash_hmac('sha1', $context_path, $secret_bytes, true);
$signature = base64_encode($sig_hash);

// add signature to URL
$url .= '&signature=' . $signature;

echo 'key = [' . $key . "]\n";
echo 'secret = [' . $secret . "]\n";
echo 'context_path = [' . $context_path . "]\n";
echo 'timestamp = [' . $timestamp . "]\n";
echo 'signature = [' . $signature . "]\n";
echo htmlspecialchars($url) . "\n";
?>
```

```ruby
require 'rest_client'
require "base64"

$KEY="YOUR KEY GOES HERE"
$BASE64_SECRET="YOUR SECRET GOES HERE"
$TIMESTAMP=Time.now.to_i*1000
$QUERY = "{\"match_all\":{}}"

def hex_to_base64_digest(hexdigest)
    [[hexdigest].pack("H*")].pack("m").strip
end

begin
    baseUrl = "https://api.hmsonline.com"
    body="#{$QUERY}"
    contextPath = "/v1/search/masterfile?timestamp=#{$TIMESTAMP}&key=#{$KEY}"

    secret = Base64.decode64($BASE64_SECRET)
    signature = OpenSSL::HMAC.hexdigest(OpenSSL::Digest::Digest.new('sha1'), secret, contextPath)
    base64Signature = hex_to_base64_digest(signature)

    signedUrl = "#{baseUrl}#{contextPath}&signature=#{base64Signature}"
    encodedUrl = URI::encode(signedUrl)

    puts("Base 64 Secret : [#{$BASE64_SECRET}]")
    puts("Message : [#{contextPath}]")
    puts("Base64 Signature : [#{base64Signature}]")
    response = RestClient.post encodedUrl, body, :content_type => :json, :accept => :json
    puts(response)
rescue => e
    puts(e.response)
end
```

`POST https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`

## Example: Query All By Identifier
Query all records for a specific HMS Identifier (PIID)

```shell
curl -X POST -H "Accept:application/json" -H "Content-Type:application/json" -d '@/path/to/filename' https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2×tamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=
```

> File Should Contain the Following JSON

```json
{
  "bool": {
    "must": [
      {
        "nested": {
          "path": "practitioner",
          "score_mode": "total",
          "query": {
            "bool": {
              "must": [
                {
                  "term": {
                    "practitioner.hms_piid": "pi0048kwj8"
                  } 
                }
              ]
            }
          }
        }
      }
    ]
  }
}
```

`POST https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`


## Example: Query by First Name (exact)
```shell
curl -X POST -H "Accept:application/json" -H "Content-Type:application/json" -d '@/path/to/filename' https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2×tamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=
```

> File Should Contain the Following JSON

```json
{
  "bool": {
    "must": [
      {
        "nested": {
          "path": "practitioner",
          "score_mode": "total",
          "query": {
            "bool": {
              "must": [
                {
                  "match": {
                    "practitioner.first_name": {
                      "query": "john",
                      "operator": "and"
                    }
                  }
                }
              ]
            }
          }
        }
      }
    ]
  }
}
```

Query for a specific first name:

`POST https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`



## Example: Query by First Name (fuzzy)
```shell
curl -X POST -H "Accept:application/json" -H "Content-Type:application/json" -d '@/path/to/filename' https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2×tamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=
```

> File Should Contain the Following JSON

```json
{
  "nested": {
    "path": "practitioner",
    "score_mode": "total",
    "query": {
      "bool": {
        "must": [
          {
            "fuzzy_like_this": {
              "fields": [
                "first_name"
              ],
              "like_text": "amera",
              "min_similarity": "0.5"
            }
          }
        ]
      }
    }
  }
}
```

Fuzzy search for first name (using edit-distance). 

`POST https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`



## Example: Query by Address (fuzzy)
```shell
curl -X POST -H "Accept:application/json" -H "Content-Type:application/json" -d '@/path/to/filename' https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2×tamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=
```

> File Should Contain the Following JSON

```json
{
  "nested": {
    "path": "practitioner.address",
    "score_mode": "total",
    "query": {
      "bool": {
        "must": [
          {
            "fuzzy": {
              "practitioner.address.address_line1": { 
                "min_similarity": 0.5,
                "boost": 1,
                "value": "wstrn",
                "prefix_length": 0
                }
              }
            }
          ]
        }
      }
    }
}
```

Fuzzy search for address (Using edit-distance).

`POST https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`

## Example: Complex Fuzzy Query
```shell
curl -X POST -H "Accept:application/json" -H "Content-Type:application/json" -d '@/path/to/filename' https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2×tamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=
```

> File Should Contain the Following JSON:

```json
{
  "bool": {
    "should": [
      {
        "bool": {
          "must": [
            {
              "nested": {
                "path": "practitioner.npi",
                "score_mode": "total",
                "query": {
                  "term": {
                      "npi": " "
                  }
                }
              }
            }
          ]
        }
      },
      {
        "bool": {
          "must": [
            {
              "nested": {
                "path": "practitioner",
                "score_mode": "total",
                "query": {
                  "bool": {
                    "must": [
                      {
                        "match": {
                          "last_name": "PORTER"
                        }
                      },
                      {
                        "fuzzy_like_this": {
                          "fields": [
                            "first_name"
                          ],
                          "like_text": "DENNI",
                          "min_similarity": ".5"
                        }
                      }
                    ]
                  }
                }
              }
            },
            {
              "nested": {
                "path": "practitioner.address",
                "score_mode": "total",
                "query": {
                  "match": {
                    "address_zip5": "26201"
                  }
                }
              }
            }
          ]
        }
      }
    ]
  }
}
```
Query with OR condition--Identifier (exact) OR (Fuzzy Name and ZIP Code)

* Combined search for: (identifier) OR (name and address). Also illustrates use of the preferred fuzzy_like_this, which is recommended over fuzzy.

`POST https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`

## Example: Query by Name (wildcard) AND Address
```shell
curl -X POST -H "Accept:application/json" -H "Content-Type:application/json" -d '@/path/to/filename' https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2×tamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=
```

> File Should Contain the Following JSON:

```json
{
  "bool": {
    "must": [
      {
        "nested": {
          "path": "practitioner",
          "score_mode": "total",
          "query": {
            "bool": {
              "must": [
                {
                  "wildcard": {
                    "practitioner.first_name": "robe*"
                  }
                }
              ]
            }
          }
        }
      },
      {
        "nested": {
          "path": "practitioner.address",
          "score_mode": "total",
          "query": {
            "bool": {
              "must": [
                {
                  "match": {
                    "practitioner.address.city_name": "casper"
                  }
                }
              ]
            }
          }
        }
      }
    ]
  }
}
```

* Search for two parameters (name AND address):

`POST https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`

## Example: Query by Location (distance)
```shell
curl -X POST -H "Accept:application/json" -H "Content-Type:application/json" -d '@/path/to/filename' https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2×tamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=
```

> File Should Contain the Following JSON:

```json
{
  "bool": {
    "must": [
      {
        "nested": {
          "path": "practitioner.address",
          "query": {
            "filtered": {
              "query": {
                "match_all": {}
              },
              "filter": {
                "geo_distance": {
                  "distance": "8mi",
                  "practitioner.address.location": {
                    "lat": 40.73,
                    "lon": -74.1
                  }
                }
              }
            }
          }
        }
      }
    ]
  }
}
```

* Distance search (8 miles) from a location:

`POST https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`


## Example: Query by Practitioner Specialty
```shell
curl -X POST -H "Accept:application/json" -H "Content-Type:application/json" -d '@/path/to/filename' https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2×tamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=
```

> File Should Contain the Following JSON:

```json
{
 "bool": {
   "must": [
     {
       "nested": {
         "path": "practitioner.specialty",
         "score_mode": "total",
         "query": {
           "bool": {
             "must": [
               {
                 "match": {
                   "practitioner.specialty.rank": "1"
                 }
               },
                {
                 "match": {
                   "practitioner.specialty.specialty_description": "internal medicine"
                 }
               },
               {
                 "match": {
                   "practitioner.specialty.type": "abbrev_specialty"
                 }
               }
             ]
           }
         }
       }
     }
   ]
 }
}
```

* Practitioner Specialty 1 search:

`POST https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`


## Layout

```json
{
    "name": "hms master file",
    "commonKey": [
        "hms_piid"
    ],
    "children": [
        {
            "name": "practitioner",
            "physicalName": "V_PRACTITIONER",
            "alias": "pract",
            "key": [
                "hms_piid"
            ],
            "physicalColumns": [
                "hms_piid",
                "name_prefix",
                "first_name",
                "middle_name1",
                "middle_name2",
                "last_name",
                "name_suffix",
                "credential1",
                "credential2",
                "gender_description",
                "practitioner_type_description",
                "date_born",
                "date_died",
                "update_date"
            ],
            "children": [
                {
                    "name": "address",
                    "physicalName": "v_practitioner_address",
                    "key": [
                        "hms_piid",
                        "address_key"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "address_key",
                        "address_type",
                        "rank",
                        "address_line1",
                        "address_line2",
                        "city_name",
                        "state_code",
                        "address_zip5",
                        "address_zip4",
                        "latitude",
                        "longitude",
                        "fips5_code",
                        "cbsa_code",
                        "census_block_group",
                        "address_std_code",
                        "lat_lon_resolution_code",
                        "lid",
                        "agid"
                    ],
                    "children": [
                        {
                            "name": "location",
                            "physicalName": "location",
                            "foreignKeys": [
                                "address_key"
                            ],
                            "joinLayout": "m_address_index_loc"
                        },
                        {
                            "name": "address_contact",
                            "physicalName": "v_practitioner_addr_contact",
                            "key": [
                                "hms_piid",
                                "address_key",
                                "value",
                                "type"
                            ],
                            "physicalColumns": [
                                "hms_piid",
                                "address_key",
                                "value",
                                "type"
                            ]
                        }
                    ]
                },
                {
                    "name": "specialty",
                    "physicalName": "v_specialty",
                    "key": [
                        "hms_piid",
                        "specialty_code",
                        "type",
                        "rank"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "specialty_description",
                        "type",
                        "rank"
                    ]
                },
                {
                    "name": "contact",
                    "physicalName": "v_practitioner_contact",
                    "key": [
                        "hms_piid",
                        "value",
                        "type"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "value",
                        "type"
                    ]
                },
                {
                    "name": "school",
                    "physicalName": "m_school",
                    "key": [
                        "hms_piid",
                        "school_code"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "school_code",
                        "school_name",
                        "year_graduated",
                        "rank"
                    ]
                },
                {
                    "name": "certification",
                    "physicalName": "m_certification",
                    "key": [
                        "hms_piid",
                        "certification_code"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "certification_description",
                        "board_description",
                        "expiration_year",
                        "issue_year",
                        "renewal_year",
                        "lifetime_flag",
                        "rank"
                    ]
                },
                {
                    "name": "dea",
                    "physicalName": "m_dea",
                    "key": [
                        "hms_piid",
                        "dea_id"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "dea_id",
                        "expiration_date",
                        "business_activity_code",
                        "business_activity_sub_code",
                        "dea_schedule",
                        "active_flag",
                        "rank"
                    ]
                },
                {
                    "name": "npi",
                    "physicalName": "m_npi",
                    "key": [
                        "hms_piid",
                        "npi"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "npi",
                        "rank",
                        "entity_type_code",
                        "replacement_npi",
                        "last_update_date",
                        "npi_status",
                        "deactivation_reason_code",
                        "deactivation_date",
                        "reactivation_date"
                    ],
                    "children": [
                        {
                            "name": "npi_taxonomy",
                            "physicalName": "m_npi_taxonomy",
                            "key": [
                                "hms_piid",
                                "npi",
                                "ansi_x12_taxonomy_code"
                            ],
                            "physicalColumns": [
                                "hms_piid",
                                "npi",
                                "ansi_x12_taxonomy_code",
                                "rank"
                            ]
                        }
                    ]
                },
                {
                    "name": "upin",
                    "physicalName": "m_upin",
                    "key": [
                        "hms_piid",
                        "upin_id"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "upin_id",
                        "rank"
                    ]
                },
                {
                    "name": "state_license",
                    "physicalName": "m_state_license",
                    "key": [
                        "hms_piid",
                        "state_code",
                        "state_license_id",
                        "state_license_type"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "state_code",
                        "state_license_id",
                        "state_license_type",
                        "state_license_status",
                        "state_license_qualifier",
                        "state_license_sub_qualifier",
                        "expiration_date",
                        "issue_date"
                    ]
                },
                {
                    "name": "state_csr",
                    "physicalName": "m_state_csr",
                    "key": [
                        "hms_piid",
                        "state_code",
                        "csr_number"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "csr_number",
                        "state_code",
                        "drug_schedule_auth_lvl_2",
                        "drug_schedule_auth_lvl_2n",
                        "drug_schedule_auth_lvl_3",
                        "drug_schedule_auth_lvl_3n",
                        "drug_schedule_auth_lvl_4",
                        "drug_schedule_auth_lvl_5",
                        "expiration_date",
                        "issue_date"
                    ]
                },
                {
                    "name": "is_active",
                    "physicalName": "m_vend_isactive",
                    "key": [
                        "hms_piid"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "vend_flag_value"
                    ],
                    "virtualColumns": {
                        "vend_flag_value": "value"
                    }
                },
                {
                    "name": "piid_migration",
                    "physicalName": "M_PIID_MIGRATION",
                    "key": [
                        "hms_piid",
                        "row_num"
                    ],
                    "physicalColumns": [
                        "hms_piid",
                        "new_piid",
                        "piid_migration_status",
                        "hops",
                        "date_modified"
                    ]
                }
            ]
        }
    ]
}
```

This is the layout associated with the masterfile index.

`POST https://api.hmsonline.com/v1/search/masterfile?pageSize=20&page=2&timestamp=1369844777731&key=EU2BD6eHBQeUMpMxDW9dmg==&signature=8qrFmQbQgILzdDeQfbJTxHXeZvE=`
