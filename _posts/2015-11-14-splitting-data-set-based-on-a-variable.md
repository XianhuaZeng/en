---
layout: post
title: Splitting Data Set Based on a Variable
date: 2015-11-14 20:57
author: Xianhua.Zeng
comments: true
tags: [CALL EXECUTE, FILENAME, Hash Object]
categories: [Code]
---
SAS programmers sometimes need to split a data set into multiple data sets, depending on the unique values of a variable. And you can usually achieve what you want by applying a WHERE= option or IF statement. But these aren't the most efficient or elegant method. Suppose that you need to break SASHELP.CLASS into different tables based on the value of SEX, here are three methods I know:<!--more-->
<ol>
 	<li><span style="text-decoration: underline;"><a href="http://support.sas.com/documentation/cdl/en/mcrolref/61885/HTML/default/viewer.htm#a000543697.htm" target="_blank">CALL EXECUTE</a></span>:
<pre lang="SAS">proc sql;
    create table sex as
        select distinct SEX 
    	from sashelp.class
        ;
quit;

data _null_;
    set sex;
    call execute('data sex_'||cats(SEX)||'(where=(SEX='||quote(cats(SEX))||')); set sashelp.class; run;');
run;
</pre>
</li>
 	<li><span style="text-decoration: underline;"><a href="https://support.sas.com/documentation/cdl/en/lrdict/64316/HTML/default/viewer.htm#a000211297.htm" target="_blank">FILENAME</a></span>:
<pre lang="SAS">proc sql;
    create table sex as
        select distinct SEX 
    	from sashelp.class
        ;
quit;

filename code temp;
data _null_;
    file code;
    set sex;
    put ' sex_' SEX '(where=(SEX="' SEX '"))' @@;
run;

data %inc code;;
    set sashelp.class;
run;
</pre>
</li>
 	<li><a href="http://support.sas.com/documentation/cdl/en/lrcon/65287/HTML/default/viewer.htm#n1b4cbtmb049xtn1vh9x4waiioz4.htm" target="_blank"><span style="text-decoration: underline;">HASH</span></a>(SAS9.2+):
<pre lang="SAS">proc sort data=sashelp.class out=class;
	by SEX;
run;

data _null_;
    dcl hash h(multidata:'y');
    h.definekey('SEX');
    h.definedone();
    do until(last.SEX);
        set class;
        by SEX;
        h.add();
    end;
    h.output(dataset:cats('sex_', SEX));
run;
</pre>
</li>
</ol>
Note that the second method is most efficient when splits a huge data set since it reads data set only one time.
