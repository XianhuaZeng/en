---
layout: post
title: Creating a SAS Format from a Data Set
date: 2015-05-08 21:01
author: Xianhua.Zeng
comments: true
tags: [CALL EXECUTE, FILENAME, PROC FORMAT]
categories: [Code]
---
SAS Formats are useful to the SAS programmer. They are usually used to map one value into another. We can create a format from a data set.  The most common way to create a format is use <a href="http://support.sas.com/documentation/cdl/en/proc/61895/HTML/default/viewer.htm#a000063536.htm" target="_blank"><span style="text-decoration: underline;">PROC FORMAT</span></a>.  The picture below is an example of a data set with two columns, analysis visit and analysis visit number. <!--more-->
<a href="http://www.xianhuazeng.com/en/wp-content/uploads/2015/05/Format.jpg"><img class="aligncenter size-full wp-image-136" src="http://www.xianhuazeng.com/en/wp-content/uploads/2015/05/Format.jpg" alt="Format" width="506" height="483" /></a> This post will illustrate four different methods to create a format called $visit from this data set.
<ol>
 	<li><a href="http://support.sas.com/documentation/cdl/en/mcrolref/67912/HTML/default/viewer.htm#n1q1527d51eivsn1ob5hnz0yd1hx.htm" target="_blank"><span style="text-decoration: underline;">CALL EXECUTE</span></a>
<pre lang="SAS">data _null_;
    set demo end=eof;
    if _n_=1 then call execute('proc format; value visit');
    call execute(cats(AVISITN)||' = '||quote(cats(AVISIT)));
    if eof then call execute('; run;');
run;
</pre>
</li>
 	<li>Macro variable
<pre lang="SAS">proc sql noprint;
    select catx(' = ', cats(AVISITN), quote(cats(AVISIT))) into :fmtlst separated by ' '
        from demo
        order by AVISITN;
quit;

proc format;
    value visit
    &amp;fmtlst;
run;
</pre>
</li>
 	<li><a href="http://support.sas.com/documentation/cdl/en/proc/65145/HTML/default/viewer.htm#n1e19y6lrektafn1kj6nbvhus59w.htm" target="_blank"><span style="text-decoration: underline;">CNTLIN=</span></a> option
<pre lang="SAS">proc sql;
    create table fmt as
        select distinct 'visit' as FMTNAME
             , AVISITN as START
             , cats(AVISIT) as label
        from demo
        order by AVISITN
        ;
quit;

proc format library=work cntlin=fmt;
run;
</pre>
</li>
 	<li><a href="https://support.sas.com/documentation/cdl/en/lestmtsref/63323/HTML/default/p05r9vhhqbhfzun1qo9mw64s4700.htm" target="_blank"><span style="text-decoration: underline;">FILENAME</span></a>
<pre lang="SAS">proc sql;
    create table fmt as
        select distinct AVISITN
             , quote(cats(AVISIT)) as AVISIT
        from demo
        order by AVISITN
        ;
quit;

/*Write the generated code to a temporary file*/
filename code temp;
data _null_;
    file code;
    set fmt;
    if _n_=1 then put +4 'value visit';
    put +14 AVISITN ' = ' AVISIT;
run;

proc format;
    %inc code / source2;
    ;
run;
</pre>
</li>
</ol>
