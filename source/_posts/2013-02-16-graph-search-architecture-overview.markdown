---
layout: post
title: "Graph Search Architecture Overview"
date: 2013-02-16 12:16
comments: true
categories: ["Architecture", "Graph Search"]
---

Back in 2009 me and my colleagues traveled to Moscow to visit Google Developers Day. It has been very first international development conference I've attended and the most excited one. As you may remember these days were Google Wave days. The hype was enormous and I had a chance to chat with Lars Rasmussen, one of the most influential people in the industry, a person who simply makes innovation happen. Well, just check out his [resume][1].

Now [Wave is dead][2], Lars works for [Facebook][3], and they've recently announced [graph search][4], a technology that allows you to search anything shared with you on Facebook. I've been interested in the technical details for a while and lately I've found Lars [answering questions on Reddit][5]. So for those who are lazy to read whole Q/As I've written a quick summary of graph search architecture quoting author as much as possible.

At the back-end they have inverted-index system called *Unicorn* that is used to select nodes in the social graph based on edge-relationships to other nodes. *Unicorn* is akin to document search systems for other contexts like email, webpages, or files on your computer; but it also has some graph-centric features that make it especially useful for searching over the social graph.

All the search requests from users are sent to the index servers. *Hive*, *Hadoop*, and *HBase* are used to convert database contents into an inverted index on a regular basis, and then they push messages to index servers to have realtime updates from the base index.

When a user fires a search request the query gets translated two times. First NLP translator transforms natural language query to a semantic language that describes the interpretation of the natural language, and then further down in the stack they use an *s-expression* syntax to retrieve potential results from an inverted index.

Lars gave the following example. When user types

> My friends who live in Sydney, Australia

it is interpreted to semantical form of

> intersect(friends(me), residents(110884905606108))

and then to s-expression

> (and friend:767560056 city_to_user:110884905606108)

If you have tried graph search you may noticed that you can't use a random query to search stuff, when you start typing system automatically suggests you matched queries and you can fire only suggested ones. How does it work? At the core of the system sits a [Context-Free Grammar][6] describing all the queries the system can understand. The grammar in general contains many different ways of expressing the same question. As a user types in the search field, a [Parser][7] attempts to find the queries from the grammar that most closely matches what the user has typed, and displays those as suggestions in a drop-down below the search field.

As part of the parser they've developed [named entity recognition engine][8]. For example, if you search for photos of *jane doe* the parser will figure out which Jane Doe you are looking for. When in doubt, they tend of course to pick the Jane who has the most friends in common with you.

Graph search is an exciting piece of technology as mush as Wave was, and it definitely pushed industry forward. I hope you've find my notes interesting, and by the way I guess Lars is still answering the questions!


[1]: http://en.wikipedia.org/wiki/Lars_Rasmussen_(software_developer) "Lars Rasmussen"
[2]: http://incubator.apache.org/wave/  "Apache Wave"
[3]: http://facebook.com/ "Facebook"
[4]: https://www.facebook.com/about/graphsearch "Graph Search"
[5]: http://www.reddit.com/r/IAmA/comments/18jb6d/i_am_the_pointyhaired_engineering_director_for/ "Answers on reddit"
[6]: http://en.wikipedia.org/wiki/Context-free_grammar "Context-Free Grammar"
[7]: http://en.wikipedia.org/wiki/Parsing "Parsing"
[8]: http://en.wikipedia.org/wiki/Named-entity_recognition "Named Entity Recognition"