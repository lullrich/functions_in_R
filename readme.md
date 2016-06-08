---
title: "Tutorial - Functions in R"
author: "Lars Ullrich"
date: "7 June 2016"
output: 
  html_document:
    theme: simplex
---



## What are functions?

Functions in R or any other programming language are a way to encapsulate code in order to make it re-usable. A commonly used acronym in the world of programming is "**DRY**" which stands for "**D**on't **R**epeat **Y**ourself". It basically means that whenever you find yourself writing the same lines of code multiple times, you should stop to think about what you are doing. Maybe what you are trying to do could be further abstracted or encapsulated into a function. When you write a function to avoid repeating yourself, you also ensure that when you need to change that function, you will only have to change it in one place: the function definition. But more on that later...

Many functions are already built into R itself and all programming languages come with many built-in functions to make dealing with day-to-day problems easier.  

Whenever you find yourself writing a word followed by opening and closing brackets like `library("stringr")` and you haven't already loaded different packages, you are using a built-in function of R (in this case a function to load additional libraries). Loading packages itself is just a way to get additional functions into R. In the case of [stringr](https://github.com/hadley/stringr) we get additional functions to deal with characters (strings).



## Example {.tabset .tabset-fade .tabset-pills}
### Code

```r
library("stringr")
my_string <- "This is boring. I know this already. Moreover, string manipulation is hardly what I consider fun. Way too tedious and totally not worth my time. Please, let's not waste any more time. Get on with it already!"
my_new_string <- str_extract_all(my_string, "(This is)|(fun)|(and)|(totally)|(worth my time)|(let's)|(Get on with it)")
my_new_string
```

```
## [[1]]
## [1] "This is"        "fun"            "and"            "totally"       
## [5] "worth my time"  "let's"          "Get on with it"
```
### Explanation
Here, after loading the library we want to use, we create a simple string we call `my_string`. We then use the `str_extract_all` function from *stringr* to extract the parts we want to keep. In this case the patterns "this is" or "fun" or "and" or "totally" or "worth my time" etc. giving us:

```
## [1] "This is"        "fun"            "and"            "totally"       
## [5] "worth my time"  "let's"          "Get on with it"
```
I'm pretty sure that there is an alternative way to extract the parts we wanted from this string, but since this one (sort of) worked, we can move on. We can always come back later and optimize our code. 

### Excercise
Try to use functions deliberately. All built-in functions in R and every function from a package should have proper documentation. Make a habit of calling up the documentation whenever you are using a new function or whenever you are stuck. You can access the documentation of a function by pressing F1 in RStudio when the cursor is on the name of the function or executing `?FUNCTION_NAME` where `FUNCTION_NAME` rather unsurprisingly is the name of the function. Read the documentation carefully and have a look at the examples, if there are any (they usually are at the bottom of the page). Another useful thing for you to do when you are stuck is searching for the function - or the package - on the search engine of your choice (a process also known as googling...). Usually you will find links to the documentation for the package and, depending on how specific your query was, to stackoverflow questions. Use those resources!

## Writing our own function {.tabset .tabset-fade .tabset-pills}

### Code

```r
my_function <- function() {
  # Your function code goes here.
  # For now let's just say:
  print("Wooohoooooo, functions!")
}
my_function()
```

```
## [1] "Wooohoooooo, functions!"
```

### Explanation
This is a very, very simple example of a custom function. It basically just wraps the `print` funtion in our own function. So this really is just a re-naming of the `print` function.

Every definition of a new function starts with the `function` keyword. After that come the parantheses which will hold the parameters of the function, if it accepts any (we will use and try this out later)

### Exercise
Write a simple function. It isn't hard and the possibilities are endless. 

## Writing more advanced functions {.tabset .tabset-fade .tabset-pills}

### Code

```r
my_function <- function(parameter1, parameter2) {
  str_c(parameter1, parameter2, sep=" ")
}
my_function("Hello", "World")
```

```
## [1] "Hello World"
```

### Explanation
Again, a very simple (and quite useless) example. It uses the `str_c` function of the stringr package to concatenate two strings. This is just an illustration of parameters being used in a function. 

### Exercise
Rewrite your function to accept parameters. 

# A real world example
Now that the very basics are all clear and out of the way, we can apply our new found knowledge to the real world.
Functions are very powerful and even more so when we combine them. This is an example of combining functions, using the output of one function as the input of another and building a pipeline. We will build a pipeline that get's a list of URLs from a website and then visits each url in the list to extract some information from that site.

Let's get started.

## Loading the libraries {.tabset .tabset-fade .tabset-pills}

### Code
First of all, we have to load a library. 

```r
library("rvest")
```

