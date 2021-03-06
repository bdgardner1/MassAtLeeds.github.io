---
layout: postMB
title: Harvesting the Twitter 'firehose' with open source tools
---

Unlike Facebook, Twitter is admirably transparent and open about its data.
Its [Terms of Service](https://twitter.com/tos?PHPSESSID=57a411f70b1964a2bc78b82638ba1843)
state that what you put up there is instantly publicly available and contain the
'tip' that "What you say on Twitter may be viewed all around the world instantly. You are what you Tweet!" 
This allows the company to offer open access methods
for people to tap into the 'live stream' and use the information for 3rd party applications.

This openness and the growing interest in Tweets as a useful
data source about peoples' perceptions of the world has led to a mass of 3rd party
applications to help access this vast stream of data. Many of these are
open source and most take advantage of either the
[Twitter REST API](https://dev.twitter.com/docs/api) (which is
severely [rate limited](https://dev.twitter.com/docs/rate-limiting/1.1)) or the
[Twitter Streaming API](https://dev.twitter.com/docs/api/streaming), 
which allows more information to be taken in, based on either a search term
or a bounding box - a spatial filter.

In this post we demonstrate a few methods of accessing this rich Twitter data.
The majority are open source and many are evolving thanks largely to the social
coding site GitHub. In the final section we demonstrate some interesting potential
applications of harvesting Twitter data, with an illustrated example of Tweets about
road safety.

## Tweepy

The first method we use is [Tweepy](https://github.com/tweepy/tweepy),
a Python library for accessing the Twitter APIs (I'm not sure if
this is can access only the REST or Streaming API, or both).
Installation is easy. Once Python's package manager is installed
(via `sudo apt-get install python-pip` in Debian/Ubuntu systems), 
installation of Tweepy and all its dependencies is a doddle:

```{python}
pip install tweepy
```

Now that it is installed, how does it work?
Well Python must *import* the Tweepy package before it is used.
A critical stage is to find and fill-in your Twitter API
user details into the `streaming.py` example file in the Tweepy
GitHub repository:

```{}
consumer_key=""
consumer_secret=""
access_token=""
access_token_secret=""
```

All four codes must be entered correctly between the quotation "" symbols.
Then the rest of the `streaming.py` file can be altered for your needs
A basic example is provided [here](https://github.com/Robinlovelace/tweepy/blob/master/streaming-leeds.py),
which contains the following line of code to filter the tweets by geographical
location:

```{}
    stream.filter(locations=[-2.17,53.52,-1.20,53.96])
```

This instructs the program to only stream those tweets with are
within the bounding box, which set as the extent of West Yorksire,
in lat/long coordinates. If the file is saved in the working directory
of a Linux terminal, it can be run simply by typing the following:

```{python}
python streaming-leeds.py
```

To see this in action, take a look at the stream of Tweets illustrated
in [this video](http://youtu.be/fqrVFReL7dY).

<iframe width="420" height="315" src="//www.youtube.com/embed/fqrVFReL7dY" frameborder="0" allowfullscreen></iframe>

Not that in the first stream, a mass of unreadable information was provided directly:
this is the '[firehose]', the raw mass of data eminating from Twitter.
The second stream used the following line of code to extract only the user name
and the text for each tweet (see
[here](http://runnable.com/Us9rrMiTWf9bAAW3/how-to-stream-data-from-twitter-with-tweepy-for-python) for the complete script):

```{python}
print '@%s: %s' % (decoded['user']['screen_name'], decoded['text'].encode('ascii', 'ignore'))
```

Now, saving this stream of information from the terminal output is quite simple
in Linux: just type `bash | tee /path/to/logfile` before running the streaming
command and all output will be sent to the logfile in text format.
Remember to type `exit` again in the terminal when you have finished streaming
and the data will be saved. After that, the challenge is to extract useful
information from that mass of raw data.

## Extracting the data into other formats

## Doing it in Java (Dan)

I've had a play with [Twitter4J](http://twitter4j.org) - at the moment by combining it with the [Processing library](http://processing.org/) as I initially thought I'd just be working on visualising. Which I will - but it's become apparent the Java approach might be useful for some other twitter-related things. [David Crowley](http://davidcrowley.me/?p=435)'s chunk of code got me started quickly with the streaming API, with a few little alterations (in particular, supplying the secret API codes via a local class so no-one can hijack my twitter account!)

Twitter's [streaming API](https://dev.twitter.com/docs/streaming-apis/parameters) allows queries that include a location bounding box (picking up on tweets that have included geolocation) - but it can't do an "AND" search for keywords as well:

> "Bounding boxes do not act as filters for other filter parameters. For example track=twitter&locations=-122.75,36.8,-121.75,37.8 would match any tweets containing the term Twitter (even non-geo tweets) OR coming from the San Francisco area."

So here's where I've got to: the stream is supplying geolocated tweets in a Leeds-ish bounding box (-2.17, 53.52, -1.20, 53.96) and I'm doing a test keyword filter once it comes through locally. Testing just with "the "/"The ", [here's a sample screenshot](https://www.dropbox.com/s/sst6nzx9ewjvsez/Screenshot%202014-04-06%2018.38.22.png) of the output (with the keyword highlighted, makes it read a little odd...!)

I've also used the excellent [Unfolding Maps](http://unfoldingmaps.org/) to get tweets' locations appearing live on a map of Leeds - here's a [screenshot of that](https://dl.dropboxusercontent.com/u/306562/pics/unfoldingmapLiveTweets.png).

Here's a few things that might happen next:

* This little chunk of java could be easily extended to harvest data. I'd envisage a config file with options for the bounding box and the keyword/tag search terms, as well as a maximum file size and maximum time to run the harvest. The output could just be a bog standard CSV, not sure if there's any need to keep it in JSON format, though could do that too.
* The pretty/cool visualisation stuff is obviously far more important than any of this data analysis nonsense. So: come up with some options for how to produce a live visualisation.

I haven't yet made it very easy to run the code. There are only a couple of java classes to copy (Main and SimpleStream). You'll also need the [Processing 2](http://processing.org/), [Unfolding Maps](http://unfoldingmaps.org/) and [Twitter4J](http://twitter4j.org/en/index.html) libraries. Finally, there's a hidden class that currently supplies the secret Twitter API keys (I'll let you google how to get set up with the Twitter API). Look in SimpleStream for the methods supplying the secret keys (they're commented with an explanation) - you'll just need to write a simple class to provide those strings. I'll get a .properties file set up soon to do all of this!

## Other options

To install the python-twitter plugin ( http://code.google.com/p/python-twitter/ )
