# About the API
The Nextory API is the only way we communicate with the Nextory app backend. It is an internal API that is used by Nextory's app teams. Our API is designed around REST. It has predictable resource-oriented URLs, returns JSON responses and uses standard authentication and verbs.

# Documentation
Our API is defined using the OpenAPI Specification (OAS) v3.02. We refer to this as 'the definition'. This industry-standard model allows us to define how our API functions and what you should expect from it. We describe in detail what each endpoint does, each request parameter and all of their possible values, including their data types, formatting, rules, and whether or not they are required or optional. We also provide all of the errors and responses, along with schemas and sample data.

# Terminologies Used
We use 'account holder' not 'user' to describe the person who owns the account because an account can support more than one user via profiles. The term 'user' and 'profile' are used to refer to the actual person using the account via a profile. We say 'log in' and 'log out' to describe the actions of an existing user and 'sign up' to describe the creation of a new account.

# Standards and Ongoing Improvements
Our API was documented retrospectively so a few inconsistencies were picked up. For example, spelling errors (query 'helpfull' and get 'helpful' in the response). And subtle differences between common parameters ('sort' either uses 'relevance' as the default or in some cases 'popularity'). These will need to be fixed at the code level at some stage.

As a result, the technical writer doing the docs in 2021 could not use the re-usable components functionality available in the OAS as much as she would have liked. But this can be refined once some standards are put in place.

# Evaluating Responses
All responses are returned in JSON. In the definition, we provide an example of each response along with a response schema. The schema describes each property in the response in more detail including what each property contains, the data format of the values, whether each property is required or optional, and the structure.

The response example is typically a subset of all possible information returned from a request. The response schema describes all possible properties in the response. That's why we include both.

<!-- theme: danger -->
> **Important! Boolean Values in JSON Response Samples**
All "0" and "1" data values are shown as "false" and "true (respectively) because that is the only way the OAS handles booleans.

# Endpoints

| ID       | ENDPOINT                                        | TAG                       | DESCRIPTION                                                                                                                                                                                                                                     |
|----------|-------------------------------------------------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NX-013   | GET /searchbooks                                | Search                    | Does a simple search for books.                                                                                                                                                                                                                 |
| NX-014   | GET /searchbooksfacets                          | Search                    | Returns a list of facets that the account holder can use to narrow down the search results.                                                                                                                                                     |
| NX-0141  | GET /facets                                     | Search                    | Gets facets for a list of books that the account holder can use to narrow down the list.                                                                                                                                                        |
| NX-0251  | GET /readdiary/profilereadtimemeta              | Reading diary             | Gets reading-diary summary data.                                                                                                                                                                                                                |
| NX-0252  | GET /readdiary/profilereadtimelist              | Reading diary             | Gets a detailed list of historical read times for this account holder’s reading diary.                                                                                                                                                          |
| NX-0253  | GET /readdiary/<api_version>/profilestats       | Reading diary             | Returns a list of the most-liked authors, genres and books for this account holder.                                                                                                                                                             |
| NX-0254  | GET /readdiary/profileyearlystats               | Reading diary             | Returns how much reading time the account holder had each month in the last 12 months.                                                                                                                                                          |
| NX-0281  | GET /readdiary/<api_version>/adddeletegoals     | Reading goals             | Modifies reading goals.                                                                                                                                                                                                                         |
| NX-0282  | GET /readdiary/<api_version>/fetchgoals         | Reading goals             | Gets a list of current reading goals.                                                                                                                                                                                                           |
| NX-043   | GET /autocompleteresults                        | Search                    | Returns a list of autocomplete results for a provided search string. This endpoint is similar to NX-015 but not the same. It is newer, and is used by the app.                                                                                                                                                                             |
# Book Download
There is no specific endpoint for book downloads. The app gets a URL to download a book file in the NX-007 response ("url" in the "file" JSON object).

# Servers
All requests should use either the production or development root API URLs:

**Production** https://www.nextory.se/api/app/<module_name>/<API_version>/<br>
**Development** http://apptest.nextory.se:82/api/app/<module_name>/<API_version>/

# Modules
Each endpoint references a specific module. A module is the part of the backend system responsible for that endpoint. When a call reaches the backend, the value is checked then the API request is sent to the specified module. Examples are 'library' and 'catalog'.

# Mandatory Request Header Parameters
Since API version 6.4, the following required and optional parameters must be passed through the request header.

