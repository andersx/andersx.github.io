---
layout: post
title: Fast Lennard-Jones and electrostatics on a CPU
date: '2011-10-26T22:26:00.009+02:00'
author: hellogetmyblogback
tags:
- opls molecular mechanics force field speed fast implementation icpc g++ c++ quake
fast inverse square root
modified\_time: '2011-10-27T09:34:41.500+02:00'
blogger\_id: tag:blogger.com,1999:blog-8160351477288734008.post-122881654375705947
blogger\_orig\_url: https://combichem.blogspot.com/2011/10/fast-lennard-jones-and-electrostatics.html
---

I recently got interested in programming molecular mechanics force fields on CPUs and GPUs. One of the hurdles you have to get through, is the non bonded interactions, which you have to iterate over all pairs of atoms. It thus scales as N^2 with the number of atoms, whereas the bonded terms scale only linearly. A good example is the [OPLS force field](http://en.wikipedia.org/wiki/OPLS) (read more on [Wikipedia](http://en.wikipedia.org/wiki/OPLS)). Here the non-bonded interactions are given by a Lennard-Jones potential term an an electrostatic term:

[![](http://upload.wikimedia.org/wikipedia/en/math/d/4/8/d48008008d016c29d82d4009943a5783.png)](http://upload.wikimedia.org/wikipedia/en/math/d/4/8/d48008008d016c29d82d4009943a5783.png)

For more information about molecular modeling and force fields, I can  recommend reading the [Molecular Modeling Basics](http://www.crcpress.com/product/isbn/9781420075267) book, which has excellent pages on the subject. You can also just ask in the comment sections, if you have anything. Finally, the code can be found for download at the bottom of this post.

Now let's get started! I've put the coordinates of all atoms in the double coordinates[] 2D array. In the example I'm using the coordinates of the protein structure of Cutinase [1CEX](http://www.pdb.org/pdb/explore/explore.do?structureId=1cex), which has 2867 atoms. I used [pdb2pqr](http://kryptonite.nbcr.net/pdb2pqr/) for protonation. I'm going to assume (for simplicity), that all interactions use the same parameters. The naive way to implement the non-bonded OPLS interaction could be (in C/C++):
```
// Lennard-Jones parameters
double A = 10.0;
double C = 10.0;
// Electrostatic parameter
double E = -10.0;

double non\_bonded\_interaction(const double a[3], const double b[3]) {

// Fastest way of getting distance
double distance = sqrt((a[0] - b[0]) * (a[0] - b[0])
+ (a[1] - b[1]) * (a[1] - b[1])
+ (a[2] - b[2]) * (a[2] - b[2]));

return A*pow(distance, -12.0)
+ C*pow(distance, -6.0)
+ E/distance;
}

int main() {

// Set energy to 0
double energy = 0.0;
// Loop over all atoms
for (unsigned int i = 0; i < n\_atoms; i++) {

// Only up to i-1, to avoid double counting
// interactions, and self-interactions
for (unsigned int j = 0; j < i; j++) {

energy += non\_bonded\_interaction(coordinates[i],
coordinates[j]);

}
}
std::cout << "E = " << energy << std::endl

return 0;
}

```
Timing this gives 0.0300 µs per iteration with the Intel icpc compiler, and 0.189 µs with the g++ compiler, both with -O3 optimization. As I will demonstrate, this is because pow() is horribly implemented in g++. Lets further optimize the non\_bonded\_interaction() function:
```
double non\_bonded\_interaction(const double a[3], const double b[3]) {

double distance = sqrt((a[0] - b[0]) * (a[0] - b[0])
+ (a[1] - b[1]) * (a[1] - b[1])
+ (a[2] - b[2]) * (a[2] - b[2]));

double distance6 = distance * distance * distance
* distance * distance * distance;
double distance12 = distance6 * distance6;

return A/distance12 + C/distance6 + E/distance;
}

```


Now we get: 0.0295 µs with icpc and 0.0313 µs with g++. Clearly pow() shouldn't be used with GNU compilers!

Next on our list of optimizations is getting rid of divisions. Divisions are performed iteratively by the CPU, while multiplications are done bit-parallel by specialized circuits hard-coded into the CPU silicon. We have three divisions, but we can write everything in terms of only one, using reciprocal distances:


```
double non\_bonded\_interaction(const double a[3], const double b[3]) {

double distance = sqrt((a[0] - b[0]) * (a[0] - b[0])
+ (a[1] - b[1]) * (a[1] - b[1])
+ (a[2] - b[2]) * (a[2] - b[2]));

double inv\_distance = 1.0/distance;
double inv\_distance6 = inv\_distance * inv\_distance
* inv\_distance * inv\_distance
* inv\_distance * inv\_distance;
double inv\_distance12 = inv\_distance6 * inv\_distance6;

return A * inv\_distance12
+ C * inv\_distance6
+ E * inv\_distance;
}

```




This gives us 0.0173 µs with icpc and 0.0189 µs with g++. Still ... there is one division left and also a square-root which is problematic. These are what takes up by far the most time in the calculation. But there is a hacky trick we can use. I'm going to show you how to use an approach by Newton to estimate inverse square roots .. take a look at [this Wikipedia page](http://en.wikipedia.org/wiki/Fast_inverse_square_root) for more info. The method became famous after it was found in the Quake III open source engine. Here it is in double type, so slightly different than the float type example on wikipedia. I'm using two final iterations to achieve sufficient accuracy.


```
inline double fast\_inv\_sqrt(double number) {

long long i;
double x2, y;
const double threehalfs = 1.5;
x2 = number * 0.5;
y  = number;
i  = * ( long long * ) &y;
i  = 0x5fe6ec85e7de30da - ( i >> 1 );
y  = * ( double * ) &i;
y  = y * ( threehalfs - ( x2 * y * y ) );
y  = y * ( threehalfs - ( x2 * y * y ) );
return y;
}

```
Using this, we can avoid ever dividing and taking square roots, and reduce our code to:


```
double non\_bonded\_interaction(const double a[3], const double b[3]) {
double dx = (a[0] - b[0]);
double dy = (a[1] - b[1]);
double dz = (a[2] - b[2]);
double distance\_sqr = dx*dx + dy*dy + dz*dz;

double inv\_distance   = fast\_inv\_sqrt(distance\_sqr);
double inv2\_distance  = inv\_distance * inv\_distance;
double inv6\_distance  = inv2\_distance * inv2\_distance
* inv2\_distance;
double inv12\_distance = inv6\_distance * inv6\_distance;
return A * inv\_distance12
+ C * inv\_distance6
+ E * inv\_distance;


}

```


Now, timings are down to 0.0118 µs for icpc and 0.0155 µs for g++ per iteration. It's hard to optimize the code further from here, using code that isn't very hacky, but we still got a x2.5 times speed-up with the Intel compiler and x12.2! times speed-up with the GNU C++ compiler. Still, we can always try and parallelize the code using OpenMP. This is the easiest way to parallelize code there ever was. We only need to add ONE compiler flag, which tells it to parallelize the next loop. Take a look:


```
// Loop over all atoms
for (unsigned int i = 0; i < n\_atoms; i++) {

// Tell compiler, to run next in loop in parallel
#pragma omp parallel for reduction(+:energy)

//Only up to i-1, to avoid double counting interactions, and self-interactions
for (unsigned int j = 0; j < i; j++) {

energy += non\_bonded\_interaction(coordinates[i], coordinates[j]);

}
}

```


To compile we also must remember to link to OpenMP:



icpc -O3 cpuff.cpp -o cpuff -openmp (for Intel)

g++ -O3 cpuff.cpp -o cpuff -fopenmp (for GNU)



On four cores instead of one, this gives us: 0.0042 µs for icpc and 0.0042 for g++ as well. That's a x2.8 and x3.6 speed up for Intel and GNU, respectively! Not too shabby, by no means! Compared to the unparallelized code, this is a x7.1 speed-up for Intel and a whooping x45.0 speed-up for the initial lazy GNU compiled code.

I hope this post will inspire you to play around with these easy-to-do optimizations yourself. You can download code examples here:



[cpuff.cpp](http://dl.dropbox.com/u/17435887/cpuff.cpp) (the implementation)[coordinates.h](http://dl.dropbox.com/u/17435887/coordinates.h) (the protein coordinates. I put this in a 2D array to avoid writing a parser)





DISCLAIMER: The above was carried out on an Intel Core i5-760 running 64-bit Ubuntu Server 10.04.3, icpc (ICC) 12.0.0 20101006 and g++ (Ubuntu 4.4.3-4ubuntu5) 4.4.3. Timings are averages over 10 consecutive runs. I compiled everything with -O3 optimization.



