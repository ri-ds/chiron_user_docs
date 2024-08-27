# Data API Authentication Process

This will walk through the steps to authenticate yourself and retrieve report data as a CSV file. If you are working in R, we already have a pre-built function that will handle this process for you. But with these instructions you should be able to connect to the data API from any programming language/environment capable of running HTTP requests and reading CSV files.

Also, these notes are for IMPRINT, but the same approach will work for RheumsMart, HeartsMart, and any other Chiron instances on the CCHMC network. However, for Chiron instances running in the RDCRN network or at UC/UC Health there will probably be a different workflow.

### before you start: need a data API token and Report ID(s)

You should already have an IMPRINT user account, which you can use to create your API token. Contact [John Meinken](john.meinken@cchmc.org) if you're not sure how to create an API token. Remember that API tokens are the same as a username/password, so you should store your token in a safe place and not share with anyone else.

Currently, the data API can only be used to download pre-existing reports. So you will want to first use the website to find/create report(s) with the data you're interested in.

## Step 1: use your data API token to get an OAuth 2 access token

What is the difference between these two token types?
- data API token - stored long-term and used at the start of each data API session to authenticate yourself 
- access token - used during a data API session to retrieve data; generally has a short life-span (10 minutes)

HTTP request:
- URL: `https://imprint-test.research.cchmc.org/data_api/authenticate`
- type: HTTP POST request
- special required headers
	- Authorization = "Bearer {your data api token}"
- content body: can leave empty

HTTP response:
- headers
	- Content-Type: application/json
- body:
	- access_token: {your temporary access token for this session}
	- expires_in: {life of the token in seconds}
	- token_type: "Bearer"
	- scope: "read write"
	- refresh_token: {a token you can use to extend the session}

You will probably only be interested in the "access_token". You can also check "expires_in" to see how long your access token will last.

## Step 2: Use your access token to get a report CSV

Now we will use the access token we generated in Step 1 to get a report.

HTTP request:
- URL: `https://imprint-test.research.cchmc.org/data_api/reports/{report_id}/export_csv/`
- type: HTTP GET request
- special required headers
	- Authorization = "Bearer {your access token}"
- content body: can leave empty

HTTP response:
- headers:
	- Content-Type: text/csv
	- Content-Disposition: attachment; filename="{default filename}"
- body
	- CSV file (includes a header row as the first row)
	- In case you have problems reading the file:
		- encoding is UTF-8
		- comma delimited
		- strings are demarcated with double quotes when necessary
		- date format is "yyyy-mm-dd" (in the near future we might change this or allow you to specify a format)