| PARAMETER NAME | SAMPLE VALUE                     | REQUIRED          | NOTES                                                                                                                         |
|----------------|----------------------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------|
| appid          | 200 for Android<br>201 for iOS   | required          | Identifies the app platform that the call comes from. Allowed values are 200 = Google Android, 201 = iOS, 202 = Huawei Android.                                                                                                                              |
| version        | 1.1                              | required          | Application version (CFBundleShortVersionString not CFBundleVersion).                                                         |
| model          | iPhone 6 Plus                    | required          | A string which describes the model of the device that the API call comes from. The OS provides this value. For informational purposes only.                                                                                                                              |
| osinfo         | ios8.4.1                         | optional          | A string which indicates the OS version being used. For informational/statistical purposes only.                                                                                                                              |
| deviceid       | xxxxx-xxxxx-xxxxx-xxxxx          | required          | A UUID is the unique identifier for application installation. If the user uninstalls and re-installs the app, the UUID changes. |
| locale         | sv_SE                            | required          | The account holder's language from a successful login response (if logged in) or device settings (if not logged in).                    |
| token          | 733393910B1B27CY0E4316EA10DF1CF1 | optional/required | Mandatory for all calls except /login, /forgotpassword and /salt.                                                    |
| canary         | "1.0"                            | optional          | This is the canary flag which is sent as part of the /login and /userdertails endpoints.                                                      |

# General Request Format

| CONTENT ENCODING | UTF-8                                              |
|------------------|----------------------------------------------------|
| Body Format      | JSON                                               |
| Hash-Algorithm   | MD5 (represent capitals with letters)              |
| Date Format      | yyyy-MM-dd HH:mm:ss Z<br>2021-05-06 21:56:16 +0200 |
| Unix Date Format | Should be in seconds                               |

# General Response Format

<!-- theme: danger -->
> **Important!** All responses must be in JSON format with UTF-8 encoding, since the default encoding is ISO.
> iOS APP expects the response in UTF-8, otherwise the app will throw an error.

| CONTENT ENCODING | UTF-8                                              |
|------------------|----------------------------------------------------|
| Body Format      | JSON                                               |
| Hash-Algorithm   | MD5 (represent capitals with letters)              |
| Date Format      | yyyy-MM-dd HH:mm:ss Z<br>2021-05-06 21:56:16 +0200 |
| Unix Date Format | Should be in seconds                               |

# Response Schemas - Samples
## Success
```
{
	"data": json-objects-here
}
```
## Fail
```
{
    "error": {
        "code": "error-code",
        "msg": "error-message"
    }
}
```
## Warning
```
{
	"data": {json-objects-here},
    "warning": {
        "code": "warning-code",
        "msg": "warning-message"
    }
}
```

# Proper SynchSettings and Data Validation Rules for NX-0201 (POST /library-sync)

This table explains the validation rules for data sent to the server. If the server validation fails, pieces of data will not be added into the server synchsetting database.

