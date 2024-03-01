# Hash Hash Hash

This code functions to prevent race conditions with multiple thread interactions on a hashmap through mutual exclusion with mutex locks.

## Building

To build, run the 'make' command.

## Running

To run the program, use the command './hash-table-tester -t <# of threads> -s <# of items>'

Examples (tested using a VM with 2 cores, for the purpose of the performance section below):

./hash-table-tester -t 2 -s 100000
Generation: 137,047 usec
Hash table base: 778,296 usec
  - 0 missing
Hash table v1: 1,144,241 usec
  - 0 missing
Hash table v2: 561,649 usec
  - 0 missing

./hash-table-tester -t 4 -s 90000
Generation: 244,180 usec
Hash table base: 4,084,735 usec
  - 0 missing
Hash table v1: 4,109,900 usec
  - 0 missing
Hash table v2: 1,661,455 usec
  - 0 missing

./hash-table-tester -t 4 -s 100000
Generation: 266,989 usec
Hash table base: 4,498,901 usec
  - 0 missing
Hash table v1: 4,993,339 usec
  - 0 missing
Hash table v2: 2,282,368 usec
  - 0 missing

## First Implementation

For this implementation, I treated the 'hash_table_v1_add_entry' function as a critical section, meaning only one thread was able to modify the hash table at a time. This removed the possibility of a race condition or data races, however, it also meant that mutual exclusion had to be broken and I had to unlock the lock at the end of the function to prevent a deadlock. This specifically fixes the race condition where two threads attempt to append to the same bucket at the same instant.

### Performance

Looking at the results in the examples section above, we can see that increasing the number of threads causes v1 to perform worse than the base. This is due to locking occuring as a result of adding threads. When we try to get a lock that is already held, the program must run a thread context switch which takes time and slows down the process due to locking/unlocking the process.  

## Second Implementation

For this implementation I added a lock to each bucket in the hashmap, meaning 4096 locks in total. This means that multiple threads are able to add to the hashmap at one time as long as their items hash to different buckets. This is a working solution because locking still will happen for every insertion at the tail of the SLIST.

### Performance

Using the results of hash table v2 from the examples section above, we can see that there is a large increase in performance because we are now utilizing the abilities of multi-threading. Now, more than one item can be added into the hashmap at a given time because multiple threads can access it at once. The reason that this resulted in a performance increase as compared to v1 is because v1 used only a single mutex, meaning multi-threading could not be fully utilized. This resulted in a speed-up of nearly double the base. 

## Cleaning up

To clean up, run the command 'make clean'

To remove python artifacts from use of the tester, run the command 'rm -r __pycache__/'