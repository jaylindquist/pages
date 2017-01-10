+++
menu = "main"
date = "Thu, 20 Jun 2013 04:15:27 +0000"
title = "Creating a Reusable URL with Spring RestTemplate"
draft = false
+++

It's a fact. Spring has a way of simplifying development in a developer-centric way. Spring's <a href="http://blog.springsource.org/2009/03/27/rest-in-spring-3-resttemplate/" title="RestTemplate">RestTemplate</a> gets us an easy to use REST interface.

We can take it one step further by making the template creation a bit more flexible. What we need is a way to build a template from a known base URL and known parameters with configurable path and parameters.

For instance, a search template could look like: `http://api.example.com/search?query={query}&sortOrder={sortOrder}&maxResults={maxResults}&api_key=myKey&format=json`

Here, we have a few different requirements needed to build a template:

* The constant `baseUrl` - `http://api.example.com`
* The configurable `path` - `/search`
* The configurable parameters - `query={query}&sortOrder={sortOrder}&maxResults={maxResults}`
* The constant parameters - `api_key=myKey&format=json`

This is a fairly straight forward implementation which I have found to be very useful.

```
class RestTemplateUtil {
    private String baseUrl
    private String key
    private String format

    String buildTemplateUrl(String path, Set queryParameters) {
        def builder = new StringBuilder()
        builder.append(this.baseUrl).append(path).append('?')
        queryParameters.each { queryKey ->
            builder.append(queryKey).append('={').append(queryKey).append('}&')
        }
        builder.append('api_key=').append(this.key).append('&format=').append(this.format)
        return builder.toString()
    }

    void setBaseUrl(String baseUrl) {
        this.baseUrl = baseUrl	
    }

    void setKey(String key) {
        this.key = key
    }

    void setFormat(String format) {
        this.format= format
    }
}
```

Once configured, the developer builds the template from the API path and a Set of parameter names. This Set is easily constructed from a Map of parameters and their values used directly by the RestTemplate.

```
def templateUtil = new RestTemplateUtil()
templateUtil.baseUrl = 'http://api.example.com'
templateUtil.key = 'myKey'
templateUtil.format = 'json'

def params = ['query':'search terms', 'sortOrder':'asc', 'maxResults':20]
def template = templateUtil.buildTemplateUrl('/search', params.keySet())

def rest = new RestTemplate()
def result = rest.getForObject(template, String, params)
println result
```

This could be expanded easily by changing the utility class follow the builder pattern or just incorporating the `getForObject` call directly. However, the above is simple, straight forward and helps reduce code by creating a reusable asset that can easily be leveraged by any code needing RestTemplates
