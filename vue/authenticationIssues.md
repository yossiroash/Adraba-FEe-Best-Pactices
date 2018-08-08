## Issue 1

####Issue description (example with OlssonCapital):

User is LOGGED IN on the site wich starts with "www" (www.olssoncapital.com).
BUT when he goes to site without "www" (olssoncapital.com) - he IS NOT LOGGED IN.
(And vice versa)

####Reason of the issue:

Domain with "www" (www.olssoncapital.com) and without "www" (olssoncapital.com) are considered by browser like 2 different domains.
Therefore, when we save AccessToken in the Cookies for domain "www.olssoncapital.com" - them are not accessible from domain "olssoncapital.com". And vice versa.

####Solution (use one of listed bellow, no need to apply both):

A) Settup redirect on server - from "www.olssoncapital.com" to "olssoncapital.com".
In this case as soon as user tries to visit "www.olssoncapital.com" - he will be immediately redirected to "olssoncapital.com".

B) Save Cookies on domain ".olssoncapital.com". (first dot is considered like "wildcard")
In this case cookies are stored on domain ".olssoncapital.com", and can be accessible from the "olssoncapital.com" domain and allso from any of its subdomain ( for example "www.olssoncapital.com", "manager.olssoncapital.com" and other). As result -  AccessToken, which is stored in cookies on ".olssoncapital.com" - is accessible from both - "olssoncapital.com" and "www.olssoncapital.com"

`Use A) option like hight priority (because there is no need to make changes in the source code, perform build and deploy after changes. Just small changes by dev-ops)`
`Use B) option in case - user for any reason dosen't want to settup redirect, which is mentioned in option A)`

####Testing Scenarios:

Go to "olssoncapital.com" and perform LOG IN.
Go to "www.olssoncapital.com" and make sure that:

1) redirect to "olssoncapital.com" is performed (in case "A" solution is applied)

OR

2) you are still LOGGED IN (in case "B" solution is applied)


   ------------------------------------------------------------------------------------------------------------------------------------------

## Issue 2 (relates only to NUXT projects)

####Issue description:

User is LOGGED IN, reloads page a lot of times (for example by pressing F5 key).
As result - user notices, that "user details" are wrong, and belongs to another user (balance, user ID, and another information)

####Reason of the issue:

In case few logged in users are trying to fetch aplication at the same time (by reloading the pages) (for exapmle - in case of highly loaded server) - "nuxtServerInit" function starts to use single Store instance for these few user (in normal case it should use separate unique Store instances per each user). Due to the fact, that we performed AccessToken reading (from cookies inside request) and setting (to Store) inside "nuxtServerInit" function - there were cases, when few different users had the same access token.

####Solution:

Do not perform AccessToken reading (from cookies inside request) and setting (to Store) inside "nuxtServerInit" function.
Move this logic from server-side function "nuxtServerInit" to the client (browser) - for example to "Layout Default file - onCreated function"

####Testing Scenarios:
1) Perform Login using user "A" (any user) credentials.

2) Then open "private window" and Login using user "B" (any user, which is different from "A" user) credentials. (Also you can login user "B" on another computer)

3) Start to reload pages on both the browser windows (under user "A", and under user "B").

4) Make sure, that after reload - users still the same - user "A" receives the relevant information regarding its profile (balance, user ID, and another information). The same check regarding the user "B"

PS - This solution works,but till the end of this week i will try to find another kind of solutions, maybe better one (in order to fix an issue with nuxt server init)