| PARAMETER                      | TYPE                  | POSSIBLE VALUES                                         | RULE EXCEPTIONS      | DESCRIPTION                                                                                                                                                                                                     |
|--------------------------------|-----------------------|---------------------------------------------------------|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| position                       | String                | 0.0-99999.0 (MAX_Double)                                | None                 | Invalid number values (like Inf, NaN) should not be saved. We can keep the previous value or if it does not exist then set it on 0.0.                                                                           |
| currentpercentage              | String                | 0.0-1.0                                                 | None                 | If the value is out of range, set to 1.0. If value is invalid (Inf, NaN) we should keep the previous value or if it does not exist then set it on 0.0.                                                              |
| cfi                            | String                | /4[epub_Hatadochalskad]/2/32,/1:0,/1:94                 | None                 | Used to define location in the Cloudshelf reader.                                                                                                                                                                 |
| idref                          | String                | epub_Hatadochalskad-7                                   | None                 | Used to define location in the Cloudshelf reader.                                                                                                                                                                 |
| pages                          | Array                 | []                                                      | None                 | Not used by the app. Only used for backward compatibility.                                                                                                                                                      |
| playersetting                  | Dictionary            | {}, all values != null                                  | None                 | All values should be valid in the same way as we check them now. If one of the values is not valid, we should keep previously saved values or set it on empty {}.                                           |
| playersetting > playbackspeed  | String                | 1.0, 1.25, 1.5, 1.75, 2.0,...                           | None                 | Invalid number values (like Inf, NaN) should not be saved. We can keep the previous value or if it does not exist then set it on 1.0. Should be treated as float (or double) without any restrictions on value. |
| syncdate                       | String                | The proper format is yyyy-MM-dd HH:mm:ss Z              | syncdate = null      | If datetime is invalid set value on null.                                                                                                                                                                       |
| setting                        | Dictionary            | {}, all values != null                                  | synctimestamp = null | All values should be valid in the same way as we check them now. If one of the values is not valid, we should keep previously saved values or set it on empty {}.                                           |
| completed                      | Integer               | -1,0,1,2                                                |                      |                                                                                                                                                                                                                 |
| bookmarks                      | Array of Dictionaries | [] - empty array                                        |                      | Bookmarks is one dictionary data in an array and each dictionary should be validated. If a condition is not satisfied then bookmarks should not be added.                                                         |
| bookmarks > pagepositioninbook  | String                | pagepositioninbook != null,<br>0.0-99999.0 (MAX_Double) |                      |                                                                                                                                                                                                                 |
| bookmarks > cfi                 | String                | /4[epub_Hatadochalskad]/2/32,/1:0,/1:94                 |                      | Used to define location in the Cloudshelf reader.                                                                                                                                                                 |
| bookmarks > idref               | String                | epub_Hatadochalskad-7                                   |                      | Used to define location in the Cloudshelf reader.                                                                                                                                                                 |
| bookmarks > type                | String                | type = 0-MAX_Integer, type != null                      |                      |                                                                                                                                                                                                                 |
| bookmarks > chapterindex        | Integer               | chapterindex = 0-MAX_Integer                            |                      |                                                                                                                                                                                                                 |
| bookmarks > title               | String                | title != null                                           |                      |                                                                                                                                                                                                                 |
| bookmarks > datetime            | String                | The proper format is yyyy-MM-dd HH:mm:ss Z              | datetime = null      | If datetime is invalid set value on null.                                                                                                                                                                       |
| highlights                     | Array                 | []                                                      |                      |                                                                                                                                                                                                                 |
| highlights > chapterindex       | Integer               | chapterindex = 0-MAX_Integer                            |                      | Highlights is one dictionary data in an array and each dictionary should be validated. If a condition is not satisfied then highlights should not be added.                                                       |
| highlights > datetime           | String                | The proper format is yyyy-MM-dd HH:mm:ss Z              | datetime = null      | If datetime is invalid set value on null.                                                                                                                                                                       |
| highlights > title              | String                | title != null                                           |                      |                                                                                                                                                                                                                 |
| highlights > pagepositioninbook | String                | pagepositioninbook != null,<br>0.0-99999.0 (MAX_Double) |                      |                                                                                                                                                                                                                 |
| highlights > startindex         | Integer               | !=null<br>0-MAX_Integer                                 |                      |                                                                                                                                                                                                                 |
| highlights > startoffset        | Integer               | !=null<br>0-MAX_Integer                                 |                      |                                                                                                                                                                                                                 |
| highlights > endindex           | Integer               | !=null<br>0-MAX_Integer                                 |                      |                                                                                                                                                                                                                 |
| highlights > endoffset          | Integer               | !=null<br>0-MAX_Integer                                 |                      |                                                                                                                                                                                                                 |
| highlights > cfi                | String                | /4[epub_Hatadochalskad]/2/32,/1:0,/1:94                 |                      | Used to define location in the Cloudshelf reader.                                                                                                                                                                 |
| highlights > idref              | String                | epub_Hatadochalskad-7                                   |                      | Used to define location in the Cloudshelf reader.                                                                                                                                                                 |
| highlights > text               | String                | text != null                                            |                      |                                                                                                                                                                                                                 |

# Status and Error Codes
Requests that can be successfully parsed by the backend will return a 200 OK HTTP status code. If something goes wrong in the server application logic, one of the error codes in the following table will be returned. Error codes are specific to our API. They are one layer above standard HTTP error codes. We use HTTP error codes for pure infrastructure problems only.

| CODE  | DESCRIPTION                                                                                                                             |
|-------|-----------------------------------------------------------------------------------------------------------------------------------------|
| 1000  | Input missing or invalid.                                                                                                               |
| 1001  | You were logged out because your membership got canceled.                                                                               |
| 1002  | You were logged out because you exceeded your daily/monthly download limit.                                                             |
| 1003  | You were logged out because you downgraded your subscription.                                                                           |
| 1004  | You were logged out because you upgraded your subscription.                                                                             |
| 1005  | You were logged out because you are currently logged in to too many devices.                                                            |
| 1006  | Your current session expired because you are currently logged in to too many devices.                                                   |
| 1007  | You were logged out because your profile was deleted.                                                                                   |
| 1008  | You were logged out because your account password changed.                                                                              |
| 1009  | You were logged out. Please log in again.                                                                                               |