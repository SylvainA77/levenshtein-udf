# levenshtein-udf
How to make levenshtein function a UDF

1. Sources  
The sources are currently located in the root directory on the machine: levenshteinUDF.c. These are calculation functions that I downloaded and converted into MySQL-compatible libraries. The specs for the overlay can be found here: https://dev.mysql.com/doc/refman/5.6/en/adding-udf.html
Recommendation: Save the sources in an SVN repository. levenshteinSP.sql is only presented so that one can benchmark by himself the performance of compiled UDF.

2.  Libraries  
The aforementioned sources are compiled into libraries using gcc : **gcc -o levenshteinUDF.so -shared levenshteinUDF.c `mysql_config --cflags` -fPIC** . It is crucial not to forget the -shared flag during compilation; otherwise, creating the function in MySQL will not go smoothly.
Recommendation: Place the libraries (the levenshteinUDF.so file in our case) in the /lib directory of the machine and then create a symbolic link of the same name from _/usr/local/mysql/lib/plugin_. This way, a program that needs to call these functions won't have to go through the database.

3.  Functions  
Finally, you need to create the functions in MySQL to use them in queries. Use the following command: _CREATE FUNCTION <function_name_in_library> RETURNS <expected_return_variable_type> SONAME '<library_name_with_extension_but_without_path>';_
Example: In the levenshtein.so library, we have three functions: levenshtein, levenshtein_k, and levenshtein_ratio. The first two return an integer, while the last one, which we are interested in, returns a real number. To create the function in MySQL, use the following command: **CREATE FUNCTION levenshtein_ratio RETURNS REAL SONAME 'levenshteinUDF.so';**
We can now use it like any other function in a query.

Tip: The list of UDFs can be found using this query: _SELECT * FROM mysql.func_.
