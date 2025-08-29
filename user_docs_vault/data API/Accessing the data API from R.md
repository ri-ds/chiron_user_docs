
## Instructions using IMPRINT as an example

These instructions will walk you through creating a data API token and using it to pull a Chiron report into R as a Data Frame.

1. Go to the IMPRINT test website
	- You must be on campus or connected by VPN.
	- https://imprint-test.research.cchmc.org/
	- Keep in mind this is a test website and the data isn't reliable.
	- For example, I don't seem to have the freezerworks data loaded currently.
	
2. Open the Data API Token view
	- Click your username in the top right.
	- Select "Data API Tokens"

![[Pasted image 20240702115531.png]]

	
3. Create an API token
	- Select the dataset - for IMPRINT there is only 1 option "default_dataset".
	- Optionally add a comment to remind yourself where you are using this token.
	- Click "Create New Token"
	- You **MUST** record your token value immediately. The website doesn't store the value, so you can't come back and get it again later.

![[Pasted image 20240826220757.png]]
![[Pasted image 20240826220803.png]]


4. What to do if you lose your token
	- Come back to Data API Token view anytime and just create a new token.
	- You can only have 1 token per dataset, so it will automatically replace any existing token.
	
5. Identify or create a report that you want to work with
	- The data API will allow you to download an existing report.
	- You will need the ID # for the report you're interested in.
	
6. Use your token in R
	- You must be on campus or connected by VPN.
	- See the example code below. You can copy/paste the provided `get_chiron_report()` function directly into your R script.
	- My R function requires the token to be stored in a separate file.
		- This is intended to reduce the risk of accidentally sharing your token by storing it directly in your R script.
		- Save your token as a text file on your computer. You can use Notepad or any text editor.
		- The file can go anywhere you like. Just make note of the filepath.
	- Then you will also provide the URL (https://imprint-test.research.cchmc.org) and the report number.
	- You will get back a data frame with the report data.

##### function to get a saved Chiron report
```r
require(httr)

get_chiron_report <- function(site_url, token_filepath, report_number) {
  # Returns a saved Chiron report as a data frame.
  # 
  # example:
  # site_url <- "https://imprint-test.research.cchmc.org"
  # token_filepath <- "C:\\Users\\meisz8\\Desktop\\mytoken.txt"
  # report_number <- 100
  
  # STEP 1: Get the API token from the file at token_filepath.
  # check if the file exists
  if (!file.exists(token_filepath)) {
    stop("Error: The file does not exist.")
  }
  # read the file
  token <- readLines(token_filepath, encoding="UTF-8", warn=FALSE)
  # check if the file has more than one line
  if (length(token) > 1) {
    stop("Error: The file contains more than one line of text.")
  }
  # remove trailing white spaces or carriage returns
  token <- gsub("\\s+$", "", token)
  
  # STEP 2: Get an OAuth2 access_token to use for this session.
  auth_header <- paste("Bearer", token, sep=" ")
  auth_endpoint <- paste(site_url, "/data_api/authenticate", sep="")
  auth_call <- POST(
    auth_endpoint,
    add_headers(.headers = c("Authorization"=auth_header))
  )
  auth_response <- content(auth_call)
  
  # STEP 3: Get the dataset.
  auth_header2 <- paste("Bearer", auth_response["access_token"], sep=" ")
  report_endpoint <- paste(
    site_url, "/data_api/reports/", report_number, "/export_csv/", sep=""
  )
  report_call <- GET(
    report_endpoint,
    add_headers(.headers = c("Authorization"=auth_header2))
  )
  data <- read.csv(
    textConnection(content(report_call, "text", encoding="UTF-8")), 
    header=TRUE, 
    encoding="UTF-8"
  )
  
  # return data frame
  return(data)
}
```

##### Example usage
```R
site_url <- "https://imprint-test.research.cchmc.org"
token_filepath <- "C:\\Users\\meisz8\\Desktop\\to_delete\\chiron_demo_token.txt"
report_number <- 100

data <- get_chiron_report(site_url, token_filepath, report_number)
View(data)
```

