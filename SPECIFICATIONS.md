# Technical Specifications

## Requiirements 
Feature Functionality Must Incorportate Rate Limiting Accordingly. 
- Free Tier Rate Limits `https://www.microsoft.com/en-us/bing/apis/pricing`


## Python SDK Setup 
These three options would be interesting to have each as a command in the MCP. However the first order of business will be figuring out which one is able to search using those specific search parameters for the job sites that are hidden. 

1. The following URL and directions in the following steps are for **Bing Web Search**
[https://learn.microsoft.com/en-us/bing/search-apis/bing-web-search/overview]

2. Must identify the difference between the Bing Web Search and the **Bing Custom Search**
[https://learn.microsoft.com/en-us/bing/search-apis/bing-custom-search/ovearch-client-library-python]

3. The only other of interest is the **Bing News Search**
[https://learn.microsoft.com/en-us/bing/search-apis/bing-news-search/quickstarts/sdk/news-search-client-library-python]


### Create Environment 

1. Create a new virtual environment
`python -m venv mytestenv` 

2. Activate the virtual environment
`mytestenv\Scripts\activate.bat`

3. Install Bing Search SDK Dependencies
`cd mytestenv`
`python -m pip install azure-cognitiveservices-search-websearch`

### Create Client 

4. Create Client in IDE 
```
# Import required modules.
from azure.cognitiveservices.search.websearch import WebSearchClient
from azure.cognitiveservices.search.websearch.models import SafeSearch
from msrest.authentication import CognitiveServicesCredentials

# Replace with your subscription key.
subscription_key = "YOUR_SUBSCRIPTION_KEY"

# Instantiate the client and replace with your endpoint.
client = WebSearchClient(endpoint="YOUR_ENDPOINT", credentials=CognitiveServicesCredentials(subscription_key))

# Make a request. Replace Yosemite if you'd like.
web_data = client.web.search(query="Yosemite")
print("\r\nSearched for Query# \" Yosemite \"")

'''
Web pages
If the search response contains web pages, the first result's name and url
are printed.
'''
if hasattr(web_data.web_pages, 'value'):

    print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

    first_web_page = web_data.web_pages.value[0]
    print("First web page name: {} ".format(first_web_page.name))
    print("First web page URL: {} ".format(first_web_page.url))

else:
    print("Didn't find any web pages...")

'''
Images
If the search response contains images, the first result's name and url
are printed.
'''
if hasattr(web_data.images, 'value'):

    print("\r\nImage Results#{}".format(len(web_data.images.value)))

    first_image = web_data.images.value[0]
    print("First Image name: {} ".format(first_image.name))
    print("First Image URL: {} ".format(first_image.url))

else:
    print("Didn't find any images...")

'''
News
If the search response contains news, the first result's name and url
are printed.
'''
if hasattr(web_data.news, 'value'):

    print("\r\nNews Results#{}".format(len(web_data.news.value)))

    first_news = web_data.news.value[0]
    print("First News name: {} ".format(first_news.name))
    print("First News URL: {} ".format(first_news.url))

else:
    print("Didn't find any news...")

'''
If the search response contains videos, the first result's name and url
are printed.
'''
if hasattr(web_data.videos, 'value'):

    print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

    first_video = web_data.videos.value[0]
    print("First Videos name: {} ".format(first_video.name))
    print("First Videos URL: {} ".format(first_video.url))

else:
    print("Didn't find any videos...")
```

5. Replace [SUBSCRIPTION_KEY] with a valid subscription key.
6. Replace [YOUR_ENDPOINT] with your endpoint URL in portal and remove the *bing/v7.0* section from the endpoint.
7. Run the program. For example: `python your_program.py`

### Define Functions & Filter Results 
This sample uses the [count] and [offset] parameters to limit the number of results returned using the SDK's *search method*. The [name] and [url] for the first result are printed.

Search Method: `https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python&preserve-view=true`

1. Add this code to you Python Project 
```
# Declare the function.
 def web_results_with_count_and_offset(subscription_key):
     client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

     try:
         '''
         Set the query, offset, and count using the SDK's search method. See:
         https://learn.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
         '''
         web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
         print("\r\nSearching for \"Best restaurants in Seattle\"")

         if web_data.web_pages.value:
             '''
             If web pages are available, print the # of responses, and the first and second
             web pages returned.
             '''
             print("Webpage Results#{}".format(len(web_data.web_pages.value)))

             first_web_page = web_data.web_pages.value[0]
             print("First web page name: {} ".format(first_web_page.name))
             print("First web page URL: {} ".format(first_web_page.url))

         else:
             print("Didn't find any web pages...")

     except Exception as err:
         print("Encountered exception. {}".format(err))
```

2. Run the Program 

### Filter for News & Freshness 
This sample uses the [response_filter] and [freshness] parameters to filter search results using the SDK's *search method*. The search results returned are limited to news articles and pages that Bing has discovered within the last 24 hours. The [name] and [url] for the first result are printed.

Search Method: `https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations`

1. Add this code to you Python Project 
```
# Declare the function.
def web_search_with_response_filter(subscription_key):
    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
    try:
        '''
        Set the query, response_filter, and freshness using the SDK's search method. See:
        https://learn.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
        '''
        web_data = client.web.search(query="xbox",
            response_filter=["News"],
            freshness="Day")
        print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

        '''
        If news articles are available, print the # of responses, and the first and second
        articles returned.
        '''
        if web_data.news.value:

            print("# of news results: {}".format(len(web_data.news.value)))

            first_web_page = web_data.news.value[0]
            print("First article name: {} ".format(first_web_page.name))
            print("First article URL: {} ".format(first_web_page.url))

            print("")

            second_web_page = web_data.news.value[1]
            print("\nSecond article name: {} ".format(second_web_page.name))
            print("Second article URL: {} ".format(second_web_page.url))

        else:
            print("Didn't find any news articles...")

    except Exception as err:
        print("Encountered exception. {}".format(err))

# Call the function.
web_search_with_response_filter(subscription_key)
```

2. Run the Program

### Use safe search, answer count, and the promote filter
This sample uses the [answer_count], [promote], and [safe_search] parameters to filter search results using the SDK's *search method*. The [name] and [url] for the first result are displayed.

Search Method: `https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations`

1. Add this code to you Python Project 
```
# Declare the function.
def web_search_with_answer_count_promote_and_safe_search(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        '''
        Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
        https://learn.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
        '''
        web_data = client.web.search(
            query="Niagara Falls",
            answer_count=2,
            promote=["videos"],
            safe_search=SafeSearch.strict  # or directly "Strict"
        )
        print("\r\nSearching for \"Niagara Falls\"")

        '''
        If results are available, print the # of responses, and the first result returned.
        '''
        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))
```

2. Run the Program

### Clean up resources
When you're done with this project, make sure to remove your subscription key from the program's code and to deactivate your virtual environment.

## Learn how to use the Cognitive Services Python SDK with these samples

`https://github.com/Azure-Samples/cognitive-services-python-sdk-samples?tab=readme-ov-file`
