---
layout: post
title: Removing Duplicate Texts in a String
Date: 2016-11-26 15:54
author: Xianhua Zeng
comments: true
tags: [Regular Expression, PRXCHANGE]
categories: [Code]
---
A recent question on SAS-L asked how to remove duplicate texts in a string. Several solutions were offered, each of them solving the problem differently. From my point of view, using traditional functions like SCAN and SUBSTR to solve this question might be confusing and labourious. Here is <span style="text-decoration: none;"><a href="https://en.wikipedia.org/wiki/Regular_expression" target="_blank">regular expression</a></span> solution.
<pre><code>data _null_;
    infile cards truncover;
    input STRING $32767.;
    REX1=prxparse('s/([a-z].+?\.\s+)(.*?)(\1+)/\2\3/i');
    REX2=prxparse('/([a-z].+?\.\s+)(.*?)(\1+)/i');
    do i=1 to 100;
        STRING_=prxchange(REX1, -1, compbl(STRING));
        STRING=STRING_;
        if not prxmatch(REX2, compbl(STRING)) then leave;
    end;
    put STRING=;
cards;
a. The cow jumps over the moon. b. The chicken crossed the road. c. The quick brown fox jumped over the lazy dog. a. The cow jumps over the moon. 
b. The chicken crossed the road. a. The cow jumps over the moon. b. The chicken crossed the road. c. The quick brown fox jumped over the lazy dog.
a. The cow jumps over the moon. a. The cow jumps over the moon. b. The chicken crossed the road. b. The chicken crossed the road. c. The quick brown fox jumped over the lazy dog. c. The quick brown fox jumped over the lazy dog.
a. The cows jump over the moon. a. The cows jump over the moon. b. The chickens crossed the road. b. The chickens crossed the road. c. The quick brown foxes jumped over the lazy dog. c. The quick brown foxes jumped over the lazy dog.
a. The cow jumps over the moon. b. The chicken crossed the road.  c. The quick brown fox jumped over the lazy dog. a. The cow jumps over the moon.  b. The chicken crossed the road. c. The quick brown fox jumped over the lazy dog.
;
run;
</code></pre>
Regular expression visualization by <a href="http://www.regexper.com/" target="_blank"><span style="text-decoration: none;">Regexper</span></a>:
<p><a href="http://www.xianhuazeng.com/en/wp-content/uploads/2016/11/Visualization01.jpg"><img class="aligncenter size-full" src="http://www.xianhuazeng.com/en/wp-content/uploads/2016/11/Visualization01.jpg" alt="Visualization" /></a></p>
Here’s a brief explanation of the regular expression. "[a-z]" matched a single lower case letter. ".+?" matches any characters as few times as possible. "\." matches exactly a period character. "\s+" exactly a space as many times as possible. ".*?" matches any characters as few times as possible. "\1+" matches the first capturing group as many times as possible. 

Note that if the repeated time value is greater than 100, you need to increase the stopping value in DO loop accordingly. I think this scenario rarely happens. If you want to remove duplicate words instead of sentences, you need to adjust the expression. For example:
<pre><code>data _null_;
    STRING='cow chicken fox cow chicken fox cows chickens foxes';
    REX1=prxparse('s/(\b\w+\b)(.*?)(\b\1+\b)/\2\3/i');
    REX2=prxparse('/(\b\w+\b)(.*?)(\b\1+\b)/i');
    do i=1 to 100;
        STRING_=prxchange(REX1, -1, compbl(STRING));
        STRING=STRING_;
        if not prxmatch(REX2, compbl(STRING)) then leave;
    end;
    put STRING=;
run;
</code></pre>
Regular expression visualization by <a href="http://www.regexper.com/" target="_blank"><span style="text-decoration: none;">Regexper</span></a>:
<p><a href="http://www.xianhuazeng.com/en/wp-content/uploads/2016/11/Visualization02.jpg"><img class="aligncenter size-full" src="http://www.xianhuazeng.com/en/wp-content/uploads/2016/11/Visualization02.jpg" alt="Visualization" /></a></p>
"\b" matches a word boundary. "\w+" matches any word character (equal to [a-zA-Z0-9_]) as many times as possible. 