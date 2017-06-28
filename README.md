# leeDB, a C based efficient data management system

## Set up

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

## Instruction
### ```create``` command
`./create [Relation_Name] [Num_of_Attributes] [Initial_Num_of_Page] "[Choice_Vector]"`

Create multi-attribute linear-hashed (MALH) files, by accepting four command line arguments:
- the name of the relation
- the number of attributes
- the initial number of data pages (rounded up to nearest 2n)
- the multi-attribute hashing choice vector

The will give one relation/table, which is a analogous to an SQL command like:
`create table R ( a1 text, a2 text, ... an text );`

E.G.

The following example of using create makes a table called ‘new_relation’ with 4 attributes and 8 initial data pages:

`$ ./create  new_relation  4  6  "0,5:0,1:1,10:1,1:2,0:3:12"`

- Choice vector is a 32-bits binary vector for each tuple, constructed by bits from hash value of attributes in this tuple. It is used for Multihashing and Linear hashing. For example, "2,5" implies *one Choice Vector bit* is assigned from the *6th* bits* (5 for index) of *hash value* of the *3st (2 for index) attribute* of this tuple. `pseudocode: tuple->vector[0] = hash(tuple->attribute[2])[5]`

The vector "0,5:0,1:1,10:1,1:2,0:3:12" above explicit assigns the value of first 5 bits in the choice vector. 

|Bit in Choice Vector| 0 | 1 | 2 | 3 | 4 |
|--------------------|---|---|---|---|---|
|No. of Attribute    | 0 | 0 | 1 | 2 | 3 |
|Index in hash(attr) | 5 | 1 |10 | 0 |12 |

For the rest 32 - 5 = 27 bits, database will implicitly automatically generate the mapping.

### ```insert``` command
```


./gendata [Num_of_Generated_Tuple] [Num_of_Attributes] 

./stats [Relation_Name]
af
./select [Relation_Name] ?,?,?,?
```*

