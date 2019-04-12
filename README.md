# js-history-tracker

Usage
A site simply needs to include a script tag that points to the tracker.js file. e.g.
```
<script src="//wuddrum.github.io/js-history-tracker/tracker.js"></script>
```
Implementation
The implementation relies on two files tracker.js and tracker.html, these should always be next to each other.

Tracker.js: Referenced by a site in a simple script tag. Inserts an invisible iframe element in the site that executes the tracking code. When the iframe is loaded, it passes current window href to the iframe. 
Tracker.html: Executed inside an iframe that's created by tracker.js. The iframe is needed in order for the tracking script to access and store cookies on the tracker domain. The data is stored in the default cookies, so that it can be easily cleared by users.

The tracking script checks if the domain name is already added to the history cookie, if not, then it adds a new history entry with the domain and current datetime in UTC. Currently the cookie expire date is set to one year.

Afterwards the pixel gif is called with domain history from cookies and current url. Currently the tracking pixel url looks like this: //example.com/pixel.gif?data={}&i={}. Where data parameter contains an encoded pixel data JSON object and i parameter is simply current date ticks, to ensure that the tracking pixel is retrieved from your site, instead of user's cache. If you prefer for the data to be used as the pixel filename, then it can be easily changed.

The implementation is compatible with all modern browsers and IE9+. It was working fine even on an old iPhone 3Gs.

Data Structures
The cookie history is saved as an encoded JSON object. This is the object's structure:
```
{
    "domain1.com": "Thu, 11 Apr 2019 15:25:41 GMT",
    "domain2.com": "Thu, 11 Apr 2019 15:25:41 GMT"
}
```
As you can see, it's a simple key-value pair of domain and its datetime, when first loaded. The domain names are not guaranteed to be in order, so you'll need to sort them by dates on the backend to reveal the chronological user's browsing habits.

The pixel data is sent as an encoded JSON object. This is the object's structure:
```
{
    "originHref": "http://domain2.com/item/13/",
    "history": {
        "domain1.com": "Thu, 11 Apr 2019 15:25:41 GMT",
        "domain2.com": "Thu, 11 Apr 2019 15:25:41 GMT"
    }
}
```
Which is simply history data object concatenated with current page url.

Alternative
I think the more logical alternative to all this would be to handle the cookie creation/tracking on the backend.

A site would do a simple tracking pixel request and the backend could inspect the attached cookies (if any) and construct/update new ones that get returned to the client along with the 200 response. The domain/page url could be retrieved from the Referer parameter that's sent by the client.

The big downside of this of course is that the tracking wouldn't be capable of fully working if the Referer header is not sent by a client (site meta property, removed by firewall, proxy etc.). But it might be better if it didn't work in these cases, since it honors the privacy level chosen by site or user.
