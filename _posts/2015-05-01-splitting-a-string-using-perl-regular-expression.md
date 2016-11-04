---
layout: post
title: Splitting a String Using Perl Regular Expression
date: 2015-05-01 20:59
author: Xianhua.Zeng
comments: true
tags: [PRXCHANGE, Regular Expression]
categories: [Code]
---
In SDTM domains, all character variables are limited to a maximum of 200 characters due to FDA requiring datasets in SAS v5 transport format. Text more than 200 characters long should be stored as a record in the SUPP--dataset. To improve readability the text should be split between words not just broken the text into 200-character. In this post, I'll introduce a method using <span style="text-decoration: none;"><a href="https://en.wikipedia.org/wiki/Regular_expression" target="_blank">regular expression</a></span>.
Syntax: <a href="http://support.sas.com/documentation/cdl/en/lefunctionsref/63354/HTML/default/n0r8h2fa8djqf1n1cnenrvm573br.htm" target="_blank"><span style="text-decoration: none;">PRXCHANGE</span> </a>(regular-expression-id|perl-regular-expression, times, source). Example:
<pre><code>VAR=prxchange('s/(.{1,200})([\s]|$)/\1~/', -1, cats(VAR));</code></pre>
Regular expression visualization by <a href="http://www.regexper.com/" target="_blank"><span style="text-decoration: none;">Regexper</span></a>:
<a href="http://www.xianhuazeng.com/en/wp-content/uploads/2015/05/Visualization.jpg"><img class="aligncenter size-full" src="http://www.xianhuazeng.com/en/wp-content/uploads/2015/05/Visualization.jpg" alt="Visualization" /></a>
Here’s a brief explanation. Expression looks at character 201 – if it’s a space, the split character is inserting. Otherwise, it locates the position of the rightmost breaking character and inserts a split character. The process is repeated on the remaining characters in the string until the end of the variable VAR. And finally we can use SCAN function to extract individual words from the variable based on the delimiter (~), and each chunk is assigned to a new variable.