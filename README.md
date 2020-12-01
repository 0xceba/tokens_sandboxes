# Submission of Session Tokens to Sandboxes

## What's happening?

Users are submitting URLs with session tokens to online sandbox services which publicly display the sandbox submission history. The submission histories of the two biggest online sandbox services can be accessed at [https://app.any.run/submissions](https://app.any.run/submissions) and [https://urlscan.io/search/](https://urlscan.io/search/). Attackers could use the session tokens submitted to these services to get no-effort access to authenticated services.

## Who are the victims?

There are submissions of internal phishing from major corporations such as Expedia, Charles Schwab, and Westinghouse. These submissions indicate that their employees likely use these sandboxes to assess hyperlinks that they've received via email.

![Expedia internal phishing](/images/expedia_phishing.jpg)

![Charles Schwab internal phishing](/images/schwab_phishing.jpg)

![Westinghouse internal phishing](/images/westinghouse_phishing.jpg)

## How could attackers take advantage of this?

The submission history of both any.run and urlscan.io contains countless submissions with session tokens, however, urlscan.io allows users to search through the history so it's easier to find session tokens on this service. Some sample urlscan.io queries that will fetch password reset or invitation session tokens follow:
```
page.url:reset AND page.url:password
page.url:register
page.url:signup
page.url:accept
page.url:invitation
page.url:welcome
```

These queries turn up some fun results such as an invitation to create an Amazon Business account for a University of Southern California employee:
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

Many corporations use portal-based email services to ensure confidentiality between two parties. Services like these offer assurance that the communication between the two parties is encrypted, but they are dependent on the users _not mishandling the invitation links_.  We can search for URLs to these services:
```
page.url:securemail
```

Among the results is an invitation URL for an employee of Ross Stores to communicate with Alliance Data:
![Alliance Data Ross Stores](/images/alliance_data_ross.jpg)

Zix is a major provider of these secure email portals so if we search for submissions hosted by Zix’s ASN and a parameter that indicates that they are invitation links:
```
page.asn:AS13380 AND page.url:"&c="
page.asn:AS48077 AND page.url:"&c="
```

The above queries return thousands of publicly-indexed secure email invitation links, many of which are still active like the following to a NASA employee from the Bureau of the Fiscal Service:
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

## Takeaways

* URLs with session token can be just as important as passwords and should be handled accordingly.
* Consider mounting local infrastructure instead of using the easy, third-party solutions that we're all too dependent on.
	* If this is asking too much, at the very least mark the "Private" scan option when submitting samples !