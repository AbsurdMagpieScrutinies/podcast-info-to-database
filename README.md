

# pdodb : record information about podcasts to a database #

This script, poddb, reads either a file or just a few records of data about podcasts, and then writes that information to a simple sqlite3 database.

This program is meant as a working template for other 'write chunks of data to an sqlite3 database' programs. It features functions to update and append to records, to read and parse entire text files and to read individual chunks of data. To use it for another purpose, just change the record parsing function, a few of the internal names, maybe the program's name, and off you go.

Currently it is set up to store information about podcasts. 



