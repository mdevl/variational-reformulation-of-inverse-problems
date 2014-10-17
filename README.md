Variational Reformulation of Bayesian Inverse Problems
================================================================

**Panagiotis Tsilifis<sup>1</sup>, Ilias Bilionis<sup>2,*</sup>, Ioannis Katsounaros<sup>3,4,5</sup> Nicholas Zabaras<sup>6</sup>**

<sup>1</sup>
Department of Mathematics, University of Southern California, Los Angeles, CA 90089-2532, USA
School of Mechanical Engineering, Purdue University, 585 Purdue Mall, West Lafayette, IN 47906-2088, USA
Department of Chemistry, University of Illinois at Urbana-Champaign, S. Mathews Ave., Urbana, IL 61801, USA
Materials Science Division, Argonne National Laboratory, 9700 S. Cass Ave., Lemont, IL 60439, USA
Leiden Institute of Chemistry, Leiden University, Einsteinweg 55, P.O. Box 9502, 2300 RA Leiden, The Netherlands
School of Engineering, University of Warwick, UK

<sup>*</sup>
Corresponding author.
--------
The classical approach to inverse problems is based on the optimization of a misfit function. Despite its computational appeal, such an approach suffers from many shortcomings, e.g., non-uniqueness of solutions. The Bayesian formalism to inverse problems avoids most of the difficulties encountered by the optimization approach, albeit at an increased computational cost. In this work, we use information theoretic arguments in order to cast the Bayesian inference problem in terms of an optimization problem. The resulting scheme combines the theoretical soundness of fully Bayesian inference with the computational efficiency of a simple optimization.

Submitted to: *Inverse Problems*

Conventions
-----------

Here we outline some of the conventions that need to be followed when
implemented new classes/functions or anything related to the `vuq'
package.

Data
++++

Data are always 2D arrays with the rows corresponding to distinct data
points and the columns corresponding to the dimensionality of the each
data point. So, remember:

    data <---> 2D array (num samples) x (num dimensions).

Parameters
++++++++++

Typically, the person that implements a particular PDF might want to
assign to it parameters in an arbitrary way. However, for optimization
purposes, all parameters should be 1D arrays. If the parameter being
optimized is in reality a matrix, e.g., a covariance matrix, it will
be flattend in a `C`-style fashion. So, remember:

    parameter <---> 1D array (num dimensions), parameter.flatten()

Jacobian of a multi-output function
+++++++++++++++++++++++++++++++++++

The Jacobian of a multivariate function is a 2D array with the number of
rows corresponding to the comonent functions and the number of columns
to the input dimensions. That is:

    J[i, j] = df[i] / dx[j].

So, remember:

    Jacobian <---> 2D array (num outputs) x (num inputs).

This means that when you have a single-output function you should return
a single row matrix.

Hessian of a multi-output function
++++++++++++++++++++++++++++++++++

The Hessian of a multi-output function should be 3D array. That is:

    H[i, j, k] = d2f[i] / dx[j]dx[k].

So, remember:

    Hessian <---> 3D array (num outputs) x (num inputs) x (num inputs).

For the Hessian of a single-output function, you may return just a 2D array of
the right dimensions. This is to allow the use of sparse matrices.

Remark: To this point, 3D arrays are required only by the vuq.Model class (the
only multi-output function in this package). I suggest that we relax this
requirement in order to allow a model class to return a list of sparse matrices
which can have a tremendous effect in reducing the memory requirement. The only
place that this will have an effect in the existing code is
vuq.Likelihood._eval(). I have marked the affected region with a TODO.

Some things to keep in mind while dealing with the code
+++++++++++++++++++++++++++++++++++++++++++++++++++++++

These are some remarks about things that might lead to ugly bugs if we do not
pay attention to them.

Here we go:

    + Our Gamma(a, b) and Pymc's Gamma(a', b') distribution differ. The
      correct way to get one from the other is this:
        
        a' = a, b' = 1 / b.