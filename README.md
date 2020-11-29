# Submission of Session Tokens to Sandboxes

Online URL sandbox services, such as [https://app.any.run](https://app.any.run) and [https://urlscan.io](https://urlscan.io), are services that will navigate to a website on behalf of a user without the user interacting with it. The service will capture some technical details about the target website and a screen capture. These services save and publicly display the scan results. There are saved submissions of internal phishing tests which indicate that users at major corporations such as Expedia, Charles Schwab, and Westinghouse have used these services to assess suspicious hyperlinks they received via email:

![Expedia internal phishing](/images/expedia_phishing.jpg)

![Charles Schwab internal phishing](/images/schwab_phishing.jpg)

![Westinghouse internal phishing](/images/westinghouse_phishing.jpg)

It's great to see that users are cautious about navigating to suspicious hyperlinks, however, their good intentions backfire when they submit hyperlinks with sensitive information such as session tokens. Session tokens can be passed between the client and the server via GET parameters, POST data, or cookies. When tokens are sent via GET, the URL takes on the form of `https://website.com/applet.jsp?token=666`. Embedding a session token in a URL served over TLS is a safe way to implement authentication _as long as the URL is not mishandled._

any.run public submissions are accessible at [https://app.any.run/submissions](https://app.any.run/submissions) and urlscan.io public submissions are accessible at [https://urlscan.io/search/](https://urlscan.io/search/). Both services have many public submissions with session tokens, but urlscan.io has a search function so we will focus on it.

To begin we can search for some low-hanging fruit by searching for keywords like “password” and “register” in the indexed URLs:
```
page.url:reset AND page.url:password
page.url:register
page.url:signup
page.url:accept
page.url:invitation
page.url:welcome
```

This turns up some fun results such as an invitation to create an Amazon Business account for a University of Southern California employee:
![Amazon Business USC](/images/amazon_business_usc.jpg)

and an invitation to join Teledyne Technologies’ Atlassian domain:
![Teledyne Atlassian](/images/teledyne_atlassian.jpg)

What we really want are URLs that have been received in an email because those are more likely to include session tokens so we can search for URLs of services that wrap email hyperlinks like Proofpoint’s URL Defense or Microsoft’s Safe Link feature:
```
page.url:"urldefense.proofpoint.com/v2/url?u=http" AND !page.url:"urldefense.proofpoint.com/jblocked"

page.url:safelinks.protection.outlook.com
```

This returns authenticated access to a profile on an employment site named "Higher Income Jobs":
![Higher Income Jobs](/images/higherincomejobs.jpg)

Many corporations use portal-based email services to ensure confidentiality between two parties. Services like these offer assurance that the communication between the two parties is encrypted, but they are dependent on the users not mishandling the invitation links. If users gain access to these links they can access past email conversations and conduct powerful phishing attacks because both parties have authenticated. We can search for access links to these services:
```
page.url:securemail
```

Among the results is an email portal registration page for Alliance Data to communicate with an employee of Ross Stores:
![Alliance Data Ross Stores](/images/alliance_data_ross.jpg)

Zix is a major provider of these secure email portals so if we search for submissions hosted by Zix’s ASN and a parameter that indicates that they are invitation links:
```
page.asn:AS13380 AND page.url:"&c="
page.asn:AS48077 AND page.url:"&c="
```

This returns thousands of publicly-indexed secure email invitation links, many of which are still active like the following to a NASA employee from the Bureau of the Fiscal Service:
![Zix Invite Fiscal Service](/images/fiscal_service_nasa.jpg)

Dropbox invitation URLs have also been submitted and can be collected with the following query:
```
page.asn:AS19679 AND page.url:"l/scl/"
```
![Dropbox US Bank Invitation](/images/dropbox_usbank.jpg)

as well as URLs to public Dropbox files:
```
page.asn:AS19679 AND filename:.pdf
```
![Dropbox Sworn Statement](/images/dropbox_sworn_statement.jpg)

The above queries are a sample of how an attacker could maliciously use these services. The takeaways from this work are that:
* URLs with session token can be just as important as passwords and should be handled accordingly.
* consider mounting local infrastructure instead of using the easy third-party solutions that we've all become too dependent on.
	* if this is asking too much, at the very least mark the "Private" scan option when submitting samples !