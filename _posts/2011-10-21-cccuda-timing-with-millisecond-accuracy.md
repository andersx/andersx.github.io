---
layout: post
title: C++ timing with millisecond accuracy
date: '2011-10-21T09:28:00.001+02:00'
author: hellogetmyblogback
tags:
- boost timer runtime millisecond gettimeofday
modified\_time: '2011-10-21T09:34:20.061+02:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-5704697457634763048
blogger\_orig\_url: https://combichem.blogspot.com/2011/10/cccuda-timing-with-millisecond-accuracy.html
---

How do you know if you have efficiently implemented a routine. A simple routine from [Boost](http://en.wikipedia.org/wiki/Boost_C%2B%2B_Libraries) can tell you. More specifically the boost::timer function.

Boost is a set of [free software](http://en.wikipedia.org/wiki/Free_software "Free software") [libraries](http://en.wikipedia.org/wiki/Library_%28computer_science%29 "Library (computer science)") that extend the functionality of [C++](http://en.wikipedia.org/wiki/C%2B%2B "C++"), in the Wikipedia discription. There are tons of very useful features you'd expect to be native to an advanced programming language as C++ but are not.



How to get boost? Usually on Linux you can just say something like apt-get install libboost or yum install libboost. Let me know if you want a tutorial to compile or install boost. The examples below compile with both Intel and Gnu C++ compilers and even Nvidia compilers (I tried NVCC 4.0).



Here is a code example of how to time a routine with boost:



#include <boost/timer.hpp>      // For boost::timer class

#include <iostream>             // For printing





// Some constants

unsigned int x = 20000;

bool flag = true;

unsigned int k = 0;



int main() {

// Start boost timer.

boost::timer t;



// Do a double loop that takes some time.

for (unsigned int i = 0; i < x; i++) {

for (unsigned int j = 0; j < x; j++) {

k = i + j;

if (k % 2 == 0) {

flag = false;

} else {

flag = true;

}

}

}



// Store elapsed time from boost

double elapsed = t.elapsed();



// Print elapsed time from the t object.

std::cout << "boost/timer says:" << std::endl;

std::cout << "Total time    = " << elapsed

<< " seconds" << std::endl;

std::cout << "Avg time/iter = " << elapsed / (x * x)

<< " seconds" << std::endl;



return 0;

}



I get the following output on my Core i5-760 box and the icpc compiler:



boost/timer says:

Total time    = 0.35 seconds

Avg time/iter = 8.75e-10 seconds





You can use t.reset() if you want to reset the timer in your program.





If you want higher accuraccy, you can use the gettimeofday() function from sys/time.h. This example does not require boost either, but the code is way uglier. This is done very much like in the above code:



#include <sys/time.h>           // For gettimeofday()

#include <iostream>             // For printing





// Some constants

unsigned int x = 20000;

bool flag = true;

unsigned int k = 0;



int main() {

// Create objects for gettimeofday() timing

struct timeval start\_time;

struct timeval end\_time;



// Get the time of day -- NULL means we don't care about

// time zones. Store in start\_time.

gettimeofday(&start\_time, NULL);





// Do a double loop that takes some time.

for (unsigned int i = 0; i < x; i++) {

for (unsigned int j = 0; j < x; j++) {

k = i + j;

if (k % 2 == 0) {

flag = false;

} else {

flag = true;

}

}

}



//Store time of day in end time

gettimeofday(&end\_time, NULL);



// Calculate the elapsed time with micro second accuracy from gettime of day

double start = start\_time.tv\_sec*1000000 + (start\_time.tv\_usec);

double end   = end\_time.tv\_sec*1000000 + (end\_time.tv\_usec);

elapsed     = end - start;



// Print elapsed time from gettimeofday().

std::cout << "gettimeofday says:" << std::endl;

std::cout << "Total time    = " << elapsed                 << " microseconds" << std::endl;

std::cout << "Avg time/iter = " << elapsed / (x * x)                 << " microseconds" << std::endl;



return 0;

}

This gives me the following output:gettimeofday says:

Total time    = 366208 microseconds

Avg time/iter = 0.00091552 microseconds

Happy coding! Let me know if you have questions or comments.