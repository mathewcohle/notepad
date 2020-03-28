The Little Book of Semaphores
=============================

Source: https://greenteapress.com/wp/semaphores/

Notes
-----

Concurrency: two events are concurrent if we cannot tell by looking at the program which will happen first.

Semaphore: state represented by integer

-	can be initialized to any value after which only inc/decr operations are allowed
-	when thread decrements and result is negative, it block until another thread increments the value
-	when thread increments the semaphore and other threads are waiting, one waiting thread gets unblocked

Multiplex: like mutex but for n threads running in critical section concurrently
