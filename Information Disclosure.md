Information Disclosure



Lab: Information disclosure in error messages
 

    With Burp running, open one of the product pages.
    In Burp, go to "Proxy" > "HTTP history" and notice that the GET request for product pages contains a productID parameter. Send the GET /product?productId=1 request to Burp Repeater. Note that your productId might be different depending on which product page you loaded.

    In Burp Repeater, change the value of the productId parameter to a non-integer data type, such as a string. Send the request:
    GET /product?productId="example"
    The unexpected data type causes an exception, and a full stack trace is displayed in the response. This reveals that the lab is using Apache Struts 2 2.3.31.
    Go back to the lab, click "Submit solution", and enter 2 2.3.31 to solve the lab.

Lab: Information disclosure on debug page
 

    With Burp running, browse to the home page.
    Go to the "Target" > "Site Map" tab. Right-click on the top-level entry for the lab and select "Engagement tools" > "Find comments". Notice that the home page contains an HTML comment that contains a link called "Debug". This points to /cgi-bin/phpinfo.php.
    In the site map, right-click on the entry for /cgi-bin/phpinfo.php and select "Send to Repeater".
    In Burp Repeater, send the request to retrieve the file. Notice that it reveals various debugging information, including the SECRET_KEY environment variable.
    Go back to the lab, click "Submit solution", and enter the SECRET_KEY to solve the lab.

Lab: Source code disclosure via backup file
 This lab leaks its source code via backup files in a hidden directory. To solve the lab, identify and submit the database password, which is hard-coded in the leaked source code. 




Lab: Authentication bypass via information disclosure
  This lab's administration interface has an authentication bypass vulnerability, but it is impractical to exploit without knowledge of a custom HTTP header used by the front-end.

  To solve the lab, obtain the header name then use it to bypass the lab's authentication. Access the admin interface and delete Carlos's account. 



    In Burp Repeater, browse to GET /admin. The response discloses that the admin panel is only accessible if logged in as an administrator, or if requested from a local IP.

    Send the request again, but this time use the TRACE method:
    TRACE /admin
    Study the response. Notice that the X-Custom-IP-Authorization header, containing your IP address, was automatically appended to your request. This is used to determine whether or not the request came from the localhost IP address.

    Go to "Proxy" > "Options", scroll down to the "Match and Replace" section, and click "Add". Leave the match condition blank, but in the "Replace" field, enter:
    X-Custom-IP-Authorization: 127.0.0.1

    Burp Proxy will now add this header to every request you send.
    Browse to the home page. Notice that you now have access to the admin panel, where you can delete Carlos.





Lab: Information disclosure in version control history
 This lab discloses sensitive information via its version control history. To solve the lab, obtain the password for the administrator user then log in and delete Carlos's account. 
 

    Open the lab and browse to /.git to reveal the lab's Git version control data.

    Download a copy of this entire directory. For non-Windows users, the easiest way to do this is using the command:
    wget -r https://your-lab-id.web-security-academy.net/.git

    Windows users will need to find an alternative method, or install a UNIX-like environment, such as Cygwin, in order to use this command.
    Explore the downloaded directory using your local Git installation. Notice that there is a commit with the message "Remove admin password from config".
    Look closer at the diff for the changed admin.conf file. Notice that the commit replaced the hard-coded admin password with an environment variable ADMIN_PASSWORD instead. However, the hard-coded password is still clearly visible in the diff.
    Go back to the lab and log in to the administrator account using the leaked password.
    To solve the lab, open the admin interface and delete Carlos's account.



