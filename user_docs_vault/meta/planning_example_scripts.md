
function to get a saved Chiron report
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

Example usage:
```R
site_url <- "https://imprint-test.research.cchmc.org"
token_filepath <- "C:\\Users\\meisz8\\Desktop\\to_delete\\chiron_demo_token.txt"
report_number <- 100

data <- get_chiron_report(site_url, token_filepath, report_number)
View(data)
```

