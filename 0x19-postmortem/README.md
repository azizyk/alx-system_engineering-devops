## 0x19-postmortem

# Postmortem Report
	- 504 Gateway Timeout server error
# Date

Dec 10, 2022 the incident occurred at mid night approximately at 7 :20 AM (EAT) the NGINX web serve went down unexpectedly.

# Timeline and Debugging process
	- At 7:20 am (EAT): - the server was unable to be reached out throwing 504 getaway timeout server error   
	- At 7:24 am (EAT): - we immediately tried to login to the server and checks the Apache server and MySQL data base are both up and running.
	- At 7:28 am (EAT): - The website azizik.tech was not loading properly which on the background check revealed that the server was working properly as well as the database.
	- At 7:33 am (EAT): - We tried to restart the Apache server and returned a status of 200 and was OK while trying to curl the website.
	- At 7:35 am (EAT): - After reviewing error logs to check where the error might be coming from.
	- At 7:41 am (EAT): - Check /var/log to see that the Apache server was being prematurely shut down. We couldn’t find the error.
	- At 7:45 am (EAT): - Checking php.ini settings revealed all error logging had been turned off. Turning the error logging on.
	- At 7:52 am (EAT): - Reviewing error logs for php revealed a mistyped file name which was resulting in incorrect loading and premature closing of Apache.
	- At 7:58 am (EAT): - Fixing file name and restarting Apache server.
	- At 7:65 am (EAT): - Server is now running normally and the website is loading properly.
# Root Cause and Resolution

The problem was caused by the wp-settings.php file referencing the incorrect file name. The server responded with a 504-error code when the user attempted to curl it. Checking the error logs revealed that no error log file was being written for the PHP failures, and reading the normal Apache error log did not reveal much about the server's early shutdown. The engineer decided to review the error log setting for PHP in the php.ini file after realizing that the errors for PHP logs were not being directed anywhere and discovered that all error logging was disabled. After turning on the error logging, the Apache server was restarted to see if the issue persisted.
It is obvious that the site access error was caused by a typographical error. Since the problem was discovered on just one server, it's possible that it also occurred on other servers. The issue might be resolved quickly by using puppet to change the file extension, which would also affect other servers. After a brief deployment of the puppet code, all incorrect file extensions were quickly updated with the correct ones, and the server was restarted. This enabled the site and server to load correctly.
# Correctional and Preventative Actions
	- Error logging should be enabled on all servers and websites to make it simple to find faults when something goes wrong.
	- Before deploying on a multi-server setup, all servers and sites should be tested locally. This will allow issues to be fixed before coming live and reduce the amount of time needed to fix a downed site.

