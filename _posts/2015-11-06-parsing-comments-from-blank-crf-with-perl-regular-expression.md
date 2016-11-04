---
layout: post
title: Parsing Comments from Blank aCRF with Perl Regular Expression
date: 2015-11-06 15:45
author: Xianhua.Zeng
comments: true
tags: [PRXCHANGE, Regular Expression]
categories: [Code]
---
As clinical SAS programmer, we sometimes need to import and parse annotations contained in a blank aCRF for creating or validating Define.xml. When parsing the imported comments from Blank aCRF, our ultimate goal is to identify the variable. Then we can get the correspondence information, such as CRF page. In this post, I'll introduce a method using Perl <span style="text-decoration: none;"><a href="https://en.wikipedia.org/wiki/Regular_expression" target="_blank">regular expression</a></span>.
Syntax: <a href="http://support.sas.com/documentation/cdl/en/lefunctionsref/63354/HTML/default/n0r8h2fa8djqf1n1cnenrvm573br.htm" target="_blank"><span style="text-decoration: none;">PRXCHANGE</span> </a>(regular-expression-id|perl-regular-expression, times, source). Example:
<pre><code>COMMENTS=prxchange("s/.*?(\b(?:LBCAT|LBTEST|LBTESTCD)\b)?/\1 /o", -1, cats(COMMENTS));</code></pre>
Regular expression visualization by <a href="http://www.regexper.com/" target="_blank"><span style="text-decoration: none;">Regexper</span></a>:
<a href="http://www.xianhuazeng.com/en/wp-content/uploads/2015/11/Visualization.jpg"><img class="aligncenter size-full" src="http://www.xianhuazeng.com/en/wp-content/uploads/2015/11/Visualization.jpg" alt="Visualization" /></a>
Here’s a brief explanation of the regular expression used in the example above. The “.” matches any single character except newline. The “*?” is lazy repetition factor, matches 0 or more occurrences of the preceding character as few times as possible. The first “(“and “)” characters matches a pattern and creates a capture buffer for the match. The last “?” is greedy repetition factor, matches the first capturing group zero or one time as many times as possible. The “\b” matches a word boundary. Since we want to mention “\b” only once, so the second “(“and “)”characters are needed. The “(?:…)” means non-capturing group, the “?:” is not necessary in this example. Since there is no memory required for the second catch (?:), it may work faster. The “\1” matches capture buffer 1.