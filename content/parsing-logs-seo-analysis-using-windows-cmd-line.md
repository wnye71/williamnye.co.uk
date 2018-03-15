---
title: "Parsing Logs for SEO Analysis Using Windows CMD Line"
date: "2016-03-02"
categories: ["Technical"]
thumbnail: "thumbs/logs-blog-thumb.jpg"

---

Using log files for SEO analysis is a great way to uncover issues that you may have otherwise missed. This is because, unlike third party spiders, they allow you to see exactly how Googlebot is crawling a site.

If you’re an SEO professional looking to carry out your own log file analysis, then the chances are you’ll have to request the files through your own, or your clients, dev team. However, what you get back can vary widely depending on the servers configuration, and whether or not the developers were willing to extract Googlebot from the raw logs.

Whenever I go through this process at work, my standard request is for around 100k Googlebot requests. On smaller sites, I recommend pulling 2-4 weeks worth of logs; on larger sites, 5-7 days is usually sufficient.

Occasionally, the developers will be nice enough to perform a [GREP to extract Googlebot](https://www.portent.com/blog/seo/get-geeky-grep-seo-tool.htm) from the raw files. Most of the time, however, they’ll just send them over as is:

{{% img src="/img/logs-blog-post.jpg" alt="Server Logs Blog Post" w="600" h="400" %}}

If you end up in this scenario and you’re a Mac user, then you’re in luck — Mac is a [UNIX](https://en.wikipedia.org/wiki/Unix) based operating system so you can perform a GREP using its version of the terminal; if you’re on Windows like myself, then you’re either going to need to use an emulator like [Cygwin](https://www.cygwin.com/), third party software like [Gamut](http://www.gamutsoftware.com/index.php/help/logfileconfiguration), or the Windows CMD line.

Luckily, Windows CMD line does have the functionality to extract text, through a function called [Findstr](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-xp/bb490907(v=technet.10)).

As an initial step, head over to the start menu and perform a search for ‘CMD’. Click it to load up the command line (effectively a text based interface for your operating system). You’ll see something like this:

{{% img src="/img/CMD-Line-FINDSTR.jpg" alt="CMD Line FINDSTR" w="600" h="400" %}}

For ease of use, create a folder in the location that is indicated within the interface (in this instance C:\Users\Will) and place the log files within it. Use the ‘CD’ command to access the folder you created. E.g.

`cd c:\Users\Will\Logs`

Next, you can run the following command:

`findstr “Googlebot” *.log > export.csv`

This will search all of the .log files in the folder and extract all of the instances of Googlebot into one file that you can open in excel. When it’s completed processing you’ll be shown a fresh line in the command prompt; on larger batches this may take some time to complete.

## FAQ

#### Why would you use this over something like Gamut with a graphical user interface?

Mainly because it’s free and actually usually ends up being quicker. Additionally, any graphical interface requires some form of processing power, which can cause it to hang on very large files.

#### What if i’m not using logs?

Although this is primarily aimed at logs, you can use it for any form of text extraction. Ever wanted to merged a load of CSV files when doing link analysis/prospecting? Put them in the same folder and run the following command:

`copy *.csv export.csv`

#### My logs were zipped and have extracted in multiple folders?

Go to the folder where you extracted them all to. Run the same CMD but include /s before “Googlebot”
