#!/usr/bin/env python
''' reads my podcast notes file and writes the data to a sqlite3 database '''

import argparse
import re
import os
import sqlite3


# --- process the commandline
parser = argparse.ArgumentParser()
parser.add_argument("--infile", type=str,  help="the textfile of info to input")
parser.add_argument("--dbname", type=str, help="the name of the database to use")
parser.add_argument("--queryStr", "-q", type=str, help="find a podcast matching this name")
parser.add_argument("--podname", type=str, help="the specific podcast to deal with")  
parser.add_argument("--append", type=str, help="the blurb to add to the podcast\'s blub")
parser.add_argument("--newblurb", type=str, help="replace a podcast's blurb with this  "
#parser.add_argument("--table", type=str, help="write the infile's info into this table")
args = parser.parse_args()
args.table = "podinfo"

# --- usage msg

usage_msg = '''

read a file of podcast ratings into the database

    poddb     --infile podcast.notes.txt 
              --dbname datastore

update a pdcast\'s blurb

    poddb      --append "more blah blah" 
               --podname "Some Podcast"
               --dbname datastores 

change a podcast\'s blurb

    poddb      --newblurb "more blah blah" 
               --podname "Some Podcast"
               --dbname datastores

show a podcast's info

    poddb     --query | -q \"podcast name substring\" 
              --dbnmae datastore


'''



good_params =  (   (args.infile and args.table and args.dbname) 
                or (args.append and args.table and args.podname and args.dbname)
                or (args.newblurb and args.table and args.podname and args.dbname)
                or (args.queryStr and args.dbname)   )
if (not good_params): 
    print "\nRequired parameters are missing"
    print(usage_msg)
    os._exit(1)




def query_db_for_podcast(query_str):
    connection = sqlite3.connect(args.dbname)
    cursor = connection.cursor()

    sql = "SELECT name, yes_no, commentary FROM {} where name like \"%{}%\" ".format(args.table, query_str)
    cursor.execute(sql)
    
    for record in cursor:
        print record


# --- read over the input file

header_match_pat = r'^- (.*?)\t*?(.*?)'


def process_input_file(infile):
    am_reading_a_line_collection = False

    line_collection = []
    for line in infile:
        # # skip blank lines 
        # if re.search(r'[a-zA-Z]', line) is None:
        #     next
        # header/title line
        if re.search(header_match_pat, line):
            # this is the start of the next input section
            if am_reading_a_line_collection:
            # so process the previous input section
                am_reading_a_line_collection = False;
                process_a_line_collection( line_collection )
                line_collection = []
            # now, start collecting this new, next input section
            am_reading_a_line_collection = True
            line_collection.append(line)
        # text block
        else:
            line_collection.append(line)






# ----- function
def initalize_database(db, table):
    conn = sqlite3.connect(db)
    cursor = conn.cursor()

    # Create table
    sql= "CREATE TABLE IF NOT EXISTS {} (name text, yes_no text, commentary text)".format(table)
    cursor.execute(sql)
    conn.commit()





# ----- function
def update_data_fields_from_hash(database, table, test_column, field_name_and_values):
    ''' updates or inserts a record into an sqlite3 database table

        if the value for the test_col exists, then SET the other field
        if the value for the test_col does NOT exist, then use insert_hash_into_field()

        assumptions: The fieldnames are CORRECT 
    '''

    connection = sqlite3.connect(database)
    cursor = connection.cursor()

    # Q : is there is a value for the test_col? 
    test_val = field_name_and_values[test_column] 
    sqlstr = "SELECT * from {} where {} = {}".format(table,test_column,test_val)
    cursor.execute(sqlstr);
    data_exists = cursor.fetchone()

    # yes : udate the other values
    if data_exists:
        s   = " SET "
        for key, val in field_name_and_values:
            if key == test_column:
                next
            s += " " + key + " = " + val ", "
        setstr = s.rstrip( ,)
        sqlstr = "UPDATE {}  {} WHERE {} = {};".format(table, setstr,test_column,test_val)

        cursor.execute(sqlstr)

    # no : call insert_hash_into_field() 
    else
        insert_hash_into_field(database, table, field_name_and_values)




# ----- function
def append_value_to_field(
         database,
         table,
         recordID_fieldname,
         recordID_fieldval,
         append_to_fieldname,
         val_to_append):

    ''' appends a value to a cell in an sqlite3 database table

        assumptions: The fieldnames are CORRECT 
    ''' 

    connection = sqlite3.connect(database)
    cursor = connection.cursor()

    sqlstr = "UPDATE {} SET {} = {} + {} WHERE {} = {}; ".
        format(table, fieldname, fieldname, val_to_append, recordID_fieldname, recordID_fieldval)
    cursor.execute(sqlstr)





# ----- function
def insert_hash_into_field(database, table, field_name_and_values):
    ''' assumptions: The fieldnames are CORRECT '''

    connection = sqlite3.connect(database)
    cursor = connection.cursor()

    # cur.execute('INSERT INTO table (field_list) VALUES ( value_list ) ")

    key_str = "\", \"".join(field_name_and_values.keys() )
    value_str = "\", \"".join(field_name_and_values.values() )
    sqlstr = "INSERT INTO " + table + " (" + key_str + ") VALUES (" + values_str + ")"

    cursor.execute(sqlstr)


def write_podinfo_to_database(pod_name, rating, commentary_str):
    ''' insert the passed data to a database '''

    connection = sqlite3.connect(args.dbname)
    cursor = connection.cursor()

    # --- new record or updating?

    new_update = "banana"
    sqlstr = "SELECT name, yes_no, commentary from {} where name = \"{}\" ".format(args.table, pod_name); 
    cursor.execute(sqlstr)
    existing_record = [row[0] for row in cursor]
    if pod_name == existing_record[0]:
        if commentary_str != existing_record[2]
            new_update="append"
        else
            new_update="update"
    else
        new_update="new"

    if "new" == new_update:
        field_value_dict
        print "entering: name={}  yes_no={} \ncommentary={}\n\n".format(pod_name, rating, commentary_str)
        sqlstr = '''INSERT INTO {}(name, yes_no, commentary) VALUES(?,?,?)'''.format(args.table), (pod_name, rating, commentary_str)

    elif "update" == new_update:
        sqlstr = "UPDATE {} WHERE name = {} SET yes_no = {} commentary = \"{}\" ".format{args.table, podname, rating, commentary_str)

    elif "append" == new_update:
        sqlstr = "UPDATE {} WHERE name = {} commentary = commentary + \"{}\" ".format{args.table, podname, commentary_str)

    else
        print "\nerror writing a record to the database\n\n"
        os._exit(1); 

    cursor.execute(sqlstr)
    connection.commit()







def process_a_line_collection(lc):
    ''' extract the data from an input block (line collection)
    call a function to send these data items to a database
    '''

    # skip any final or initial blank lines
    index = -1
    while not re.search(r'[a-zA-Z]',lc[index]):
        lc.pop()
    index = 0
    while not re.search(r'[a-zA-Z]',lc[index]):
        index+=1
    # get the header line
    if not re.search(header_match_pat, lc[index]):
        print "\nWeird corruption finding a header line\n"
        os._exit(1)
    # parse the header line
    parts = re.search(r"^- (.+)\t+(.+)", lc[index])
    podcast_name = parts.group(1)
    podcast_yes_no = parts.group(2)
    #skip any interveining blank lines
    index+=1
    while not re.search(r'[a-zA-Z]',lc[index]):
        index+=1
    # collect the remaining lines
    if  len( lc[index:] )  == 1:
        commentary_lines = lc[index]
    else: 
        commentary_lines = "\n".join(lc[index:])

    # send the collected info to the database
    write_podinfo_to_database(podcast_name, podcast_yes_no, commentary_lines)







if __name__ ==  "__main__":

    
    # --- query the podcast list
    if (args.queryStr):
        query_db_for_podcast(args.queryStr)
        os._exit(0)

    # --- add a text file to the database
    if args.infile:
        initalize_database(args.dbname,args.table)
        insert_textfile_into_database(args.dbname, args.infile)

        # prepare the infile
        infile = open(args.infile, "r")
        if  (not infile.name):
            print "\nThe --infile file {} cannot be opened\n".format(args.infile)
            print usage_msg
            os._exit(1)

        # process the infile
        process_input_file(infile)



