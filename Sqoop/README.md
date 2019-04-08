## Sqoop Hands-on

1. From the accounts table, import only the primary key, along with the first name, last name to
HDFS directory /loudacre/accounts/user_info. Please save the file in text format with tab
delimiters.

<pre>

</pre>

<pre>
sqoop import \
--connect jdbc:mysql://localhost/loudacre \
--username training --password training \
--table accounts \
--target-dir /loudacre/accounts/user_info \
--null-non-string '\\N' \
--columns "acct_num, first_name, last_name" \
--fields-terminated-by "\t"
</pre>

![ex_screenshot](./capture_1.JPG)
