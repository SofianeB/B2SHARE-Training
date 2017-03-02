# List existing records
In this guide the retrieval of a list of existing records in paginated form is shown. Furthermore, the filtering of records for specific communities is explained.

### Setup your connection
Please make sure your machine has been properly set up to use Python and required packages. Follow [this](A_Setup_and_install.md) guide in order to do so.

This guide assumes you have successfully registered your account on the [B2SHARE website](https://trng-b2share.eudat.eu) using your institutional credentials or social ID through B2ACCESS. In addition, the loading of the token, importing Python packages and checking request responses will not be covered here.

## Retrieve a list of records
As shown in the guide '[Getting your API token](00_Getting_your_API_token.md)', to get the first set of records from the B2SHARE service, the following request suffices:

```python
>>> r = requests.get('https://trng-b2share.eudat.eu/api/records')
```

B2SHARE also returns the total number of records in the service:

```python
>>> result = json.loads(r.text)
>>> print result["hits"]["total"]
112
```

To get all records, which potentially may take a long time, the request can be altered by adding pagination parameters. In the following request, the page size and offset parameters define which records are returned. Increasing the size to a larger number and setting the page offset gives different results:

```python
>>> payload = {'size': 10,
               'page': 1
               }
>>> r = requests.get('https://trng-b2share.eudat.eu/api/records', params=payload)
```

To check whether any records are actually retrieved, again the JSON package can be used. Indeed, 112 records were found, but only records 101 to 200.

```python
>>> result = json.loads(r.text)
>>> print result["hits"]["total"]
112
>>> print len(result["hits"]["hits"])
10
```

Please note that the actual response text is very long and therefore not very usable yet since it cannot be interpreted as a data structure:

```python
>>> print type(r.text), len(r.text)
<type 'unicode'> 15126
```

### Retrieve a list of your draft records
The [Create a new record](05_Create_new_record.md) guide explains the creation of draft records. Here, the retrieval of all your draft records as a list is shown, which works similar to the retrieval of all published records (see last section). Only the draft records accessible by you will be available. Note that you now need to include your access token in order to authenticate yourself.

```python
>>> payload = {'drafts': 1,
               'access_token': token
              }
>>> r = requests.get('https://trng-b2share.eudat.eu/api/records', params=payload, verify=False)
```

Again, the result is processed to ease interpretation:
```python
>>> result = json.loads(r.text)
>>> print result["hits"]["total"]
3
>>> print json.dumps(result["hits"]["hits"][0], indent=4)
{
    "updated": "2017-01-15T14:11:20.765092+00:00",
    "metadata": {
        "publication_state": "draft",
        "owners": [
            10
        ],
        "open_access": true,
        "community": "e9b9792e-79fb-4b07-b6b4-b9c2bd06d095",
        "titles": [
            {
                "title": "Documentation"
            }
        ],
        "$schema": "https://trng-b2share.eudat.eu/api/communities/e9b9792e-79fb-4b07-b6b4-b9c2bd06d095/schemas/0#/draft_json_schema"
    },
    "id": "4d29477128404717bcdf4500f3b56bee",
    "links": {
        "self": "https://trng-b2share.eudat.eu/api/records/4d29477128404717bcdf4500f3b56bee/draft",
        "publication": "https://trng-b2share.eudat.eu/api/records/4d29477128404717bcdf4500f3b56bee"
    },
    "created": "2017-01-15T14:11:20.765083+00:00"
}
```

As is visible, the first hit has its `publication_state` metadata field is set to `draft`.

### Communities

To see what communities are available and which records they have published in B2SHARE, follow the [Communities guide](03_Communities.md).