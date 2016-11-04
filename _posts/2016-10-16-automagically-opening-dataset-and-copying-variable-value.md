---
layout: post
title: Automagically Opening Dataset and Copying Variable Value
date: 2016-10-16 17:36
author: Xianhua.Zeng
comments: true
tags: [DM, GSUBMIT, PharmaSUG, PharmaSUG China 2016]
categories: [Code]
---
<p>I attended <span style="text-decoration: none;"><a href="http://www.pharmasug.org/china/index.html" target="_blank">PharmaSUG China 2016</a></span> in Beijing last month, along with my line manager. There were a large number presentations this year. I was very inspired by Hui Liu's presentation (How to give SAS ambiguous instructions and still being a big winner (literally delegate everything to SAS)). He shared some useful tips on presentation, such as automagically opening dataset and copying variable value. The source code is not available, so I created two small macros to accomplish these two common tasks.</p><ol><li>%markdsn, automagically opens the dataset selected.{% highlight sas %}%macro markdsn();
gsubmit "
dm 'wcopy';

filename clip clipbrd;

data _null_;
   infile clip;
   input;
   call execute('dm ""vt '||_INFILE_||';"" continue ;');
run;

filename clip clear;";
%mend markdsn;{% endhighlight %}</li><li>%vvalue, automagically copies variable value.<pre><code>%macro vvalue();
gsubmit '
dm "wcopy";

filename clip clipbrd;

data _null_;
   infile clip;
   input;
   call symputx("var", _INFILE_);
run;

filename clip clear;

proc sql noprint;
    select distinct &amp;var into :varlst separated by "@"
    from &amp;syslast
    ;
quit;

%let increment=%eval(&amp;increment+1);

filename clip clipbrd;

data _null_;
    file clip;
    length value $32767;
	if &amp;increment &lt;= countw("&amp;varlst", "@") then value=scan("&amp;varlst", &amp;increment, "@");
	else value=scan("&amp;varlst", countw("&amp;varlst", "@"), "@");
    put value;
run;

filename clip clear;';
%mend vvalue;</code></pre></li></ol><p>Prerequisites:</p><ol><li>Store the macros in an autocall library</li><li>Define a global macro variable named INCREMENT with initial value 0 in setup program</li><li>In command line type below commands to assign keys to evoke these macros<pre><code>keydef 'F9' '%makedsn'
keydef 'F10' '%vvalue'</code></pre></li></ol><p>Usage:</p><ol><li>Select dataset name and then press F9</li><li>Select variable name and then press F10, repeat the above process until getting the desired value</li></ol>