# Gradual-P-Median
Gradual P-Median Problem
sets        i   sites  / 1 * 400/;

ALIAS(I,J);
parameters

xc(i) coordinate of site i /
$include x400.dat
/

yc(i) coordinate of site i /
$include  y400.dat
/

h(i) demand of site i /
$include dem400.dat
/

p total number of facilities  /1/
r1    /5/
r2  /45/

dist(i,j)   distance between facility j and customer i;

dist(i,j)=round(SQRT((abs(xc(j)-xc(i)))**2+(abs(yc(j)-yc(i)))**2),2);

Variables
         D(i,j)    flow bt facility to customer
         X(i)      facility i is open or closed
         B(i,j)    coverage value
         Z         total cost;

nonnegative variable B;
binary variable X,D;
FREE VARIABLE Z;


equations OBJ Objective function
          cc(i,j)
          ee(i,j)
          dd(i,j)
          bb
gg(i,j)
hh(j)
;
OBJ..            Z =E=  SUM((i,j),B(i,j)*h(j));

cc(i,j)$(dist(i,j)<= r1)..                          B(i,j) =E= 1*D(i,j);
ee(i,j)$(dist(i,j)<= r2 and dist(i,j)> r1)..        B(i,j) =E= ((r2-dist(i,j)*2)*D(i,j)/(r2-r1));
dd(i,j)$(dist(i,j)> r2)..                           B(i,j) =E= 0;
bb..               SUM(i,X(i)) =E= p;
gg(i,j)..          D(i,j) =L= X(i);
hh(j)..            SUM(i,D(i,j)) =E= 1;

model gradualcov / all /;

gradualcov.optcr = 0.0 ;

option mip=cplex;
OPTIONS ITERLIM = 9000000, DECIMALS = 2, LIMROW = 0, LIMCOL = 0,
OPTCR=0.000000, RESLIM = 7200;

solve gradualcov using mip maximizing Z;
display X.L,B.L,D.L;
