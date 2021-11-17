
NOM : RASOLOARIVONY Elysé
Lab : Hive


# HIVE Beeline Client

* connect to HADOOP cluster using SSH
> Password : XXXXXXX

### Connecter sur client Hive 
* beeline
> [elyse.rasoloarivony@hadoop-edge01 ~]$ beeline

NOM : RASOLOARIVONY Elysé
Prénom : Elysé
Groupe : M1 apprentissage Big data
Id : 20211003
LAB YARN 1



# Running the wordcount example 

#### List of samples 
>  yarn jar/usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

```
An example program must be given as the first argument.
Valid program names are:
  aggregatewordcount: An Aggregate based map/reduce program that counts the words in the input files.
  aggregatewordhist: An Aggregate based map/reduce program that computes the histogram of the words in the input files.
  bbp: A map/reduce program that uses Bailey-Borwein-Plouffe to compute exact digits of Pi.
  dbcount: An example job that count the pageview counts from a database.
  distbbp: A map/reduce program that uses a BBP-type formula to compute exact bits of Pi.
  grep: A map/reduce program that counts the matches of a regex in the input.
  join: A job that effects a join over sorted, equally partitioned datasets
  multifilewc: A job that counts words from several files.
  pentomino: A map/reduce tile laying program to find solutions to pentomino problems.
  pi: A map/reduce program that estimates Pi using a quasi-Monte Carlo method.
  randomtextwriter: A map/reduce program that writes 10GB of random textual data per node.
  randomwriter: A map/reduce program that writes 10GB of random data per node.
  secondarysort: An example defining a secondary sort to the reduce.
  sort: A map/reduce program that sorts the data written by the random writer.
  sudoku: A sudoku solver.
  teragen: Generate data for the terasort
  terasort: Run the terasort
  teravalidate: Checking results of terasort
  wordcount: A map/reduce program that counts the words in the input files.
  wordmean: A map/reduce program that counts the average length of the words in the input files.
  wordmedian: A map/reduce program that counts the median length of the words in the input files.
  wordstandarddeviation: A map/reduce program that counts the standard deviation of the length of the words in the input files.
```

#### help on specific sample
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount
```
Usage: wordcount <in> [<in>...] <out>
```

* count all words in downloaded e-book from Project Gutenberg (pg66464.txt) and store the output in a specific place : *elyse.rasoloarivony/raw/wordcount* (the same place as the input)

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount /user/elyse.rasoloarivony/raw/pg66464.txt /user/elyse.rasoloarivony/wordcount

Remark : the file is named part-r-00000 in the folder wordcount
 
> hdfs dfs -cat /user/elyse.rasoloarivony/raw/wordcount/part-r-00000
```
...
receive 6
received        19
receiving       3
recently        3
recommend       1
recommended     1
recovered,      1
recovery,       1
red     1
redistribute    1
redistributing  1
redistribution. 1
reduced 2
references      2
refreshment     1
...
```

# The Sudoku example 

## create sudoku.dta in local (edge)

> cat > sudoku.dta

* sudoku.dta contents
> cat sudoku.dta
```
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

* put it in the hdfs 
> hdfs dfs -put sodoku.dta


* solve this soduku problem
$ yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \sudoku sudoku.dta
```
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

# Pi example

* estimate pi
Using several maps : here 16 maps, with 10 000 000 samples each to estimate pi!

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \pi 16 10000000

**Estimated value of Pi is 3.14159155000000000000**

# 10 GB GraySort example 
benchmarck (in TB/min) : minimum 100 TB (~173 min in 2009, 0.573TB/min) 

Here, this sample uses just 10 GB of data (MapReduce)

MapReduce used :
* Teragen : generates raw of data to sort
* TeraSort: samples (=> N-1 samples ordered) the imput data and uses MapReduce to sort the data into a total order 
* TeraValidate : validates if output is globally sorted


* Generate 10 GB of data
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teragen -Dmapred.map.tasks=50 100000000 /user/elyse.rasoloarivony/data/10GB-sort-input

*generated file directory : data/10GB-sort-validate/part-r-00000*


* sample and sort the data  
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-output
The partitions are located in /user.elyse.rasoloarivony/data/10GB-sort-output

* Validate the data generate by the sort
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-validate



# MapReduce2 
* We can translate Python code using Jython into a Java jar file
* using Hadoop Streaming API 

# Python MapReduce Code 

# Map step: mapper.py
```
> cat > mapper.py

[elyse.rasoloarivony@hadoop-edge01 ~]$ cat mapper.py
#!/ usr/bin/env python

""" mapper .py """

import sys
""" input comes from STDIN ( standard input ) """
for line in sys . stdin :
    # remove leading and trailing whitespace
    line = line.strip()
    # split the line into words
    words = line.split()
    # increase counters
    for word in words :
        # write the results to STDOUT ( standard output );
        # what we output here will be the input for the
        # Reduce step , i.e. the input for reducer .py
        #
        # tab - delimited ; the trivial word count is 1
        print '%s\t%s' % (word,1)
  
```
# reduce step : reducer.py

> cat > reducer.py
```
[elyse.rasoloarivony@hadoop-edge01 ~]$ cat reducer.py
"""reducer.py"""

from operator import itemgetter
import sys

current_word = None
current_count = 0
word = None

""" input comes from STDIN """
for line in sys.stdin:
    # remove leading and trailing whitespace
    line = line.strip()

    # parse the input we got from mapper.py
    word, count = line.split('\t', 1)

    # convert count (currently a string) to int
    try:
        count = int(count)
    except ValueError:
        # count was not a number, so silently
        # ignore/discard this line
        continue

    # this IF-switch only works because Hadoop sorts map output
    # by key (here: word) before it is passed to the reducer
    if current_word == word:
        current_count += count
    else:
        if current_word:
            # write result to STDOUT
            print '%s\t%s' % (current_word, current_count)
        current_count = count
        current_word = word

""" do not forget to output the last word if needed! """
if current_word == word:
```

# Testing code
> echo "foo foo quux labs foo bar quux" | /home/elyse.rasoloarivony/mapper.py | sort -k1,1 | /home/elyse.rasoloarivony/reducer.py
```
bar     1
foo     3
labs    1
quux    2
```

# Running on Hadoop

* download 3 files txt
> wget https://www.gutenberg.org/cache/epub/20417/pg20417.txt
> wget https://www.gutenberg.org/files/34300/34300-0.txt
> wget https://www.gutenberg.org/files/4300/4300-0.txt

j'ai renommé les fichiers .txt via la commande mv, en :

davinci.txt

outline_of_science.txt

ulysses.txt

* exemples :
> mv 34300-0.txt davinci.txt

* copy davinci.txt to HDFS :
> hdfs dfs -put davinci.txt outline_of_science.txt ulysses.txt gutenberg

