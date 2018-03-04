---
title: "Extracting Search Engine Hits from Log Files"
date: "2018-03-03"
categories: ["Technical"]

---

This page describes some ways to extract search engine hits from a websites log files.

## Extracting Hits from Apache Log Files

To extract just the Googlebot hits on the site using the GNU/Linux terminal, try this:

`grep 'Googlebot\/' access.log > googlebot_access.log`

That will write the Googlebot hits to a new logfile called googlebot_access.log.

You can also pipe that output into another command, for example to extract only the URLs that Googlebot is requesting:

    grep 'Googlebot\/' access.log | cut -d' ' -f7 > googlebot_urls_access.log

(The above line assumes that the URLs are in column 7 of the log file. You might have to adjust it based on your log file format.)

Once you have a file with Google's hits on your site, you can then grep it for specific response codes. For example, to get all the 404s that Googlebot is hitting, you could use the following:

    grep 'Googlebot\/' access.log | grep [[:space:]]404[[:space:]]' > googlebot_404s.txt

You can also grep for different types of headers (302, 301, 500, etc.) which will usually provide other interesting information, especially on a large site.

You can extract all major search engine information and convert it to a CSV file for processing in a spreadsheet. You can open a space-delimited file in a spreadsheet, but converting it to a comma-delimited format will allow you to have blank columns in case you need to remove the dashes in the log file.

## Extracting Hits from IIS Log Files

IIS logs are often configured to output the filename in one column and the query string in the following column. An example of a line from an IIS log is shown below, with a highlighted filename and query string:

`2006-10-19 00:22:41 66.249.65.99 - nnn.nnn.nn.nnn 80 GET /products.aspx item=12345 404 0 Mozilla/5.0+(compatible;+Googlebot/2.1;++http://www.google.com/bot.html) - -`

Unfortunately, the default settings on IIS do not seem to output the actual full URLs requested. It may be useful to get a list of URLs that were accessed by Google in order to process them further.

The following one-liner does the following:

1.  Greps a log file that contains only hits from search engines for 404 errors. This will give a list of every "404 Not Found" page that search engines are visiting.
2.  It then uses the cut command to extract columns 8 and 9 — in this case, the page (/products.aspx) and the query string (item=12345).
3.  Then it uses awk to print out http://www.example.com/filename?querystring.
4.  Because not every requested page has a query string, sed can be used to remove the ?- that will be found on hits that don't have a query string.

\
    ```grep [[:space:]]404[[:space:]] se_access.txt | cut -d' ' -f8,9 | awk '{ print "http://www.example.com"$1"?"$2}' | sed 's/\?-//g' > 404_errors.txt```

The final result is a file named <tt>404_errors.txt</tt> that contains a list of URLs that are being requested on a site by search engines that don't exist. The example above would take the following line from an IIS log:

`2006-10-19 00:22:41 66.249.65.99 - nnn.nnn.nn.nnn 80 GET /products.aspx item=12345 404 0 Mozilla/5.0+(compatible;+Googlebot/2.1;++http://www.google.com/bot.html) - -`

and convert it to the following line:

http://www.example.com/products.aspx?item=12345
