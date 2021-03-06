# THOR-Mozfest
Building citation lists using ORCID records and DOI metadata at Mozfest 2015

## Aim of the session
We're going to try to get to grips with the Public ORCID API & DOI metadata.

### Er, What?
[ORCID](http://orcid.org) is an open, non-profit, community-based effort to provide a registry of unique researcher identifiers.  It's used within scholarly commuication to ensure that one Joe Bloggs doesn't get confused with another Joe Bloggs when publishing research outputs.

If you're an academic researcher chances are that you already have an ID.  If you're not, or you don't, head over to the registry and [create an account](https://orcid.org/register).  It takes 2 minutes.

Need some test data in there?  Well, software is a citable output.  The kind folks at Mozilla Science have worked with github and figshare to capture repositoiry snapshots and assign them DOIs.  [Claim one of your repos now](https://mozillascience.github.io/code-research-object/)!  

### Setup
- Clone this repo
- [Create an ORCID account](https://orcid.org/signin) if we don't have one already
- Get some [API credentials](https://orcid.org/developer-tools)

### Step 1
- Get a list of works for an ORCID ID

### Step 2
- Get the metadata from the DOIs
- Turn the metadata into a reference list
- Decide how to deal with non-DOI works, we can get limited metadata from ORCID
- Take a look at citeproc-js (it's rather large!)

### Step 3
- Configure our API client details.  Go to your ORCID profile, and select "developer tools"
- Authenticate users so we can display their works 
- Have a look at [this JSFiddle](https://fiddle.jshell.net/TomDemeranville/ttmrd4dm/10/)


### Step X
- Look at the search API, just because it's useful.

### Step 4
- Go crazy - work on your use cases
- Make it work for multiple authors - build co-author graphs
- Make it work in the other direction - extract ORCIDs from DOIs
- Add an author search
- Turn the ORCID profile into a widget
- Produce BibTeX
- Work out how to map to RIS or Endnote XML
- Pipe the output into another service or tool
- Use [ORCID-JS](https://github.com/ORCID/orcid-js) to leverage the citeproc-js support
- Use [simple member node js](https://github.com/ORCID/simple-member-api-nodejs) to work with the member API, Enable users to update their profiles, push things into ORCID, use the member API, get sandbox credentials
- ETC!

# Quick intro to the APIs

## The ORCID Public API

The ORCID Public API enables you to do the following things:

- Sign in with ORCID / Get a user's ORCID identifier (these two functions follow the same process)
- Retrieve public data from a user's ORCID record
- Search public ORCID registry data

Using the Public API requires a set of credentials consisting of a Client ID and a Client Secret. You can [configure credentials for the Public API](http://members.orcid.org/api/accessing-public-api) from your personal ORCID account.

There is a great [introduction to using the Public API](http://members.orcid.org/api/introduction-orcid-public-api) in the ORCID API documentation

There are two versions of the API.  v1.2 is in wide use and v2.0 is in beta.

[JSFiddle showing 1.2 fetch works](https://jsfiddle.net/TomDemeranville/vj2Lskje/1/)

[JSFiddle showing 2.0 fetch activites](https://jsfiddle.net/TomDemeranville/wdudooLg/3/)

### Search

The ORCID registry can be searched using the v1.2 API.  It supports the SOLR syntax and fields from the bio portion of the ORCID record can be queried.  All query syntaxes available in SOLR 3.6 are supported, including Lucene (with Solr extensions) which is the default, DisMax and Extended Dismax.

[JSFiddle showing v1.2 search](https://jsfiddle.net/TomDemeranville/doew6d1u/1/)

We can query the ORCID search API by specific field (see the documentation above). For example, we can search for records with a given DOI. Note how we enclose the DOI with escaped quotes in the query - this is so that we get an exact match:

```
https://pub.orcid.org/v1.2/search/orcid-bio/?q=digital-object-ids:\"10.6084/m9.figshare.1373669\
```

[Official ORCID search api documentation](http://members.orcid.org/api/tutorial-searching-api-12-and-earlier)

## Authenticating ORCID users

ORCID uses the OAuth2 protocol for authentication.  This is a user centric authentication mechanism that allows users fine grained control over what third parties can and cannot do with their ORCID record.   It works like this:

- Your website directs the user to ORCID using a specially crafted request containing details of the permissions you would like the user to grant you.  E.g. read or update
- The user authenticates to ORCID, if not already signed in
- The user grants (or denies!) permission to your application
- ORCID redirects the user back to your website with an *authorization code*
- You exchange the authorisation code for an *access token* using the ORCID OAuth API
- You include the access token in any subsequent requests you make

Implementing this in code is easier than it sounds.  

More details can be found in the [ORCID OAuth documentation](https://members.orcid.org/api/oauth2).

[This JSFiddle shows requesting a login](https://fiddle.jshell.net/TomDemeranville/ttmrd4dm/10/)

## The ORCID Member API

The ORCID Member API enables you to do everything the public API can do and in addition:

- Add information to an ORCID record, including:
  - works, 
  - employments
  - education
  - peer reviews
  - bio information, such as alternate names and keywords
- Update information that you previously added
- Delete information that you previously added
- Receive notifications when member records change

There is a great [introduction to using the Member API](http://members.orcid.org/api/introduction-orcid-member-api) in the ORCID API documentation

## DOI metadata API

Crossref and Datacite both support *content negotiation* enabling clients to request DOI metadata in the format most suitable to them.  These formats include Bibtex, Citeproc-JSON, RDF as well as XML and JSON formats specific to Crossref and Datacite.

Crosscite-JSON is a convenient format as it's  usable out of the box by most languages and available from both Crossref and Datacite.  Here is an example, expressed as Crosscite-JSON:

```javascript
{
  "volume" : "169",
  "issue" : "3946",
  "DOI" : "10.1126/science.169.3946.635",
  "URL" : "http://dx.doi.org/10.1126/science.169.3946.635",
  "title" : "The Structure of Ordinary Water: New data and interpretations are 
           yielding new insights into this fascinating substance",
  "container-title" : "Science",
  "publisher" : "American Association for the Advancement of Science AAAS (Science)",
  "issued" : { "date-parts" : [ [ 1970,8,14 ] ] },
  "author" : [ { "family" : "Frank", "given" : "H. S."} ],
  "editor" : [],
  "page" : "635-641",
  "type" : "article-journal"
}
```

### Example fetch of DOI metadata
```Javascript
$.ajax({
    headers: { 
        Accept : "application/vnd.citationstyles.csl+json"
    },
    type: "GET",
    url: "http://dx.doi.org/10.1038/nrd842",
    success: function (data) {
        if (data){
            $("#result").text(JSON.stringify(data,null,'  '));
        }else{
            $("#result").text("no result...");            
        }
    },
    error: function (jqXHR, textStatus, errorThrown )	 {
     	$("#result").text("error: "+textStatus);
    }
});
```
[JSFiddle](http://jsfiddle.net/TomDemeranville/gL4rzL5f/5/)

The [full content negotiation documentation](http://crosscite.org/cn/)

# Top tips

Start a webserver on your local machine using this simple python.  Great for testing
```
python -m SimpleHTTPServer 8000
```

## Other javascript ORCID libraries/implementations

For inspiration!

- (ORCID Researcher Lookup Widget)[http://developers.ands.org.au/widgets/orcid_widget/]
- (ORCID-JS - with citeproc citation support)[https://github.com/ORCID/orcid-js]
- (ORCID Public V2 API swagger)[https://pub.orcid.org/v2.0_rc1]
- (ORCID Member V2 API swagger)[https://api.orcid.org/v2.0_rc1]
- (simple member node js)[https://github.com/ORCID/simple-member-api-nodejs]
- (ORCID API walkthrough using node)[https://github.com/ORCID/orcid-api-walkthrough]