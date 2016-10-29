---
layout: post
title: SAS Display Manager Commands
date: 2015-11-08 23:38
author: Xianhua.Zeng
comments: true
tags: [DM, GSUBMIT]
categories: [Code]
---
SAS programmers usually use time-consuming point-and-click methods to accomplish common tasks. For example, when the program completes its run, you need to open a specific dataset to check the desired variable or observation. <span style="font-size: 1rem;">Have you ever wished that these common tasks can be done automatically? <!--more-->Of course, these tasks had to be accomplished almost automagically. </span><span style="text-decoration: underline;"><a style="font-size: 1rem;" href="https://support.sas.com/documentation/cdl/en/lrdict/64316/HTML/default/viewer.htm#a000167815.htm" target="_blank">DM</a></span><span style="font-size: 1rem;"> commands came to your rescue. A DM command stands for Display Manager Statement. It submits SAS Program Editor, Log, Procedure Output or text editor commands as SAS statements. DM command is very powerful but didn’t get much attention of SAS programmers as they should be. In this post, I’ll introduce 2 DM commands you might not have heard of before.</span>
<ol>
 	<li>Usually when you are working with a dataset, you want to see certain columns and not all of them. The following command will show only column A.
<pre lang="SAS">gsub "dm _last_ 'show A;' continue;"
</pre>
</li>
 	<li>Usually when you are working with a huge dataset, you want to retrieve values in a certain observation. The following command will scroll downward 1116 observations.
<pre lang="SAS">gsub "dm _last_ 'forward 1116;' continue;"
</pre>
</li>
</ol>
If you don't want to type commands, here is a hotkey-driven solution. Save the following program(e.g., as /user1/zenga/tool.sas):
<pre lang="SAS">%let line=;
%let name=;
%window Tool irow = 10 rows = 15 icolumn = 10 columns = 90 color=white
#3 @18 'To show the desired variable or desired line.' color=blue
#5 @18 'Enter line number:' color=blue
#7 @18 'Enter variable name:' color=blue
#9 @18 'Note: variable name should be separated by a single space.' color=blue
#5 @37 line 15 attr=underline
#7 @39 name 15 attr=underline;
%display Tool;

%macro tool;
%if &amp;line^= %then %do;
    dm _last_ "top" continue;
    dm _last_ "forward %eval(&amp;line-1)" continue;
%end;
%if &amp;name^= %then %do;
    dm _last_ "show ""&amp;name""" continue;
%end;
%mend tool;

%tool
</pre>
Open one dataset then type below command in command line to assign a VT key to run the code.
<pre lang="SAS">keydef "F9" "gsubmit '%inc ""/user1/zenga/tool.sas"";'" 
</pre>
When you enter the assigned key you will be asked to enter line number or variable name, then you can get desired observation or variable. <a href="http://www.xianhuazeng.com/en/wp-content/uploads/2015/11/Window.jpg"><img class="aligncenter size-full wp-image-589" src="http://www.xianhuazeng.com/en/wp-content/uploads/2015/11/Window.jpg" alt="Window" width="888" height="279" /></a>
