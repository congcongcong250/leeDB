# leeDB, a C based efficient data management system
## Table of Content
- [Set Up](#1)
- [Instruction](#2)
    - [create](#3)
    - [insert](#4)
    - [select](#5)
    - [stats](#6)
- [Multihashing](#multihashing)
- [Linear hashing](#7)
## Set up<a id="1"></a>

Copy all files to a folder
```
$ make
gcc -Wall -Werror -g   -c -o create.o create.c
gcc -Wall -Werror -g   -c -o query.o query.c
gcc -Wall -Werror -g   -c -o page.o page.c
gcc -Wall -Werror -g   -c -o reln.o reln.c
gcc -Wall -Werror -g   -c -o tuple.o tuple.c
gcc -Wall -Werror -g   -c -o util.o util.c
gcc -Wall -Werror -g   -c -o chvec.o chvec.c
gcc -Wall -Werror -g   -c -o hash.o hash.c
gcc -Wall -Werror -g   -c -o bits.o bits.c
gcc   create.o query.o page.o reln.o tuple.o util.o chvec.o hash.o bits.o   -o create
gcc -Wall -Werror -g   -c -o insert.o insert.c
gcc   insert.o query.o page.o reln.o tuple.o util.o chvec.o hash.o bits.o   -o insert
gcc -Wall -Werror -g   -c -o select.o select.c
gcc   select.o query.o page.o reln.o tuple.o util.o chvec.o hash.o bits.o   -o select
gcc -Wall -Werror -g   -c -o stats.o stats.c
gcc   stats.o query.o page.o reln.o tuple.o util.o chvec.o hash.o bits.o   -o stats
gcc -Wall -Werror -g   -c -o gendata.o gendata.c
gcc   gendata.o query.o page.o reln.o tuple.o util.o chvec.o hash.o bits.o   -o gendata
```

## Instruction<a id="2"></a>
### `create` command
`./create [Relation_Name] [Num_of_Attributes] [Initial_Num_of_Page] "[Choice_Vector]"`

Create multi-attribute linear-hashed (MALH) files, with four command line arguments:
- the name of the relation
- the number of attributes
- the initial number of data pages (rounded up to nearest 2n)
- the multi-attribute hashing choice vector

The will give one relation/table, which is a analogous to an SQL command like:
`create table R ( a1 text, a2 text, ... an text );`

E.G.

The following example of using create makes a table called ‘new_relation’ with 4 attributes and 8 initial data pages:

`$ ./create  new_relation  4  6  "0,5:0,1:1,10:1,1:2,0:3:12"`

Choice vector is a 32-entries binary vector for each tuple, indicating the tuple's [Multihashing](#multihashing) value.
### `insert` command
`$ ./gendata [Num_of_Generated_Tuple] [Num_of_Attributes] [Start_Index] | ./insert [Relation_Name]`

Insert command is used to insert tuples from stdin. For testing purpose, command `gendata` is introduced to generate bulk of tuples to stdout, in which case a pipeline command could insert tuples into relation.

E.G.
```
$ ./gendata  5 3 11
11,sandwich,pocket
12,circus,spectrum
13,snail,adult
14,crystal,fungus
15,bowl,surveyor
```

`./gendata 250 3 101 | ./insert new_relation`

### `select` command
`./select [Relation_Name] [Tuple_Query]`

Select the tuples matching the tuple query. A query including all attributes in a tuple formated as "val1,val2,...,valn", where some of the vali can be '?' (without the quotes) for ambiguous search.

Selection will use Choice vector of query tuple and Multihashing to search only matched pages (including overflow).

E.G.
`$ ./select new_relation 10,?,apple`

### `stats` command
`$ ./stats [Relation_Name]`

Stats command is used to check the stats of a relation. When Linear hash happens, page pool will extend. Stats is a good command to check if the linear hash happen in righ time and right way. 
```
$ ./stats new_relation
Global Info:
#attrs:3  #pages:4  #tuples:251  d:2  sp:0
Choice vector
0,0:0,1:0,2:1,0:1,1:2,0:0,31:1,31:2,31:0,30:1,30:2,30:0,29:1,29:2,29:0,28:1,28:2,28:
0,27:1,27:2,27:0,26:1,26:2,26:0,25:1,25:2,25:0,24:1,24:2,24:0,23:1,23
Bucket Info:
#   Info on pages in bucket
    (pageID,#tuples,freebytes,ovflow)
0   (d0,57,7,0) -> (ov0,14,757,-1)
1   (d1,57,8,3) -> (ov3,2,971,-1)
2   (d2,57,1,1) -> (ov1,4,946,-1)
3   (d3,58,3,2) -> (ov2,2,975,-1)
```
## Multihashing
Multihashing is a algorithm to increase query performance for database. It maps tuple to a specified page in `insert` operation. The page works as a bucket with a common index (page id). All tuples inside of a page (bucket) have a hash value, whose first several bits indicates the id of the page.

### Construct tuple hash value
Tuple hash value is constructed by choice vector. It is a 32-entries binary vector for each relation(table), indicating the tuple hash value bit by bit from attributes hash value in this tuple. For example, a entry "2,5" implies **one tuple hash bit** is assigned from the **6th bits(index 5)** of **hash value** of the **3rd(index 2) attribute** in this tuple. 

`pseudocode: tuple->hash[0] = hash(tuple->attribute[2])[5]`

The vector "0,5:0,1:1,10:1,1:2,0:3:12" from example above explicitly assigns the value of first 5 bits in a choice vector. 

|Bit in Choice Vector| 0 | 1 | 2 | 3 | 4 |
|--------------------|---|---|---|---|---|
|No. of Attribute    | 0 | 0 | 1 | 2 | 3 |
|Index in hash(attr) | 5 | 1 |10 | 0 |12 |

For the rest 32 - 5 = 27 bits, database will implicitly automatically generate the mapping.
### Insertion with Multihashing
For each tuple, we pick the lower several bits as its page index. Numbers of bits is determined by base 2 exponent on number of pages. If there are 8 pages, we take 3 bits. In insertion tuples will only go into the page with id matching its index bits.
### Query with Multihashing
Multihashing makes every page as a bucket for tuples with same index bits. That makes bucket search possible on query operation. When a tuple comes in, its multihash value will be calculated first and the database only need to search for the mapping page based on index bits, or pages if there is no quote for attributes.
## Linear hashing
Linear hashing is another important algorithm for this database. 
