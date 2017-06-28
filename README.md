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
### **create** command
Create multi-attribute linear-hashed (MALH) files, by accepting four command line arguments:
- the name of the relation
- the number of attributes
- the initial number of data pages (rounded up to nearest 2n)
- the multi-attribute hashing choice vector
The will give one relation/table, which is a analogous to an SQL command like:
`create table R ( a1 text, a2 text, ... an text );`
E.G.
`$ ./create  abc  4  6  "0,0:0,1:1,0:1,1:2:0:3:0"`
```
./create [Relation_Name] [Num_of_Attributes] [Initial_Num_of_Page] "[Choice_Vector]"

./gendata [Num_of_Generated_Tuple] [Num_of_Attributes] 

./stats [Relation_Name]
af
./select [Relation_Name] ?,?,?,?
```
asd