# Running MapReduce job
>  yarn jar /usr/odp/1.0.3.0-223/hadoop-mapreduce/hadoop-streaming.jar -file /home/elyse.rasoloarivony/mapper.py -mapper /home/elyse.rasoloarivony/mapper.py -file /home/elyse.rasoloarivony/reducer.py -reducer /home/elyse.rasoloarivony/reducer.py -input /user/elyse.rasoloarivony/gutenberg/* -output /user/elyse.rasoloarivony/gutenberg-output

```
21/10/29 11:50:14 INFO mapreduce.Job:  map 0% reduce 0%
21/10/29 11:50:21 INFO mapreduce.Job:  map 67% reduce 0%
21/10/29 11:50:22 INFO mapreduce.Job:  map 100% reduce 0%
21/10/29 11:50:27 INFO mapreduce.Job:  map 100% reduce 100%
21/10/29 11:50:27 INFO mapreduce.Job: Job job_1630864376208_4282 completed successfully

......
              WRONG_REDUCE=0
        File Input Format Counters
                Bytes Read=3689749
        File Output Format Counters
                Bytes Written=75
```


* checking if it was successful

> hdfs dfs -ls gutenbberg-output

```
hdfs dfs -ls gutenberg-output
Found 2 items
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony          0 2021-10-29 11:50 gutenberg-output/_SUCCESS
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony         75 2021-10-29 11:50 gutenberg-output/part-00000
```



# Advanced mapper 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano mapper.py
```
#!/usr/bin/env python

"""A more advanced Mapper, using Python iterators and generators."""

import sys

def read_input(file):
    for line in file:
        # split the line into words
        yield line.split()

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for words in data:
        # write the results to STDOUT (standard output);
        # what we output here will be the input for the
        # Reduce step, i.e. the input for reducer.py
        #
        # tab-delimited; the trivial word count is 1
        for word in words:
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```


# Advanced reducer 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano reducer.py
```
#!/usr/bin/env python

"""A more advanced Reducer, using Python iterators and generators."""

from itertools import groupby
from operator import itemgetter
import sys

def read_mapper_output(file, separator='\t'):
    for line in file:
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_mapper_output(sys.stdin, separator=separator)
    # groupby groups multiple word-count pairs by word,
    # and creates an iterator that returns consecutive keys and their group:
    #   current_word - string containing a word (the key)
    #   group - iterator yielding all ["&lt;current_word&gt;", "&lt;count&gt;"] items
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            total_count = sum(int(count) for current_word, count in group)
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # count was not a number, so silently discard this item
            pass

if __name__ == "__main__":
    main()
```
....
ng a script to a file
Usage: script <filename>
0: jdbc:hive2://hadoop-master01.efrei.online:> !set                Set a beeline variable
Usage: set <key> <value>
0: jdbc:hive2://hadoop-master01.efrei.online:> !sh                 Execute a shell command
Exception raised from Shell command java.lang.Exception: Failed to execute Execute a shell
. . . . . . . . . . . . . . . . . . . . . . .> !verbose            Set verbose mode on
. . . . . . . . . . . . . . . . . . . . . . .> ;
Error: Error while compiling statement: FAILED: ParseException line 1:16 cannot recognize input near 'Execute' 'a' 'SQL' (state=42000,code=40000)
0: jdbc:hive2://hadoop-master01.efrei.online:>


# la liste des commmande de beeline
$ !help
....0: jdbc:hive2://hadoop-master01.efrei.online:> help
!addlocaldriverjar  Add driver jar file in the beeline client side.
!addlocaldrivername Add driver name that needs to be supported in the beeline
                    client side.
!all                Execute the specified SQL against all the current connections
!autocommit         Set autocommit mode on or off
!batch              Start or execute a batch of statements
!brief              Set verbose mode off
!call               Execute a callable statement
.........
!dbinfo             Give metadata information about the database
!delimiter          Sets the query delimiter, defaults to ;
!describe           Describe a table
!dropall            Drop all tables in the current database
!exportedkeys       List all the exported keys for the specified table
!go                 Select the current connection
!help               Print a summary of command usage
........
!sh                 Execute a shell command
!sql                Execute a SQL command
!tables             List all the tables in the database
!typeinfo           Display the type map for the current connection
!verbose            Set verbose mode on

Comments, bug reports, and patches go to ???
NOM : RASOLOARIVONY Elysé
Prénom : Elysé
Groupe : M1 apprentissage Big data
Id : 20211003
LAB YARN 1



# Running the wordcount example 

#### List of samples 
>  yarn jar/usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

```
An example program must be given as the first argument.
Valid program names are:
  aggregatewordcount: An Aggregate based map/reduce program that counts the words in the input files.
  aggregatewordhist: An Aggregate based map/reduce program that computes the histogram of the words in the input files.
  bbp: A map/reduce program that uses Bailey-Borwein-Plouffe to compute exact digits of Pi.
  dbcount: An example job that count the pageview counts from a database.
  distbbp: A map/reduce program that uses a BBP-type formula to compute exact bits of Pi.
  grep: A map/reduce program that counts the matches of a regex in the input.
  join: A job that effects a join over sorted, equally partitioned datasets
  multifilewc: A job that counts words from several files.
  pentomino: A map/reduce tile laying program to find solutions to pentomino problems.
  pi: A map/reduce program that estimates Pi using a quasi-Monte Carlo method.
  randomtextwriter: A map/reduce program that writes 10GB of random textual data per node.
  randomwriter: A map/reduce program that writes 10GB of random data per node.
  secondarysort: An example defining a secondary sort to the reduce.
  sort: A map/reduce program that sorts the data written by the random writer.
  sudoku: A sudoku solver.
  teragen: Generate data for the terasort
  terasort: Run the terasort
  teravalidate: Checking results of terasort
  wordcount: A map/reduce program that counts the words in the input files.
  wordmean: A map/reduce program that counts the average length of the words in the input files.
  wordmedian: A map/reduce program that counts the median length of the words in the input files.
  wordstandarddeviation: A map/reduce program that counts the standard deviation of the length of the words in the input files.
```

#### help on specific sample
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount
```
Usage: wordcount <in> [<in>...] <out>
```

* count all words in downloaded e-book from Project Gutenberg (pg66464.txt) and store the output in a specific place : *elyse.rasoloarivony/raw/wordcount* (the same place as the input)

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount /user/elyse.rasoloarivony/raw/pg66464.txt /user/elyse.rasoloarivony/wordcount

Remark : the file is named part-r-00000 in the folder wordcount
 
> hdfs dfs -cat /user/elyse.rasoloarivony/raw/wordcount/part-r-00000
```
...
receive 6
received        19
receiving       3
recently        3
recommend       1
recommended     1
recovered,      1
recovery,       1
red     1
redistribute    1
redistributing  1
redistribution. 1
reduced 2
references      2
refreshment     1
...
```

# The Sudoku example 

## create sudoku.dta in local (edge)

> cat > sudoku.dta

* sudoku.dta contents
> cat sudoku.dta
```
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

* put it in the hdfs 
> hdfs dfs -put sodoku.dta


* solve this soduku problem
$ yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \sudoku sudoku.dta
```
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

# Pi example

* estimate pi
Using several maps : here 16 maps, with 10 000 000 samples each to estimate pi!

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \pi 16 10000000

**Estimated value of Pi is 3.14159155000000000000**

# 10 GB GraySort example 
benchmarck (in TB/min) : minimum 100 TB (~173 min in 2009, 0.573TB/min) 

Here, this sample uses just 10 GB of data (MapReduce)

MapReduce used :
* Teragen : generates raw of data to sort
* TeraSort: samples (=> N-1 samples ordered) the imput data and uses MapReduce to sort the data into a total order 
* TeraValidate : validates if output is globally sorted


* Generate 10 GB of data
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teragen -Dmapred.map.tasks=50 100000000 /user/elyse.rasoloarivony/data/10GB-sort-input

*generated file directory : data/10GB-sort-validate/part-r-00000*


* sample and sort the data  
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-output
The partitions are located in /user.elyse.rasoloarivony/data/10GB-sort-output

* Validate the data generate by the sort
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-validate



# MapReduce2 
* We can translate Python code using Jython into a Java jar file
* using Hadoop Streaming API 

# Python MapReduce Code 

# Map step: mapper.py
```
> cat > mapper.py

[elyse.rasoloarivony@hadoop-edge01 ~]$ cat mapper.py
#!/ usr/bin/env python

""" mapper .py """

import sys
""" input comes from STDIN ( standard input ) """
for line in sys . stdin :
    # remove leading and trailing whitespace
    line = line.strip()
    # split the line into words
    words = line.split()
    # increase counters
    for word in words :
        # write the results to STDOUT ( standard output );
        # what we output here will be the input for the
        # Reduce step , i.e. the input for reducer .py
        #
        # tab - delimited ; the trivial word count is 1
        print '%s\t%s' % (word,1)
  
```
# reduce step : reducer.py

> cat > reducer.py
```
[elyse.rasoloarivony@hadoop-edge01 ~]$ cat reducer.py
"""reducer.py"""

from operator import itemgetter
import sys

current_word = None
current_count = 0
word = None

""" input comes from STDIN """
for line in sys.stdin:
    # remove leading and trailing whitespace
    line = line.strip()

    # parse the input we got from mapper.py
    word, count = line.split('\t', 1)

    # convert count (currently a string) to int
    try:
        count = int(count)
    except ValueError:
        # count was not a number, so silently
        # ignore/discard this line
        continue

    # this IF-switch only works because Hadoop sorts map output
    # by key (here: word) before it is passed to the reducer
    if current_word == word:
        current_count += count
    else:
        if current_word:
            # write result to STDOUT
            print '%s\t%s' % (current_word, current_count)
        current_count = count
        current_word = word

""" do not forget to output the last word if needed! """
if current_word == word:
```

# Testing code
> echo "foo foo quux labs foo bar quux" | /home/elyse.rasoloarivony/mapper.py | sort -k1,1 | /home/elyse.rasoloarivony/reducer.py
```
bar     1
foo     3
labs    1
quux    2
```

# Running on Hadoop

* download 3 files txt
> wget https://www.gutenberg.org/cache/epub/20417/pg20417.txt
> wget https://www.gutenberg.org/files/34300/34300-0.txt
> wget https://www.gutenberg.org/files/4300/4300-0.txt

j'ai renommé les fichiers .txt via la commande mv, en :

davinci.txt

outline_of_science.txt

ulysses.txt

* exemples :
> mv 34300-0.txt davinci.txt

* copy davinci.txt to HDFS :
> hdfs dfs -put davinci.txt outline_of_science.txt ulysses.txt gutenberg

# Running MapReduce job
>  yarn jar /usr/odp/1.0.3.0-223/hadoop-mapreduce/hadoop-streaming.jar -file /home/elyse.rasoloarivony/mapper.py -mapper /home/elyse.rasoloarivony/mapper.py -file /home/elyse.rasoloarivony/reducer.py -reducer /home/elyse.rasoloarivony/reducer.py -input /user/elyse.rasoloarivony/gutenberg/* -output /user/elyse.rasoloarivony/gutenberg-output

```
21/10/29 11:50:14 INFO mapreduce.Job:  map 0% reduce 0%
21/10/29 11:50:21 INFO mapreduce.Job:  map 67% reduce 0%
21/10/29 11:50:22 INFO mapreduce.Job:  map 100% reduce 0%
21/10/29 11:50:27 INFO mapreduce.Job:  map 100% reduce 100%
21/10/29 11:50:27 INFO mapreduce.Job: Job job_1630864376208_4282 completed successfully

......
              WRONG_REDUCE=0
        File Input Format Counters
                Bytes Read=3689749
        File Output Format Counters
                Bytes Written=75
```


* checking if it was successful

> hdfs dfs -ls gutenbberg-output

```
hdfs dfs -ls gutenberg-output
Found 2 items
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony          0 2021-10-29 11:50 gutenberg-output/_SUCCESS
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony         75 2021-10-29 11:50 gutenberg-output/part-00000
```



# Advanced mapper 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano mapper.py
```
#!/usr/bin/env python

"""A more advanced Mapper, using Python iterators and generators."""

import sys

def read_input(file):
    for line in file:
        # split the line into words
        yield line.split()

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for words in data:
        # write the results to STDOUT (standard output);
        # what we output here will be the input for the
        # Reduce step, i.e. the input for reducer.py
        #
        # tab-delimited; the trivial word count is 1
        for word in words:
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```


# Advanced reducer 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano reducer.py
```
#!/usr/bin/env python

"""A more advanced Reducer, using Python iterators and generators."""

from itertools import groupby
from operator import itemgetter
import sys

def read_mapper_output(file, separator='\t'):
    for line in file:
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_mapper_output(sys.stdin, separator=separator)
    # groupby groups multiple word-count pairs by word,
    # and creates an iterator that returns consecutive keys and their group:
    #   current_word - string containing a word (the key)
    #   group - iterator yielding all ["&lt;current_word&gt;", "&lt;count&gt;"] items
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            total_count = sum(int(count) for current_word, count in group)
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # count was not a number, so silently discard this item
            pass

if __name__ == "__main__":
    main()
```

* voir qui est connecté en ce moment: (jdbc?)
> !list
  NOM : RASOLOARIVONY Elysé
Prénom : Elysé
Groupe : M1 apprentissage Big data
Id : 20211003
LAB YARN 1



# Running the wordcount example 

#### List of samples 
>  yarn jar/usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

```
An example program must be given as the first argument.
Valid program names are:
  aggregatewordcount: An Aggregate based map/reduce program that counts the words in the input files.
  aggregatewordhist: An Aggregate based map/reduce program that computes the histogram of the words in the input files.
  bbp: A map/reduce program that uses Bailey-Borwein-Plouffe to compute exact digits of Pi.
  dbcount: An example job that count the pageview counts from a database.
  distbbp: A map/reduce program that uses a BBP-type formula to compute exact bits of Pi.
  grep: A map/reduce program that counts the matches of a regex in the input.
  join: A job that effects a join over sorted, equally partitioned datasets
  multifilewc: A job that counts words from several files.
  pentomino: A map/reduce tile laying program to find solutions to pentomino problems.
  pi: A map/reduce program that estimates Pi using a quasi-Monte Carlo method.
  randomtextwriter: A map/reduce program that writes 10GB of random textual data per node.
  randomwriter: A map/reduce program that writes 10GB of random data per node.
  secondarysort: An example defining a secondary sort to the reduce.
  sort: A map/reduce program that sorts the data written by the random writer.
  sudoku: A sudoku solver.
  teragen: Generate data for the terasort
  terasort: Run the terasort
  teravalidate: Checking results of terasort
  wordcount: A map/reduce program that counts the words in the input files.
  wordmean: A map/reduce program that counts the average length of the words in the input files.
  wordmedian: A map/reduce program that counts the median length of the words in the input files.
  wordstandarddeviation: A map/reduce program that counts the standard deviation of the length of the words in the input files.
```

#### help on specific sample
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount
```
Usage: wordcount <in> [<in>...] <out>
```

* count all words in downloaded e-book from Project Gutenberg (pg66464.txt) and store the output in a specific place : *elyse.rasoloarivony/raw/wordcount* (the same place as the input)

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount /user/elyse.rasoloarivony/raw/pg66464.txt /user/elyse.rasoloarivony/wordcount

Remark : the file is named part-r-00000 in the folder wordcount
 
> hdfs dfs -cat /user/elyse.rasoloarivony/raw/wordcount/part-r-00000
```
...
receive 6
received        19
receiving       3
recently        3
recommend       1
recommended     1
recovered,      1
recovery,       1
red     1
redistribute    1
redistributing  1
redistribution. 1
reduced 2
references      2
refreshment     1
...
```

# The Sudoku example 

## create sudoku.dta in local (edge)

> cat > sudoku.dta

* sudoku.dta contents
> cat sudoku.dta
```
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

* put it in the hdfs 
> hdfs dfs -put sodoku.dta


* solve this soduku problem
$ yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \sudoku sudoku.dta
```
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

# Pi example

* estimate pi
Using several maps : here 16 maps, with 10 000 000 samples each to estimate pi!

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \pi 16 10000000

**Estimated value of Pi is 3.14159155000000000000**

# 10 GB GraySort example 
benchmarck (in TB/min) : minimum 100 TB (~173 min in 2009, 0.573TB/min) 

Here, this sample uses just 10 GB of data (MapReduce)

MapReduce used :
* Teragen : generates raw of data to sort
* TeraSort: samples (=> N-1 samples ordered) the imput data and uses MapReduce to sort the data into a total order 
* TeraValidate : validates if output is globally sorted


* Generate 10 GB of data
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teragen -Dmapred.map.tasks=50 100000000 /user/elyse.rasoloarivony/data/10GB-sort-input

*generated file directory : data/10GB-sort-validate/part-r-00000*


* sample and sort the data  
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-output
The partitions are located in /user.elyse.rasoloarivony/data/10GB-sort-output

* Validate the data generate by the sort
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-validate



# MapReduce2 
* We can translate Python code using Jython into a Java jar file
* using Hadoop Streaming API 

# Python MapReduce Code 

# Map step: mapper.py
```
> cat > mapper.py

[elyse.rasoloarivony@hadoop-edge01 ~]$ cat mapper.py
#!/ usr/bin/env python

""" mapper .py """

import sys
""" input comes from STDIN ( standard input ) """
for line in sys . stdin :
    # remove leading and trailing whitespace
    line = line.strip()
    # split the line into words
    words = line.split()
    # increase counters
    for word in words :
        # write the results to STDOUT ( standard output );
        # what we output here will be the input for the
        # Reduce step , i.e. the input for reducer .py
        #
        # tab - delimited ; the trivial word count is 1
        print '%s\t%s' % (word,1)
  
```
# reduce step : reducer.py

> cat > reducer.py
```
[elyse.rasoloarivony@hadoop-edge01 ~]$ cat reducer.py
"""reducer.py"""

from operator import itemgetter
import sys

current_word = None
current_count = 0
word = None

""" input comes from STDIN """
for line in sys.stdin:
    # remove leading and trailing whitespace
    line = line.strip()

    # parse the input we got from mapper.py
    word, count = line.split('\t', 1)

    # convert count (currently a string) to int
    try:
        count = int(count)
    except ValueError:
        # count was not a number, so silently
        # ignore/discard this line
        continue

    # this IF-switch only works because Hadoop sorts map output
    # by key (here: word) before it is passed to the reducer
    if current_word == word:
        current_count += count
    else:
        if current_word:
            # write result to STDOUT
            print '%s\t%s' % (current_word, current_count)
        current_count = count
        current_word = word

""" do not forget to output the last word if needed! """
if current_word == word:
```

# Testing code
> echo "foo foo quux labs foo bar quux" | /home/elyse.rasoloarivony/mapper.py | sort -k1,1 | /home/elyse.rasoloarivony/reducer.py
```
bar     1
foo     3
labs    1
quux    2
```

# Running on Hadoop

* download 3 files txt
> wget https://www.gutenberg.org/cache/epub/20417/pg20417.txt
> wget https://www.gutenberg.org/files/34300/34300-0.txt
> wget https://www.gutenberg.org/files/4300/4300-0.txt

j'ai renommé les fichiers .txt via la commande mv, en :

davinci.txt

outline_of_science.txt

ulysses.txt

* exemples :
> mv 34300-0.txt davinci.txt

* copy davinci.txt to HDFS :
> hdfs dfs -put davinci.txt outline_of_science.txt ulysses.txt gutenberg

# Running MapReduce job
>  yarn jar /usr/odp/1.0.3.0-223/hadoop-mapreduce/hadoop-streaming.jar -file /home/elyse.rasoloarivony/mapper.py -mapper /home/elyse.rasoloarivony/mapper.py -file /home/elyse.rasoloarivony/reducer.py -reducer /home/elyse.rasoloarivony/reducer.py -input /user/elyse.rasoloarivony/gutenberg/* -output /user/elyse.rasoloarivony/gutenberg-output

```
21/10/29 11:50:14 INFO mapreduce.Job:  map 0% reduce 0%
21/10/29 11:50:21 INFO mapreduce.Job:  map 67% reduce 0%
21/10/29 11:50:22 INFO mapreduce.Job:  map 100% reduce 0%
21/10/29 11:50:27 INFO mapreduce.Job:  map 100% reduce 100%
21/10/29 11:50:27 INFO mapreduce.Job: Job job_1630864376208_4282 completed successfully

......
              WRONG_REDUCE=0
        File Input Format Counters
                Bytes Read=3689749
        File Output Format Counters
                Bytes Written=75
```


* checking if it was successful

> hdfs dfs -ls gutenbberg-output

```
hdfs dfs -ls gutenberg-output
Found 2 items
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony          0 2021-10-29 11:50 gutenberg-output/_SUCCESS
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony         75 2021-10-29 11:50 gutenberg-output/part-00000
```



# Advanced mapper 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano mapper.py
```
#!/usr/bin/env python

"""A more advanced Mapper, using Python iterators and generators."""

import sys

def read_input(file):
    for line in file:
        # split the line into words
        yield line.split()

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for words in data:
        # write the results to STDOUT (standard output);
        # what we output here will be the input for the
        # Reduce step, i.e. the input for reducer.py
        #
        # tab-delimited; the trivial word count is 1
        for word in words:
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```


# Advanced reducer 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano reducer.py
```
#!/usr/bin/env python

"""A more advanced Reducer, using Python iterators and generators."""

from itertools import groupby
from operator import itemgetter
import sys

def read_mapper_output(file, separator='\t'):
    for line in file:
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_mapper_output(sys.stdin, separator=separator)
    # groupby groups multiple word-count pairs by word,
    # and creates an iterator that returns consecutive keys and their group:
    #   current_word - string containing a word (the key)
    #   group - iterator yielding all ["&lt;current_word&gt;", "&lt;count&gt;"] items
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            total_count = sum(int(count) for current_word, count in group)
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # count was not a number, so silently discard this item
            pass

if __name__ == "__main__":
    main()
```
0: jdbc:hive2://hadoop-master01.efrei.online:> !list
1 active connection:
 #0  open     jdbc:hive2://hadoop-master01.efrei.online:2181,hadoop-master02.efrei.online:2181,hadoop-master03.efrei.online:2181/default;httpPath=cliservice;principal=hive/_HOST@EFREI.ONLINE;serviceDiscoveryMode=zooKeeper;ssl=true;transportMode=http;zooKeeperNamespace=hiveserver2
NOM : RASOLOARIVONY Elysé
Prénom : Elysé
Groupe : M1 apprentissage Big data
Id : 20211003
LAB YARN 1



# Running the wordcount example 

#### List of samples 
>  yarn jar/usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

```
An example program must be given as the first argument.
Valid program names are:
  aggregatewordcount: An Aggregate based map/reduce program that counts the words in the input files.
  aggregatewordhist: An Aggregate based map/reduce program that computes the histogram of the words in the input files.
  bbp: A map/reduce program that uses Bailey-Borwein-Plouffe to compute exact digits of Pi.
  dbcount: An example job that count the pageview counts from a database.
  distbbp: A map/reduce program that uses a BBP-type formula to compute exact bits of Pi.
  grep: A map/reduce program that counts the matches of a regex in the input.
  join: A job that effects a join over sorted, equally partitioned datasets
  multifilewc: A job that counts words from several files.
  pentomino: A map/reduce tile laying program to find solutions to pentomino problems.
  pi: A map/reduce program that estimates Pi using a quasi-Monte Carlo method.
  randomtextwriter: A map/reduce program that writes 10GB of random textual data per node.
  randomwriter: A map/reduce program that writes 10GB of random data per node.
  secondarysort: An example defining a secondary sort to the reduce.
  sort: A map/reduce program that sorts the data written by the random writer.
  sudoku: A sudoku solver.
  teragen: Generate data for the terasort
  terasort: Run the terasort
  teravalidate: Checking results of terasort
  wordcount: A map/reduce program that counts the words in the input files.
  wordmean: A map/reduce program that counts the average length of the words in the input files.
  wordmedian: A map/reduce program that counts the median length of the words in the input files.
  wordstandarddeviation: A map/reduce program that counts the standard deviation of the length of the words in the input files.
```

#### help on specific sample
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount
```
Usage: wordcount <in> [<in>...] <out>
```

* count all words in downloaded e-book from Project Gutenberg (pg66464.txt) and store the output in a specific place : *elyse.rasoloarivony/raw/wordcount* (the same place as the input)

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount /user/elyse.rasoloarivony/raw/pg66464.txt /user/elyse.rasoloarivony/wordcount

Remark : the file is named part-r-00000 in the folder wordcount
 
> hdfs dfs -cat /user/elyse.rasoloarivony/raw/wordcount/part-r-00000
```
...
receive 6
received        19
receiving       3
recently        3
recommend       1
recommended     1
recovered,      1
recovery,       1
red     1
redistribute    1
redistributing  1
redistribution. 1
reduced 2
references      2
refreshment     1
...
```

# The Sudoku example 

## create sudoku.dta in local (edge)

> cat > sudoku.dta

* sudoku.dta contents
> cat sudoku.dta
```
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

* put it in the hdfs 
> hdfs dfs -put sodoku.dta


* solve this soduku problem
$ yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \sudoku sudoku.dta
```
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

# Pi example

* estimate pi
Using several maps : here 16 maps, with 10 000 000 samples each to estimate pi!

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \pi 16 10000000

**Estimated value of Pi is 3.14159155000000000000**

# 10 GB GraySort example 
benchmarck (in TB/min) : minimum 100 TB (~173 min in 2009, 0.573TB/min) 

Here, this sample uses just 10 GB of data (MapReduce)

MapReduce used :
* Teragen : generates raw of data to sort
* TeraSort: samples (=> N-1 samples ordered) the imput data and uses MapReduce to sort the data into a total order 
* TeraValidate : validates if output is globally sorted


* Generate 10 GB of data
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teragen -Dmapred.map.tasks=50 100000000 /user/elyse.rasoloarivony/data/10GB-sort-input

*generated file directory : data/10GB-sort-validate/part-r-00000*


* sample and sort the data  
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-output
The partitions are located in /user.elyse.rasoloarivony/data/10GB-sort-output

* Validate the data generate by the sort
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-validate



# MapReduce2 
* We can translate Python code using Jython into a Java jar file
* using Hadoop Streaming API 

# Python MapReduce Code 

# Map step: mapper.py
```
> cat > mapper.py

[elyse.rasoloarivony@hadoop-edge01 ~]$ cat mapper.py
#!/ usr/bin/env python

""" mapper .py """

import sys
""" input comes from STDIN ( standard input ) """
for line in sys . stdin :
    # remove leading and trailing whitespace
    line = line.strip()
    # split the line into words
    words = line.split()
    # increase counters
    for word in words :
        # write the results to STDOUT ( standard output );
        # what we output here will be the input for the
        # Reduce step , i.e. the input for reducer .py
        #
        # tab - delimited ; the trivial word count is 1
        print '%s\t%s' % (word,1)
  
```
# reduce step : reducer.py

> cat > reducer.py
```
[elyse.rasoloarivony@hadoop-edge01 ~]$ cat reducer.py
"""reducer.py"""

from operator import itemgetter
import sys

current_word = None
current_count = 0
word = None

""" input comes from STDIN """
for line in sys.stdin:
    # remove leading and trailing whitespace
    line = line.strip()

    # parse the input we got from mapper.py
    word, count = line.split('\t', 1)

    # convert count (currently a string) to int
    try:
        count = int(count)
    except ValueError:
        # count was not a number, so silently
        # ignore/discard this line
        continue

    # this IF-switch only works because Hadoop sorts map output
    # by key (here: word) before it is passed to the reducer
    if current_word == word:
        current_count += count
    else:
        if current_word:
            # write result to STDOUT
            print '%s\t%s' % (current_word, current_count)
        current_count = count
        current_word = word

""" do not forget to output the last word if needed! """
if current_word == word:
```

# Testing code
> echo "foo foo quux labs foo bar quux" | /home/elyse.rasoloarivony/mapper.py | sort -k1,1 | /home/elyse.rasoloarivony/reducer.py
```
bar     1
foo     3
labs    1
quux    2
```

# Running on Hadoop

* download 3 files txt
> wget https://www.gutenberg.org/cache/epub/20417/pg20417.txt
> wget https://www.gutenberg.org/files/34300/34300-0.txt
> wget https://www.gutenberg.org/files/4300/4300-0.txt

j'ai renommé les fichiers .txt via la commande mv, en :

davinci.txt

outline_of_science.txt

ulysses.txt

* exemples :
> mv 34300-0.txt davinci.txt

* copy davinci.txt to HDFS :
> hdfs dfs -put davinci.txt outline_of_science.txt ulysses.txt gutenberg

# Running MapReduce job
>  yarn jar /usr/odp/1.0.3.0-223/hadoop-mapreduce/hadoop-streaming.jar -file /home/elyse.rasoloarivony/mapper.py -mapper /home/elyse.rasoloarivony/mapper.py -file /home/elyse.rasoloarivony/reducer.py -reducer /home/elyse.rasoloarivony/reducer.py -input /user/elyse.rasoloarivony/gutenberg/* -output /user/elyse.rasoloarivony/gutenberg-output

```
21/10/29 11:50:14 INFO mapreduce.Job:  map 0% reduce 0%
21/10/29 11:50:21 INFO mapreduce.Job:  map 67% reduce 0%
21/10/29 11:50:22 INFO mapreduce.Job:  map 100% reduce 0%
21/10/29 11:50:27 INFO mapreduce.Job:  map 100% reduce 100%
21/10/29 11:50:27 INFO mapreduce.Job: Job job_1630864376208_4282 completed successfully

......
              WRONG_REDUCE=0
        File Input Format Counters
                Bytes Read=3689749
        File Output Format Counters
                Bytes Written=75
```


* checking if it was successful

> hdfs dfs -ls gutenbberg-output

```
hdfs dfs -ls gutenberg-output
Found 2 items
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony          0 2021-10-29 11:50 gutenberg-output/_SUCCESS
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony         75 2021-10-29 11:50 gutenberg-output/part-00000
```



# Advanced mapper 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano mapper.py
```
#!/usr/bin/env python

"""A more advanced Mapper, using Python iterators and generators."""

import sys

def read_input(file):
    for line in file:
        # split the line into words
        yield line.split()

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for words in data:
        # write the results to STDOUT (standard output);
        # what we output here will be the input for the
        # Reduce step, i.e. the input for reducer.py
        #
        # tab-delimited; the trivial word count is 1
        for word in words:
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```


# Advanced reducer 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano reducer.py
```
#!/usr/bin/env python

"""A more advanced Reducer, using Python iterators and generators."""

from itertools import groupby
from operator import itemgetter
import sys

def read_mapper_output(file, separator='\t'):
    for line in file:
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_mapper_output(sys.stdin, separator=separator)
    # groupby groups multiple word-count pairs by word,
    # and creates an iterator that returns consecutive keys and their group:
    #   current_word - string containing a word (the key)
    #   group - iterator yielding all ["&lt;current_word&gt;", "&lt;count&gt;"] items
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            total_count = sum(int(count) for current_word, count in group)
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # count was not a number, so silently discard this item
            pass

if __name__ == "__main__":
    main()
```
* Listez les bases de données
> show databases;
  
  NOM : RASOLOARIVONY Elysé
Prénom : Elysé
Groupe : M1 apprentissage Big data
Id : 20211003
LAB YARN 1



# Running the wordcount example 

#### List of samples 
>  yarn jar/usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

```
An example program must be given as the first argument.
Valid program names are:
  aggregatewordcount: An Aggregate based map/reduce program that counts the words in the input files.
  aggregatewordhist: An Aggregate based map/reduce program that computes the histogram of the words in the input files.
  bbp: A map/reduce program that uses Bailey-Borwein-Plouffe to compute exact digits of Pi.
  dbcount: An example job that count the pageview counts from a database.
  distbbp: A map/reduce program that uses a BBP-type formula to compute exact bits of Pi.
  grep: A map/reduce program that counts the matches of a regex in the input.
  join: A job that effects a join over sorted, equally partitioned datasets
  multifilewc: A job that counts words from several files.
  pentomino: A map/reduce tile laying program to find solutions to pentomino problems.
  pi: A map/reduce program that estimates Pi using a quasi-Monte Carlo method.
  randomtextwriter: A map/reduce program that writes 10GB of random textual data per node.
  randomwriter: A map/reduce program that writes 10GB of random data per node.
  secondarysort: An example defining a secondary sort to the reduce.
  sort: A map/reduce program that sorts the data written by the random writer.
  sudoku: A sudoku solver.
  teragen: Generate data for the terasort
  terasort: Run the terasort
  teravalidate: Checking results of terasort
  wordcount: A map/reduce program that counts the words in the input files.
  wordmean: A map/reduce program that counts the average length of the words in the input files.
  wordmedian: A map/reduce program that counts the median length of the words in the input files.
  wordstandarddeviation: A map/reduce program that counts the standard deviation of the length of the words in the input files.
```

#### help on specific sample
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount
```
Usage: wordcount <in> [<in>...] <out>
```

* count all words in downloaded e-book from Project Gutenberg (pg66464.txt) and store the output in a specific place : *elyse.rasoloarivony/raw/wordcount* (the same place as the input)

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount /user/elyse.rasoloarivony/raw/pg66464.txt /user/elyse.rasoloarivony/wordcount

Remark : the file is named part-r-00000 in the folder wordcount
 
> hdfs dfs -cat /user/elyse.rasoloarivony/raw/wordcount/part-r-00000
```
...
receive 6
received        19
receiving       3
recently        3
recommend       1
recommended     1
recovered,      1
recovery,       1
red     1
redistribute    1
redistributing  1
redistribution. 1
reduced 2
references      2
refreshment     1
...
```

# The Sudoku example 

## create sudoku.dta in local (edge)

> cat > sudoku.dta

* sudoku.dta contents
> cat sudoku.dta
```
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

* put it in the hdfs 
> hdfs dfs -put sodoku.dta


* solve this soduku problem
$ yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \sudoku sudoku.dta
```
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

# Pi example

* estimate pi
Using several maps : here 16 maps, with 10 000 000 samples each to estimate pi!

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \pi 16 10000000

**Estimated value of Pi is 3.14159155000000000000**

# 10 GB GraySort example 
benchmarck (in TB/min) : minimum 100 TB (~173 min in 2009, 0.573TB/min) 

Here, this sample uses just 10 GB of data (MapReduce)

MapReduce used :
* Teragen : generates raw of data to sort
* TeraSort: samples (=> N-1 samples ordered) the imput data and uses MapReduce to sort the data into a total order 
* TeraValidate : validates if output is globally sorted


* Generate 10 GB of data
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teragen -Dmapred.map.tasks=50 100000000 /user/elyse.rasoloarivony/data/10GB-sort-input

*generated file directory : data/10GB-sort-validate/part-r-00000*


* sample and sort the data  
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-output
The partitions are located in /user.elyse.rasoloarivony/data/10GB-sort-output

* Validate the data generate by the sort
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-validate



# MapReduce2 
* We can translate Python code using Jython into a Java jar file
* using Hadoop Streaming API 

# Python MapReduce Code 

# Map step: mapper.py
```
> cat > mapper.py

[elyse.rasoloarivony@hadoop-edge01 ~]$ cat mapper.py
#!/ usr/bin/env python

""" mapper .py """

import sys
""" input comes from STDIN ( standard input ) """
for line in sys . stdin :
    # remove leading and trailing whitespace
    line = line.strip()
    # split the line into words
    words = line.split()
    # increase counters
    for word in words :
        # write the results to STDOUT ( standard output );
        # what we output here will be the input for the
        # Reduce step , i.e. the input for reducer .py
        #
        # tab - delimited ; the trivial word count is 1
        print '%s\t%s' % (word,1)
  
```
# reduce step : reducer.py

> cat > reducer.py
```
[elyse.rasoloarivony@hadoop-edge01 ~]$ cat reducer.py
"""reducer.py"""

from operator import itemgetter
import sys

current_word = None
current_count = 0
word = None

""" input comes from STDIN """
for line in sys.stdin:
    # remove leading and trailing whitespace
    line = line.strip()

    # parse the input we got from mapper.py
    word, count = line.split('\t', 1)

    # convert count (currently a string) to int
    try:
        count = int(count)
    except ValueError:
        # count was not a number, so silently
        # ignore/discard this line
        continue

    # this IF-switch only works because Hadoop sorts map output
    # by key (here: word) before it is passed to the reducer
    if current_word == word:
        current_count += count
    else:
        if current_word:
            # write result to STDOUT
            print '%s\t%s' % (current_word, current_count)
        current_count = count
        current_word = word

""" do not forget to output the last word if needed! """
if current_word == word:
```

# Testing code
> echo "foo foo quux labs foo bar quux" | /home/elyse.rasoloarivony/mapper.py | sort -k1,1 | /home/elyse.rasoloarivony/reducer.py
```
bar     1
foo     3
labs    1
quux    2
```

# Running on Hadoop

* download 3 files txt
> wget https://www.gutenberg.org/cache/epub/20417/pg20417.txt
> wget https://www.gutenberg.org/files/34300/34300-0.txt
> wget https://www.gutenberg.org/files/4300/4300-0.txt

j'ai renommé les fichiers .txt via la commande mv, en :

davinci.txt

outline_of_science.txt

ulysses.txt

* exemples :
> mv 34300-0.txt davinci.txt

* copy davinci.txt to HDFS :
> hdfs dfs -put davinci.txt outline_of_science.txt ulysses.txt gutenberg

# Running MapReduce job
>  yarn jar /usr/odp/1.0.3.0-223/hadoop-mapreduce/hadoop-streaming.jar -file /home/elyse.rasoloarivony/mapper.py -mapper /home/elyse.rasoloarivony/mapper.py -file /home/elyse.rasoloarivony/reducer.py -reducer /home/elyse.rasoloarivony/reducer.py -input /user/elyse.rasoloarivony/gutenberg/* -output /user/elyse.rasoloarivony/gutenberg-output

```
21/10/29 11:50:14 INFO mapreduce.Job:  map 0% reduce 0%
21/10/29 11:50:21 INFO mapreduce.Job:  map 67% reduce 0%
21/10/29 11:50:22 INFO mapreduce.Job:  map 100% reduce 0%
21/10/29 11:50:27 INFO mapreduce.Job:  map 100% reduce 100%
21/10/29 11:50:27 INFO mapreduce.Job: Job job_1630864376208_4282 completed successfully

......
              WRONG_REDUCE=0
        File Input Format Counters
                Bytes Read=3689749
        File Output Format Counters
                Bytes Written=75
```


* checking if it was successful

> hdfs dfs -ls gutenbberg-output

```
hdfs dfs -ls gutenberg-output
Found 2 items
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony          0 2021-10-29 11:50 gutenberg-output/_SUCCESS
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony         75 2021-10-29 11:50 gutenberg-output/part-00000
```



# Advanced mapper 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano mapper.py
```
#!/usr/bin/env python

"""A more advanced Mapper, using Python iterators and generators."""

import sys

def read_input(file):
    for line in file:
        # split the line into words
        yield line.split()

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for words in data:
        # write the results to STDOUT (standard output);
        # what we output here will be the input for the
        # Reduce step, i.e. the input for reducer.py
        #
        # tab-delimited; the trivial word count is 1
        for word in words:
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```


# Advanced reducer 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano reducer.py
```
#!/usr/bin/env python

"""A more advanced Reducer, using Python iterators and generators."""

from itertools import groupby
from operator import itemgetter
import sys

def read_mapper_output(file, separator='\t'):
    for line in file:
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_mapper_output(sys.stdin, separator=separator)
    # groupby groups multiple word-count pairs by word,
    # and creates an iterator that returns consecutive keys and their group:
    #   current_word - string containing a word (the key)
    #   group - iterator yielding all ["&lt;current_word&gt;", "&lt;count&gt;"] items
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            total_count = sum(int(count) for current_word, count in group)
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # count was not a number, so silently discard this item
            pass

if __name__ == "__main__":
    main()
```
+-------------------------------+
|         database_name         |
+-------------------------------+
| a_aney                        |
| a_chevron                     |
| a_leblanc                     |
| a_ngau                        |
| a_poloubinski                 |
| a_tonlop                      |
| abdelhadi_hirchi              |
| adrien_tarcy                  |
| adrouineau_areino             |
.....
| cmauvezin                     |
| cvenet                        |
| database_ccarayon             |
| db_ccarayon                   |
| default                       |
| droguet                       |
| e_bernard                     |
| e_diez                        |
| e_guedj                       |
| e_joliman                     |
| e_yimene_kaze                 |
| ebitton                       |
| elyse_rasoloarivony           |
...
| wzouitene                     |
| y_jendoubi                    |
| yang_chen                     |
| yingda                        |
| ymaassouli                    |
| ymeli                         |
+-------------------------------+
213 rows selected (0.224 seconds)
NOM : RASOLOARIVONY Elysé
Prénom : Elysé
Groupe : M1 apprentissage Big data
Id : 20211003
LAB YARN 1



# Running the wordcount example 

#### List of samples 
>  yarn jar/usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

```
An example program must be given as the first argument.
Valid program names are:
  aggregatewordcount: An Aggregate based map/reduce program that counts the words in the input files.
  aggregatewordhist: An Aggregate based map/reduce program that computes the histogram of the words in the input files.
  bbp: A map/reduce program that uses Bailey-Borwein-Plouffe to compute exact digits of Pi.
  dbcount: An example job that count the pageview counts from a database.
  distbbp: A map/reduce program that uses a BBP-type formula to compute exact bits of Pi.
  grep: A map/reduce program that counts the matches of a regex in the input.
  join: A job that effects a join over sorted, equally partitioned datasets
  multifilewc: A job that counts words from several files.
  pentomino: A map/reduce tile laying program to find solutions to pentomino problems.
  pi: A map/reduce program that estimates Pi using a quasi-Monte Carlo method.
  randomtextwriter: A map/reduce program that writes 10GB of random textual data per node.
  randomwriter: A map/reduce program that writes 10GB of random data per node.
  secondarysort: An example defining a secondary sort to the reduce.
  sort: A map/reduce program that sorts the data written by the random writer.
  sudoku: A sudoku solver.
  teragen: Generate data for the terasort
  terasort: Run the terasort
  teravalidate: Checking results of terasort
  wordcount: A map/reduce program that counts the words in the input files.
  wordmean: A map/reduce program that counts the average length of the words in the input files.
  wordmedian: A map/reduce program that counts the median length of the words in the input files.
  wordstandarddeviation: A map/reduce program that counts the standard deviation of the length of the words in the input files.
```

#### help on specific sample
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount
```
Usage: wordcount <in> [<in>...] <out>
```

* count all words in downloaded e-book from Project Gutenberg (pg66464.txt) and store the output in a specific place : *elyse.rasoloarivony/raw/wordcount* (the same place as the input)

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \wordcount /user/elyse.rasoloarivony/raw/pg66464.txt /user/elyse.rasoloarivony/wordcount

Remark : the file is named part-r-00000 in the folder wordcount
 
> hdfs dfs -cat /user/elyse.rasoloarivony/raw/wordcount/part-r-00000
```
...
receive 6
received        19
receiving       3
recently        3
recommend       1
recommended     1
recovered,      1
recovery,       1
red     1
redistribute    1
redistributing  1
redistribution. 1
reduced 2
references      2
refreshment     1
...
```

# The Sudoku example 

## create sudoku.dta in local (edge)

> cat > sudoku.dta

* sudoku.dta contents
> cat sudoku.dta
```
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

* put it in the hdfs 
> hdfs dfs -put sodoku.dta


* solve this soduku problem
$ yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \sudoku sudoku.dta
```
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

# Pi example

* estimate pi
Using several maps : here 16 maps, with 10 000 000 samples each to estimate pi!

> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \pi 16 10000000

**Estimated value of Pi is 3.14159155000000000000**

# 10 GB GraySort example 
benchmarck (in TB/min) : minimum 100 TB (~173 min in 2009, 0.573TB/min) 

Here, this sample uses just 10 GB of data (MapReduce)

MapReduce used :
* Teragen : generates raw of data to sort
* TeraSort: samples (=> N-1 samples ordered) the imput data and uses MapReduce to sort the data into a total order 
* TeraValidate : validates if output is globally sorted


* Generate 10 GB of data
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teragen -Dmapred.map.tasks=50 100000000 /user/elyse.rasoloarivony/data/10GB-sort-input

*generated file directory : data/10GB-sort-validate/part-r-00000*


* sample and sort the data  
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-output
The partitions are located in /user.elyse.rasoloarivony/data/10GB-sort-output

* Validate the data generate by the sort
> yarn jar /usr/odp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar \teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /user/elyse.rasoloarivony/data/10GB-sort-input /user/elyse.rasoloarivony/data/10GB-sort-validate



# MapReduce2 
* We can translate Python code using Jython into a Java jar file
* using Hadoop Streaming API 

# Python MapReduce Code 

# Map step: mapper.py
```
> cat > mapper.py

[elyse.rasoloarivony@hadoop-edge01 ~]$ cat mapper.py
#!/ usr/bin/env python

""" mapper .py """

import sys
""" input comes from STDIN ( standard input ) """
for line in sys . stdin :
    # remove leading and trailing whitespace
    line = line.strip()
    # split the line into words
    words = line.split()
    # increase counters
    for word in words :
        # write the results to STDOUT ( standard output );
        # what we output here will be the input for the
        # Reduce step , i.e. the input for reducer .py
        #
        # tab - delimited ; the trivial word count is 1
        print '%s\t%s' % (word,1)
  
```
# reduce step : reducer.py

> cat > reducer.py
```
[elyse.rasoloarivony@hadoop-edge01 ~]$ cat reducer.py
"""reducer.py"""

from operator import itemgetter
import sys

current_word = None
current_count = 0
word = None

""" input comes from STDIN """
for line in sys.stdin:
    # remove leading and trailing whitespace
    line = line.strip()

    # parse the input we got from mapper.py
    word, count = line.split('\t', 1)

    # convert count (currently a string) to int
    try:
        count = int(count)
    except ValueError:
        # count was not a number, so silently
        # ignore/discard this line
        continue

    # this IF-switch only works because Hadoop sorts map output
    # by key (here: word) before it is passed to the reducer
    if current_word == word:
        current_count += count
    else:
        if current_word:
            # write result to STDOUT
            print '%s\t%s' % (current_word, current_count)
        current_count = count
        current_word = word

""" do not forget to output the last word if needed! """
if current_word == word:
```

# Testing code
> echo "foo foo quux labs foo bar quux" | /home/elyse.rasoloarivony/mapper.py | sort -k1,1 | /home/elyse.rasoloarivony/reducer.py
```
bar     1
foo     3
labs    1
quux    2
```

# Running on Hadoop

* download 3 files txt
> wget https://www.gutenberg.org/cache/epub/20417/pg20417.txt
> wget https://www.gutenberg.org/files/34300/34300-0.txt
> wget https://www.gutenberg.org/files/4300/4300-0.txt

j'ai renommé les fichiers .txt via la commande mv, en :

davinci.txt

outline_of_science.txt

ulysses.txt

* exemples :
> mv 34300-0.txt davinci.txt

* copy davinci.txt to HDFS :
> hdfs dfs -put davinci.txt outline_of_science.txt ulysses.txt gutenberg

# Running MapReduce job
>  yarn jar /usr/odp/1.0.3.0-223/hadoop-mapreduce/hadoop-streaming.jar -file /home/elyse.rasoloarivony/mapper.py -mapper /home/elyse.rasoloarivony/mapper.py -file /home/elyse.rasoloarivony/reducer.py -reducer /home/elyse.rasoloarivony/reducer.py -input /user/elyse.rasoloarivony/gutenberg/* -output /user/elyse.rasoloarivony/gutenberg-output

```
21/10/29 11:50:14 INFO mapreduce.Job:  map 0% reduce 0%
21/10/29 11:50:21 INFO mapreduce.Job:  map 67% reduce 0%
21/10/29 11:50:22 INFO mapreduce.Job:  map 100% reduce 0%
21/10/29 11:50:27 INFO mapreduce.Job:  map 100% reduce 100%
21/10/29 11:50:27 INFO mapreduce.Job: Job job_1630864376208_4282 completed successfully

......
              WRONG_REDUCE=0
        File Input Format Counters
                Bytes Read=3689749
        File Output Format Counters
                Bytes Written=75
```


* checking if it was successful

> hdfs dfs -ls gutenbberg-output

```
hdfs dfs -ls gutenberg-output
Found 2 items
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony          0 2021-10-29 11:50 gutenberg-output/_SUCCESS
-rw-r--r--   3 elyse.rasoloarivony elyse.rasoloarivony         75 2021-10-29 11:50 gutenberg-output/part-00000
```



# Advanced mapper 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano mapper.py
```
#!/usr/bin/env python

"""A more advanced Mapper, using Python iterators and generators."""

import sys

def read_input(file):
    for line in file:
        # split the line into words
        yield line.split()

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for words in data:
        # write the results to STDOUT (standard output);
        # what we output here will be the input for the
        # Reduce step, i.e. the input for reducer.py
        #
        # tab-delimited; the trivial word count is 1
        for word in words:
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```


# Advanced reducer 

> [elyse.rasoloarivony@hadoop-edge01 ~]$ nano reducer.py
```
#!/usr/bin/env python

"""A more advanced Reducer, using Python iterators and generators."""

from itertools import groupby
from operator import itemgetter
import sys

def read_mapper_output(file, separator='\t'):
    for line in file:
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_mapper_output(sys.stdin, separator=separator)
    # groupby groups multiple word-count pairs by word,
    # and creates an iterator that returns consecutive keys and their group:
    #   current_word - string containing a word (the key)
    #   group - iterator yielding all ["&lt;current_word&gt;", "&lt;count&gt;"] items
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            total_count = sum(int(count) for current_word, count in group)
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # count was not a number, so silently discard this item
            pass

if __name__ == "__main__":
    main()
```

* Créer une base de donnée :
> create database elyse_rasoloarivony;

* entrer dans ma base de donnée
> use elyse_rasoloarivony;

*Je suis maintenant dans ma base de donnée personnelle*


* Créer une table, nommons là "temp" :
> create table temp (col string);

```
....
425); Time taken: 0.024 seconds
INFO  : OK
INFO  : Concurrency mode is disabled, not creating a lock manager
+-----------+------------+----------+
| col_name  | data_type  | comment  |
+-----------+------------+----------+
| col       | string     |          |
+-----------+------------+----------+
```

* lister les tables dans ma base de donnée
> show tables;
```  
...
+-----------------+
|    tab_name     |
+-----------------+
| temp            |
+-----------------+
1 row selected (0.21 seconds)

```
## quelques commandes de visualisation d'une table 


* regardez les colonnes de ma table :
> show columns from temp

* voir les propriétés ma table "temp" :
> show tblproperties temp;

* Information sur la table (localisation, ...):
> show table extended  like temp;

```
+----------------------------------------------------+
|                      tab_name                      |
+----------------------------------------------------+
| tableName:temp                         |
| owner:elyse.rasoloarivony                          |
| location:hdfs://efrei/warehouse/tablespace/external/hive/elyse_rasoloariv                                                                                                                                     ony.db/temp |
| inputformat:org.apache.hadoop.mapred.TextInputFormat |
| outputformat:org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat |
| columns:struct columns { string col}               |
| partitioned:false                                  |
| partitionColumns:                                  |
| totalNumberFiles:1                                 |
| totalFileSize:16778                                |
| maxFileSize:16778                                  |
| minFileSize:16778                                  |
| lastAccessTime:1636040207450                       |
| lastUpdateTime:1636040473695                       |
|                                                    |
+----------------------------------------------------+

* Description sur ma table :
$ describe extended temp;

+-----------------------------+----------------------------------------------------+----------+
|          col_name           |                     data_type                      | comment  |
+-----------------------------+----------------------------------------------------+----------+
| col                         | string                                             |          |
|                             | NULL                                               | NULL     |
| Detailed Table Information  | Table(tableName:temp, dbName:istepanian, owner:elyse.rasoloarivony, createTim......................

```



* supprimer la table
> drop table temp;

* vérifier si c'est bien supprimé
> show tables;

```
+-----------------+
|    tab_name     |
+-----------------+
|                 |
+-----------------+
0 rows selected (0.115 seconds)
```
  
* Quitter beeline avec
> !quit



# CREATE TABLES 

* Reconnecter à beeline comme au début et reconnecter à ma base de donnée

* créer une table externe nommée trees_external:

  * créer un dossier dans hdfs via beeline :
  > sh! hdfs dfs -mkdir trees_table

> CREATE EXTERNAL TABLE trees_external(GEOPOINT string, ARRONDISSEMENT int, GENRE string, ESPECE string, FAMILLE string, ANNEEPLANTATION int, HAUTEUR float, CIRCONFERENCE float, ADRESSE string, NOMCOMMUN string, VARIETE string, OBJECTID int, NOM_EV string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ';' LINES TERMINATED BY '\n' STORED AS TEXTFILE LOCATION 'hdfs://efrei/user/elyse.rasoloarivony/trees_table' TBLPROPERTIES ("skip.header.line.count"="1");

J'ai ajouté TBLPROPERTIES ("skip.header.line.count"="1") à la requête CREATE TABLE pour garantir que l'en-tête  csv (les noms de colonnes) sera ignoré lors du changement des données. Nous pouvons maintenant charger les données dans notre table externe en utilisant LOAD DATA

ou

>  CREATE EXTERNAL TABLE trees_external (GEOPOINT STRING, ARRONDISSEMENT INT, GENRE STRING, ESPECE STRING, FAMILLE STRING, ANNEE_PLANTATION DATE, HAUTEUR FLOAT, CIRCONFERENCE FLOAT, ADRESSE STRING, NOM_COMMUN STRING, VARIETE STRING, OBJECTID INT, NOM_EV STRING) ROW FORMAT DELIMITED FIELDS TERMINATED BY ';' LINES TERMINATED BY '\n' LOCATION '/user/elyse.rasoloarivony/trees_table';




* Vérifions bien si la table est bien créee

> show tables;
```
...
INFO  : Concurrency mode is disabled, not creating a lock manager
+-----------------+
|    tab_name     |
+-----------------+
| trees_external  |
+-----------------+
```




> describe extended trees_table;

```
+-----------------------------+----------------------------------------------------+----------+
|          col_name           |                     data_type                      | comment  |
+-----------------------------+----------------------------------------------------+----------+
| geopoint                    | string                                             |          |
| arrondissement              | int                                                |          |
| genre                       | string                                             |          |
| espece                      | string                                             |          |
| famille                     | string                                             |          |
| anneeplantation             | int                                                |          |
| hauteur                     | float                                              |          |
| circonference               | float                                              |          |
| adresse                     | string                                             |          |
| nomcommun                   | string                                             |          |
| variete                     | string                                             |          |
| objectid                    | int                                                |          |
| nom_ev                      | string                                             |          |
|                             | NULL                                               | NULL     |
```






* Créer une table interne:

> CREATE TABLE trees_internal (GEOPOINT STRING, ARRONDISSEMENT INT, GENRE STRING, ESPECE STRING, FAMILLE STRING, ANNEE_PLANTATION DATE, HAUTEUR FLOAT, CIRCONFERENCE FLOAT, ADRESSE STRING, NOM_COMMUN STRING, VARIETE STRING, OBJECTID INT, NOM_EV STRING);
```
INFO  : Starting task [Stage-0:DDL] in serial mode
INFO  : Completed executing command(queryId=hive_20211110125835_4d1df3c3-3f6b-4da0-b962-67cd891bf6bc); Time taken: 0.089 seconds
INFO  : OK
INFO  : Concurrency mode is disabled, not creating a lock manager
No rows affected (0.135 seconds)


0: jdbc:hive2://hadoop-master01.efrei.online:> show tables;
...
INFO  : Executing command(queryId=hive_20211110125841_8c52755e-69cd-4869-b4a9-4a65320d8342): show tables
INFO  : Starting task [Stage-0:DDL] in serial mode
INFO  : Completed executing command(queryId=hive_20211110125841_8c52755e-69cd-4869-b4a9-4a65320d8342); Time taken: 0.017 seconds
INFO  : OK
INFO  : Concurrency mode is disabled, not creating a lock manager
+-----------------+
|    tab_name     |
+-----------------+
| trees_external  |
| trees_internal  |
+-----------------+
2 rows selected (0.128 seconds)
```


* Importation des données dans la table interne en utilisant celle d'externe

> INSERT OVERWRITE TABLE trees_internal SELECT * FROM trees_external;



* Vérification si chaque table a  les mêmes lignes
> SELECT * FROM trees_external;
  
> SELECT * FROM trees_internal;


*Avec ces commandes on peut vérifier si les 2 tables contiennent bient le même nombre de ligne*

```
....
INFO  : Completed compiling command(queryId=hive_20211104145021_0c648086-c3fb-4c5c-8aab-77a72c15ff81); Time taken: 0.402 seconds
INFO  : Concurrency mode is disabled, not creating a lock manager
INFO  : Executing command(queryId=hive_20211104145021_0c648086-c3fb-4c5c-8aab-77a72c15ff81): select count(*) from trees_internal
INFO  : Completed executing command(queryId=hive_20211104145021_0c648086-c3fb-4c5c-8aab-77a72c15ff81); Time taken: 0.003 seconds
INFO  : OK
INFO  : Concurrency mode is disabled, not creating a lock manager
+------+
| _c0  |
+------+
| 97   |
+------+
```


## CREATION DES REQUETES

*Comme dans MapReduce:*

* La liste des différents district :
> SELECT DISTINCT ARRONDISSEMENT FROM trees_internal;

```
+-----------------+
| arrondissement  |
+-----------------+
| 3               |
| 4               |
| 5               |
| 6               |
| 7               |
| 8               |
| 9               |
| 11              |
| 12              |
| 13              |
| 14              |
| 15              |
| 16              |
| 17              |
| 18              |
| 19              |
| 20              |
+-----------------+
17 rows selected (28.472 seconds)

```



* la liste des différentes espèces

> SELECT ESPECE FROM trees_internal GROUP BY ESPECE;

```
+-----------------+
|     espece      |
+-----------------+
| araucana        |
| atlantica       |
| australis       |
| baccata         |
| bignonioides    |
| biloba          |
| bungeana        |
| cappadocicum    |
| carpinifolia    |
| colurna         |
| coulteri        |
| decurrens       |
| dioicus         |
| distichum       |
| excelsior       |
| fraxinifolia    |
| giganteum       |
| giraldii        |
| glutinosa       |
| grandiflora     |
| hippocastanum   |
| ilex            |
| involucrata     |
| japonicum       |
| kaki            |
| libanii         |
| monspessulanum  |
| nigra           |
| nigra laricio   |
| opalus          |
| orientalis      |
| papyrifera      |
| petraea         |
| pomifera        |
| pseudoacacia    |
| sempervirens    |
| serrata         |
| stenoptera      |
| suber           |
| sylvatica       |
| tomentosa       |
| tulipifera      |
| ulmoides        |
| virginiana      |
| x acerifolia    |
+-----------------+
45 rows selected (27.241 seconds)
```


* La liste de nombre d'arbre par genre:

> SELECT GENRE, COUNT(*) AS NB_TREES FROM trees_internal GROUP BY GENRE;
```
+-----------------+-----------+
|      genre      | nb_trees  |
+-----------------+-----------+
| Acer            | 3         |
| Aesculus        | 3         |
| Ailanthus       | 1         |
| Alnus           | 1         |
| Araucaria       | 1         |
| Broussonetia    | 1         |
| Calocedrus      | 1         |
| Catalpa         | 1         |
| Cedrus          | 4         |
| Celtis          | 1         |
| Corylus         | 3         |
| Davidia         | 1         |
| Diospyros       | 4         |
| Eucommia        | 1         |
| Fagus           | 8         |
| Fraxinus        | 1         |
| Ginkgo          | 5         |
| Gymnocladus     | 1         |
| Juglans         | 1         |
| Liriodendron    | 2         |
| Maclura         | 1         |
| Magnolia        | 1         |
| Paulownia       | 1         |
| Pinus           | 5         |
| Platanus        | 19        |
| Pterocarya      | 3         |
| Quercus         | 4         |
| Robinia         | 1         |
| Sequoia         | 1         |
| Sequoiadendron  | 5         |
| Styphnolobium   | 1         |
| Taxodium        | 3         |
| Taxus           | 2         |
| Tilia           | 1         |
| Ulmus           | 1         |
| Zelkova         | 4         |
+-----------------+-----------+
36 rows selected (22.497 seconds)
```



* La liste des hauteurs des plus grandes arbres par genre

> SELECT GENRE, MAX(HAUTEUR) AS HEIGHT FROM trees_internal GROUP BY GENRE;
```
+-----------------+---------+
|      genre      | height  |
+-----------------+---------+
| Acer            | 16.0    |
| Aesculus        | 30.0    |
| Ailanthus       | 35.0    |
| Alnus           | 16.0    |
| Araucaria       | 9.0     |
| Broussonetia    | 12.0    |
| Calocedrus      | 20.0    |
| Catalpa         | 15.0    |
| Cedrus          | 30.0    |
| Celtis          | 16.0    |
| Corylus         | 20.0    |
| Davidia         | 12.0    |
| Diospyros       | 14.0    |
| Eucommia        | 12.0    |
| Fagus           | 30.0    |
| Fraxinus        | 30.0    |
| Ginkgo          | 33.0    |
| Gymnocladus     | 10.0    |
| Juglans         | 28.0    |
| Liriodendron    | 35.0    |
| Maclura         | 13.0    |
| Magnolia        | 12.0    |
| Paulownia       | 20.0    |
| Pinus           | 30.0    |
| Platanus        | 45.0    |
| Pterocarya      | 30.0    |
| Quercus         | 31.0    |
| Robinia         | 11.0    |
| Sequoia         | 30.0    |
| Sequoiadendron  | 35.0    |
| Styphnolobium   | 10.0    |
| Taxodium        | 35.0    |
| Taxus           | 13.0    |
| Tilia           | 20.0    |
| Ulmus           | 15.0    |
| Zelkova         | 30.0    |
+-----------------+---------+
36 rows selected (25.075 seconds)
```


* Ordonner les arbres de la plus petite vers la plus grande en circonférence:

> SELECT ARRONDISSEMENT,FAMILLE,NOM_COMMUNE,CIRCONFERENCE FROM trees_internal ORDER BY CIRCONFERENCE ASC;
```
+-----------------+----------------+----------------------------+----------------+
| arrondissement  |    famille     |        nom_commune         | circonference  |
+-----------------+----------------+----------------------------+----------------+
| 12              | Ebenaceae      | Plaqueminier de Virginie   | NULL           |
| 16              | Taxodiaceae    | Séquoia géant              | NULL           |
| 16              | Magnoliaceae   | Magnolia à grandes fleurs  | NULL           |
| 7               | Moraceae       | Oranger des Osages         | NULL           |
| 12              | Fagaceae       | Chêne vert                 | NULL           |
| 12              | Pinaceae       | Pin Napoléon               | 50.0           |
| 5               | Fagaceae       | Faux de Verzy              | 72.0           |
| 16              | Cornaceae      | Arbre aux pochettes        | 120.0          |
| 12              | Taxaceae       | If commun                  | 140.0          |
| 16              | Araucariaceae  | Désespoir du singe         | 140.0          |
| 16              | Ebenaceae      | Kaki                       | 145.0          |
| 12              | Sapindaceae    | Erable d'Italie            | 160.0          |
| 12              | Ebenaceae      | Kaki                       | 160.0          |
| 16              | Fabaceae       | Chicot du Canada           | 162.0          |
| 12              | Ebenaceae      | Plaqueminier de Virginie   | 180.0          |
| 16              | Fagaceae       | Chêne liège                | 180.0          |
| 4               | Ulmaceae       | Orme champêtre             | 180.0          |
| 7               | Eucomiaceae    | Arbre à gutta-percha       | 190.0          |
| 16              | Moraceae       | Murier à papier            | 190.0          |
| 16              | Pinaceae       | Cèdre bleu de l'Atlas ple  | 195.0          |
| 8               | Cupressaceae   | Cèdre à encens             | 195.0          |
| 16              | Fagaceae       | Hêtre pleureur             | 200.0          |
| 12              | Magnoliaceae   | Tulipier de Virginie       | 205.0          |
| 19              | Malvaceae      | Tilleul argenté            | 205.0          |
| 3               | Betulaceae     | Noisetier de Byzance       | 210.0          |
| 16              | Ginkgoaceae    | Arbre aux quarante écus    | 215.0          |
| 15              | Betulaceae     | Aulne glutineux            | 220.0          |
| 20              | Sapindacaees   | Erable de Montpellier      | 225.0          |
| 12              | Pinaceae       | Pin aux grands cônes       | 225.0          |
| 19              | Ginkgoaceae    | Arbre aux quarante écus    | 230.0          |
| 16              | Fagaceae       | Hêtre pleureur             | 230.0          |
| 16              | Taxaceae       | If commun                  | 235.0          |
| 16              | Pinaceae       | Pin de Corse               | 240.0          |
| 12              | Ulmaceae       | Zelkova du Japon           | 240.0          |
| 11              | Betulaceae     | Noisetier de Byzance       | 245.0          |
| 12              | Ulmaceae       | Faux orme de Sibérie       | 245.0          |
| 12              | Pinaceae       | Pin noir                   | 248.0          |
| 16              | Pinaceae       | Pin noir                   | 250.0          |
| 12              | Ginkgoaceae    | Arbre aux quarante écus    | 255.0          |
| 16              | Ulmaceae       | Faux orme de Sibérie       | 260.0          |
| 6               | Bignoniaceae   | Catalpa commun             | 260.0          |
| 16              | Betulaceae     | Noisetier de Byzance       | 260.0          |
| 12              | Taxodiaceae    | Cyprés chauve              | 270.0          |
| 12              | Sapindaceae    | Erable de Cappadoce        | 280.0          |
| 8               | Ginkgoaceae    | Arbre aux quarante écus    | 283.0          |
| 16              | Taxodiaceae    | Cyprés chauve              | 290.0          |
| 12              | Cannabaceae    | Micocoulier de Provence    | 295.0          |
| 16              | Fagaceae       | Hêtre pourpre              | 300.0          |
| 12              | Magnoliaceae   | Tulipier de Virginie       | 305.0          |
| 8               | Taxodiaceae    | Séquoia géant              | 320.0          |
| 9               | Juglandaceae   | Pérocarya du Caucase       | 330.0          |
| 19              | Fabaceae       | Sophora du Japon           | 335.0          |
| 14              | Taxodiaceae    | Séquoia sempervirent       | 335.0          |
| 16              | Platanaceae    | Platane d'Orient           | 340.0          |
| 20              | Sapindaceae    | Marronnier d'Inde          | 345.0          |
| 16              | Taxodiaceae    | Cyprés chauve              | 350.0          |
| 13              | Pinaceae       | Cèdre bleu de l'Atlas      | 350.0          |
| 13              | Sapindaceae    | Marronnier d'Inde          | 355.0          |
| 19              | Oleaceae       | Frêne commun               | 365.0          |
| 14              | Fagaceae       | Hêtre pourpre              | 370.0          |
| 12              | Fagaceae       | Hêtre pourpre              | 370.0          |
| 16              | Platanaceae    | Platane d'Orient           | 375.0          |
| 5               | Fabaceae       | Robinier faux-acacia       | 385.0          |
| 16              | Ginkgoaceae    | Arbre aux quarante écus    | 395.0          |
| 16              | Ulmaceae       | Faux orme de Sibérie       | 395.0          |
| 20              | Platanaceae    | Platane commun             | 395.0          |
| 16              | Juglandaceae   | Pérocarya du Caucase       | 400.0          |
| 12              | Platanaceae    | Platane commun             | 405.0          |
| 12              | Platanaceae    | Platane commun             | 405.0          |
| 16              | Paulowniaceae  | Paulownia                  | 420.0          |
| 12              | Fagaceae       | Chêne rouvre               | 430.0          |
| 12              | Pinaceae       | Cèdre du Liban             | 440.0          |
| 12              | Fagaceae       | Chêne rouve                | 450.0          |
| 16              | Pinaceae       | Cèdre du Liban             | 460.0          |
| 16              | Simaroubaceae  | Ailanthe                   | 460.0          |
| 18              | Platanaceae    | Platane d'Orient           | 470.0          |
| 19              | Taxodiaceae    | Séquoia géant              | 470.0          |
| 12              | Platanaceae    | Platane d'Orient           | 475.0          |
| 16              | Platanaceae    | Platane commun             | 480.0          |
| 8               | Platanaceae    | Platane d'Orient           | 480.0          |
| 16              | Taxodiaceae    | Séquoia géant              | 490.0          |
| 12              | Platanaceae    | Platane commun             | 490.0          |
| 16              | Sapindaceae    | Marronnier d'Inde          | 505.0          |
| 12              | Platanaceae    | Platane commun             | 510.0          |
| 16              | Platanaceae    | Platane commun             | 520.0          |
| 16              | Platanaceae    | Platane commun             | 525.0          |
| 16              | Juglandaceae   | Ptérocarya de Chine        | 530.0          |
| 12              | Fagaceae       | Hêtre pleureur             | 530.0          |
| 16              | Fagaceae       | Hêtre pourpre              | 558.0          |
| 12              | Juglandaceae   | Noyer noir                 | 570.0          |
| 12              | Platanaceae    | Platane commun             | 570.0          |
| 14              | Platanaceae    | Platane commun             | 580.0          |
| 17              | Platanaceae    | Platane commun             | 595.0          |
| 16              | Taxodiaceae    | Séquoia géant              | 655.0          |
| 19              | Platanaceae    | Platane d'Orient           | 670.0          |
| 8               | Platanaceae    | Platane d'Orient           | 700.0          |
| 7               | Platanaceae    | Platane d'Orient           | 700.0          |
+-----------------+----------------+----------------------------+----------------+
97 rows selected (29.837 seconds)
```

* District qui contient le plus d'arbre
> SELECT ARRONDISSEMENT,COUNT(*) AS NB FROM trees_internal GROUP BY ARRONDISSEMENT ORDER BY COUNT(*) DESC LIMIT 1;
```
+-----------------+-----+
| arrondissement  | nb  |
+-----------------+-----+
| 16              | 36  |
+-----------------+-----+
1 row selected (60.581 seconds)
```
