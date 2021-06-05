---
layout: post
title:  Azure Cognitive Services with Image Search and Shop Pricing Comparisons
date:   2016-04-21 12:00:00 +0100
categories: wordpressarchive
tags: Archive Azure CognitiveServices AIandML .NET
excerpt_separator: <!--more-->
---

This week I got the chance for my first attempts with Cognitive services and image search - based on a request from my Global ISV partner:) While the services are actually easy to use for a developer, documentation is behind and Internet Search is highly miss-leading (since it mostly points to a previous Bing-search API that does not exist, anymore). That's why I decided to blog about it and point people into the right direction.

<!--more-->

The Case - Price Comparisons
----------------------------
The use case is simple and has been made available [with our latest Bing-App for the iOS](http://appadvice.com/appnn/2015/12/microsoft-updates-bing-for-iphone-with-in-store-price-comparison-and-more): perform price comparisons of products across multiple shops. But instead of an App, this blog-post is about doing this from within any application with right usage of the new Bing Search APIs in their version 5.0.

Cognitive Services
------------------
At the annual [//build 2016 conference](https://channel9.msdn.com/Events/Build/2016?wt.mc_id=build_hp), Microsoft announced its new [Cognitive services](https://www.microsoft.com/cognitive-services). These are services exposed through simple-to-use APIs for doing all sorts of intelligent stuff based on extensive data and machine learning algorithms. Face recognition, Speech Recognition and the likes are some of the more advanced APIs.

What many people don't know is, that Bing APIs are also part of the Cognitive Services, now. That's because a lot of the intelligence behind Cognitive Services is powered by Bing services incl. their intelligence and machine learning components. So don't let yourself miss-lead by Internet-search results pointing to any kind of previous services.

That means don't search the Internet, just navigate to [Cognitive services](https://www.microsoft.com/cognitive-services) right away and dig into the documentation.

Walking through the Use Case
----------------------------
Ok, let's walk through the Use Case in a schematic way by looking at the APIs and their responses. That will give you a good view on how it actually works. 

1. You need a [Microsoft Account](https://account.microsoft.com/about), so if you don't have one, sign up for one first.

2. If you have a [Microsoft Account](https://account.microsoft.com/about), the first thing you need is signing up for the Cognitive Services Preview. For that purpose just navigate to the [Cognitive Services Subscriptions Page](<https://www.microsoft.com/cognitive-services/en-us/subscriptions>).

3. Once you have signed up for the subscriptions, you get application keys for each of the different types of APIs as shown in my screen-shot below. You need to "Show" and "Copy" the key for Bing Search to implement the case I've described above:
    ![Subscription Keys](/images/posts2016/20160421-figure01.png)
    
4. Once you have copied the subscription key for Bing Search, you can use Bing Image Search to  **look for images products you want to get price comparisons for**.
   - I know, that sounds a bit confusing. But let's assume you want to look for offerings of a sports watch such as Garmin Forerunner 225 (which I am currently interested in:)).
   - To get to shopping offers through Bing or Bing APIs, you'd do an image search for "Garmin Forerunner 225" and then the "new Bing" and "new Bing APIs" will give you that additional details of shopping offers.
   - Let me show you how that works with the API, but note that the same thing works with Bing Search in the browser for end-users.

5. For testing the APIs I use the available Test User Interfaces that you can use for learning the APIs without writing code right away.
   - [API Overview Page](<https://bingapis.portal.azure-api.net/docs/services/56b43f0ccf5ff8098cef3808/operations/56b4433fcf5ff8098cef380c>)
   - [API Testing Console Page](<https://bingapis.portal.azure-api.net/docs/services/56b43f0ccf5ff8098cef3808/operations/56b4433fcf5ff8098cef380c/console>)
   ![The testing Console in Action](/images/posts2016/20160421-figure02.png)

6. Now, let's dig into a few requests. If you want to get offers for e.g. a "Garmin Forerunner" watch, first you need to find images for that watch. 
    
       GET https://bingapis.azure-api.net/api/v5/images/search?q=garmin forerunner 225&count=10&offset=0&mkt=en-us&safeSearch=Moderate HTTP/1.1
       Host: bingapis.azure-api.net
       Ocp-Apim-Subscription-Key: <<your API Key taken from the previous screen shot above>>

7. Next you need to examine the results of the request above. The interesting pieces of the JSON response are the <code>imageInsightsToken</code> and the <code>insightsSourcesSummary</code> elements as highlighted below:
![Insights Details Highlighted](/images/posts2016/20160421-figure03.png)

8. These two attributes are used for the following purposes:
   - <code>imageInsightsToken</code> is used in the next subsequent request to get further details about the image kept and managed by Bing.
   - <code>insightsSourcesSummary</code> is something you can use to asses if it's worth querying further insights for the image. E.g. in my case I wanted to get as many shop-offers for the Garmin as possible. So if I'd write this in a program I would search the top-most search results (first page of JSON elemetns I got from the previous API request) and pick those with the highest <code>shoppingSourcesCount</code> value as a simple strategy.

9. So, let's use the <code>imageInsightsToken</code> to get further details from the image. the following code shows the next request we're executing with the token passed in as an additional parameter. Also note the use of the <code>modulesRequested</code> parameter which I use to specify which kinds of additional details I'd love to get from Bing for that image. That said, there are different modules providing additional information beyond the shopping sources.

       GET https://bingapis.azure-api.net/api/v5/images/search?
         q=garmin forerunner 225&count=10&offset=0
         &mkt=en-us
         &safeSearch=Moderate
         &modulesRequested=shoppingSources
         &insightstoken=ccid_eDfbozgF*mid_7F932B775F08CAD51E0BC3609B3ABA1B7AB73856*simid_608041772346312873 
         HTTP/1.1
       Host: bingapis.azure-api.net
       Ocp-Apim-Subscription-Key: <<your API Key taken from the previous screen shot above>>

10. Finally we get the results we want to have from this request. We see a list of sources which are offering the product for a given price and the basic stock-information which Bing extracted from the web pages of that source for my Garmin Forerunner 225 search.
![Shopping Sources Results](/images/posts2016/20160421-figure04.png)

I really find this kind-of cool. I found it really interesting to work with my ISV partner through that and given that documentation is not always most up2date on those new services I thought it's worth blogging about it:). 

Further resources for reading:
Here are a few helpful links with further details and information. Fortunately from the time I started writing this post until I got it published, the team has updated additional documentation on MSDN!
- [Cognitive Services](https://www.microsoft.com/cognitive-services)
- [Bing Search API v5 Docs](https://msdn.microsoft.com/en-us/library/mt604056.aspx)
- [Bing Image Insights Docs on MSDN](https://msdn.microsoft.com/en-us/library/mt712790.aspx)
- [Bing Image Search Test Console](https://bingapis.portal.azure-api.net/docs/services/56b43f0ccf5ff8098cef3808/operations/56b4433fcf5ff8098cef380c/console)

Let me know your thoughts, best via <http://twitter.com/mszcool>!