### Explanation
Nothing much to see here. We load a library to help us with our task. [rvest](https://github.com/hadley/rvest) is a web scraping library written by [Hadley Wickham](https://github.com/hadley). He is the authour of many incredibly useful packages like [dplyr](https://github.com/hadley/dplyr) or [tidyr](https://github.com/hadley/tidyr)

## Defining our function {.tabset .tabset-fade .tabset-pills}

### Code

```r
get_urls <- function() {
  # Simple function to get the urls for the teams in UEFA 2016.
  # Returns a vector of URLs.
  
  # The URL where we can find our list of links
  teams_url <- "http://www.uefa.com/uefaeuro/"
  
  # Extracting the URLs from the links to the team pages. 
  teams <-  read_html(teams_url) %>% # getting the html
    html_nodes("a.group-item") %>% # getting only the <a> tags which have the class "group-item"
    html_attr("href",TRUE) # extracting the URL from the href attribute
  
  # removing duplicates and returning the list of URLs 
  # ("list" is used in the English sense of the word. What we get back is a vector.)
  unique(teams)
}
get_urls()
```

```
##  [1] "/uefaeuro/season=2016/teams/team=2/index.html"    
##  [2] "/uefaeuro/season=2016/teams/team=43/index.html"   
##  [3] "/uefaeuro/season=2016/teams/team=113/index.html"  
##  [4] "/uefaeuro/season=2016/teams/team=128/index.html"  
##  [5] "/uefaeuro/season=2016/teams/team=39/index.html"   
##  [6] "/uefaeuro/season=2016/teams/team=57451/index.html"
##  [7] "/uefaeuro/season=2016/teams/team=58836/index.html"
##  [8] "/uefaeuro/season=2016/teams/team=144/index.html"  
##  [9] "/uefaeuro/season=2016/teams/team=47/index.html"   
## [10] "/uefaeuro/season=2016/teams/team=63/index.html"   
## [11] "/uefaeuro/season=2016/teams/team=109/index.html"  
## [12] "/uefaeuro/season=2016/teams/team=57166/index.html"
## [13] "/uefaeuro/season=2016/teams/team=56370/index.html"
## [14] "/uefaeuro/season=2016/teams/team=58837/index.html"
## [15] "/uefaeuro/season=2016/teams/team=122/index.html"  
## [16] "/uefaeuro/season=2016/teams/team=135/index.html"  
## [17] "/uefaeuro/season=2016/teams/team=13/index.html"   
## [18] "/uefaeuro/season=2016/teams/team=66/index.html"   
## [19] "/uefaeuro/season=2016/teams/team=64/index.html"   
## [20] "/uefaeuro/season=2016/teams/team=127/index.html"  
## [21] "/uefaeuro/season=2016/teams/team=8/index.html"    
## [22] "/uefaeuro/season=2016/teams/team=57/index.html"   
## [23] "/uefaeuro/season=2016/teams/team=58/index.html"   
## [24] "/uefaeuro/season=2016/teams/team=110/index.html"
```
We got our list of URLs. Great! Let's ignore for now that these are relative and not absolute URLs and move on.

### Explanation
This is a very simple function. It goes to a URL and looks for links that all share a class. It then puts their href attributes in a vector. All we needed to do that are three functions from the rvest package: `read_html`, `html_nodes` and `html_attr`. We can combine these functions using the `%>%` operator. This is a very useful tool from the [magrittr](https://github.com/cran/magrittr) package that is used in many other packages. Read the Github page I've linked to to get a basic understanding of how to use it. 

The `%>%` operator chains functions together using the output of one function as input for the next. It is very easy to understand, if we think of it as the word "then". Whenever you see the `%>%` operator just replace it in your head with the word "then" and it will become much clearer what is happening.

In our example it would translate to: `read_html(teams_url)` THEN `html_nodes(OUTPUT OF read_html, "a.group-item")`
THEN `html_attr(OUTPUT OF html_nodes, "href", TRUE)`. The output of the last function (`html_attr`) is what is stored in the `teams`variable.

## Making our function better {.tabset .tabset-fade .tabset-pills}

### Code
So far our function isn't very functiony (which isn't a word but should be). Let's generalize a bit more. Right now our function only ever visits the one URL and extracts the same group of links. We can fix that by passing the url as a parameter so we can change it with every function call.

```r
get_urls <- function(my_url) {
  # Simple function to get a list of URLs
  # Returns a vector of URLs.
 
  # Extracting the URLs from the links to the team pages. 
  teams <-  read_html(my_url) %>% # getting the html
    html_nodes("a.group-item") %>% # getting only the <a> tags which have the class "group-item"
    html_attr("href",TRUE) # extracting the URL from the href attribute
  
  # removing duplicates and returning the list of URLs 
  # ("list" is used in the English sense of the word. What we get back is a vector.)
  unique(teams)
}
get_urls("http://www.uefa.com/uefaeuro/")
```

```
##  [1] "/uefaeuro/season=2016/teams/team=2/index.html"    
##  [2] "/uefaeuro/season=2016/teams/team=43/index.html"   
##  [3] "/uefaeuro/season=2016/teams/team=113/index.html"  
##  [4] "/uefaeuro/season=2016/teams/team=128/index.html"  
##  [5] "/uefaeuro/season=2016/teams/team=39/index.html"   
##  [6] "/uefaeuro/season=2016/teams/team=57451/index.html"
##  [7] "/uefaeuro/season=2016/teams/team=58836/index.html"
##  [8] "/uefaeuro/season=2016/teams/team=144/index.html"  
##  [9] "/uefaeuro/season=2016/teams/team=47/index.html"   
## [10] "/uefaeuro/season=2016/teams/team=63/index.html"   
## [11] "/uefaeuro/season=2016/teams/team=109/index.html"  
## [12] "/uefaeuro/season=2016/teams/team=57166/index.html"
## [13] "/uefaeuro/season=2016/teams/team=56370/index.html"
## [14] "/uefaeuro/season=2016/teams/team=58837/index.html"
## [15] "/uefaeuro/season=2016/teams/team=122/index.html"  
## [16] "/uefaeuro/season=2016/teams/team=135/index.html"  
## [17] "/uefaeuro/season=2016/teams/team=13/index.html"   
## [18] "/uefaeuro/season=2016/teams/team=66/index.html"   
## [19] "/uefaeuro/season=2016/teams/team=64/index.html"   
## [20] "/uefaeuro/season=2016/teams/team=127/index.html"  
## [21] "/uefaeuro/season=2016/teams/team=8/index.html"    
## [22] "/uefaeuro/season=2016/teams/team=57/index.html"   
## [23] "/uefaeuro/season=2016/teams/team=58/index.html"   
## [24] "/uefaeuro/season=2016/teams/team=110/index.html"
```

### Explanation
We took the URL out of the function and instead pass it as a parameter whenever the function is called. This is much better. We could now change the URL everytime we call the function. However, since we also defined the html nodes to look for, that will only work, if the new URL also has `<a>` tags with the class "group-item". Let's fix that in the exercise.

### Exercise
Do what we did with the URL in the last step and turn the node to look for ("a.group-item") into a parameter.

### Solution
If you haven't tried to do it yourself and went straight to the solution, shame on you! ;)

```r
get_urls <- function(my_url, my_nodes) {
  # Simple function to get a list of URLs
  # Returns a vector of URLs.
 
  # Extracting the URLs from the links to the team pages. 
  teams <-  read_html(my_url) %>% # getting the html
    html_nodes(my_nodes) %>% # getting only the <a> tags which have the class "group-item"
    html_attr("href",TRUE) # extracting the URL from the href attribute
  
  # removing duplicates and returning the list of URLs 
  # ("list" is used in the English sense of the word. What we get back is a vector.)
  unique(teams)
}
get_urls("http://www.uefa.com/uefaeuro/", "a.group-item")
```

```
##  [1] "/uefaeuro/season=2016/teams/team=2/index.html"    
##  [2] "/uefaeuro/season=2016/teams/team=43/index.html"   
##  [3] "/uefaeuro/season=2016/teams/team=113/index.html"  
##  [4] "/uefaeuro/season=2016/teams/team=128/index.html"  
##  [5] "/uefaeuro/season=2016/teams/team=39/index.html"   
##  [6] "/uefaeuro/season=2016/teams/team=57451/index.html"
##  [7] "/uefaeuro/season=2016/teams/team=58836/index.html"
##  [8] "/uefaeuro/season=2016/teams/team=144/index.html"  
##  [9] "/uefaeuro/season=2016/teams/team=47/index.html"   
## [10] "/uefaeuro/season=2016/teams/team=63/index.html"   
## [11] "/uefaeuro/season=2016/teams/team=109/index.html"  
## [12] "/uefaeuro/season=2016/teams/team=57166/index.html"
## [13] "/uefaeuro/season=2016/teams/team=56370/index.html"
## [14] "/uefaeuro/season=2016/teams/team=58837/index.html"
## [15] "/uefaeuro/season=2016/teams/team=122/index.html"  
## [16] "/uefaeuro/season=2016/teams/team=135/index.html"  
## [17] "/uefaeuro/season=2016/teams/team=13/index.html"   
## [18] "/uefaeuro/season=2016/teams/team=66/index.html"   
## [19] "/uefaeuro/season=2016/teams/team=64/index.html"   
## [20] "/uefaeuro/season=2016/teams/team=127/index.html"  
## [21] "/uefaeuro/season=2016/teams/team=8/index.html"    
## [22] "/uefaeuro/season=2016/teams/team=57/index.html"   
## [23] "/uefaeuro/season=2016/teams/team=58/index.html"   
## [24] "/uefaeuro/season=2016/teams/team=110/index.html"
```

## Combining our function with other functions {.tabset .tabset-fade .tabset-pills}
Ok, so now we have a working function we can use to get links from a URL. All we need to do is pass it the URL to go to and the nodes we are intrested in. It is a rather useful little function and wasn't very hard to write. Let's combine it with other functions to get the URLs for all teams (like we did until now) and visit them all to get every player URL. 

### Code

```r
player_urls <- get_urls("http://www.uefa.com/uefaeuro/", "a.group-item") %>% # Get the team URLs, THEN ( %>% )
  lapply(url_absolute, "http://www.uefa.com") %>% # make the URLs absolute, THEN
  lapply(get_urls, "span.squad--player-name > a") %>% # re-use our function to get the player URLs, THEN
  lapply(url_absolute, "http://www.uefa.com") %>% # make the URLs absolute again, THEN
  unlist() # make one big vector of URLs
  

# How many URLs did we get?
length(player_urls)
```

```
## [1] 552
```

```r
# Let's look at the first 10 URLs
player_urls[1:10]
```

```
##  [1] "http://www.uefa.com/uefaeuro/season=2016/teams/player=250001606/index.html"
##  [2] "http://www.uefa.com/uefaeuro/season=2016/teams/player=24314/index.html"    
##  [3] "http://www.uefa.com/uefaeuro/season=2016/teams/player=103437/index.html"   
##  [4] "http://www.uefa.com/uefaeuro/season=2016/teams/player=62091/index.html"    
##  [5] "http://www.uefa.com/uefaeuro/season=2016/teams/player=250023520/index.html"
##  [6] "http://www.uefa.com/uefaeuro/season=2016/teams/player=250004718/index.html"
##  [7] "http://www.uefa.com/uefaeuro/season=2016/teams/player=54932/index.html"    
##  [8] "http://www.uefa.com/uefaeuro/season=2016/teams/player=250008364/index.html"
##  [9] "http://www.uefa.com/uefaeuro/season=2016/teams/player=250061470/index.html"
## [10] "http://www.uefa.com/uefaeuro/season=2016/teams/player=250016842/index.html"
```

### Explanation
Here we use 3 more functions and combine them with ours to get the URLs of all 552 Players competing in EURO 2016 (24 teams á 23 Players = 552). 

* `url_absolute` makes an absolute URL out of a relative URL and a base URL. So `url_absolute("/uefaeuro/season=2016/teams/player=1901643/index.html", "http://www.uefa.com")` turns the two parameters into one URL ("http://www.uefa.com/uefaeuro/season=2016/teams/player=1901643/index.html"). 

* `unlist` makes one long list out of a list of many lists. so ((1,2,3),(4,5,6),(7,8,9)) becomes (1,2,3,4,5,6,7,8,9)

* `lapply` takes a list of things and applies a function to each item of that list. If the function we apply needs another parameter, we can pass that to lapply too. So `lapply(LIST, FUNCTION, OPTIONAL_PARAMETER)` means take this FUNCTION and apply it to every item on my LIST using PARAMETER as the second parameter (the first one is the list item). Because we use the ` %>% ` operator, the LIST in our case is the output of the function before.

So we visited `"http://www.uefa.com/uefaeuro/"` to get every `"a.group-item"` and got a list of URLs. THEN that list was passed to `lapply`. `url_absolute` was called for every item in our list to get a list of absolute URLs. THEN that list was passed to `lapply` again and this time our function was called for every item on that list, giving us a list of lists (one list for every team URL). That list of lists was passed to `unlist` to make one big list after being passed to lapply again to repeat step 2 with the new URLs.

## Conclusion
Writing functions is really not that hard. In this Tutorial we wrote our own function to extract links from arbitrary URLs. Because our function doesn't care about which URL to visit and which link to extract from there, we can use it to extract links from anywhere. Functions allow us to abstract certain functionality (in this case extracting links from URLs) that we might want tu re-use - which we did. In our example we used our function 25 times. First to get the URLs of the teams and then once for every team URL we got back from the first call. 
So we successfully combined our new function with itself and others to get a list of 552 URLs - one for every player in EURO 2016. 

Write more functions!





