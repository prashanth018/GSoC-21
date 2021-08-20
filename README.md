<div align=center>
  <h1><b> Google Summer Of Code 2021 with Chapel </b></h1>
</div>
<div align=center>
  <img src=https://user-images.githubusercontent.com/29123352/63638548-49064180-c679-11e9-9ffb-35d68650bc7a.png>
  <img src=https://camo.githubusercontent.com/a8a74b3986fa8d06b25c66c7d35cbfd66f1a8f214ce51aad0fedf46673754c46/68747470733a2f2f63686170656c2d6c616e672e6f72672f696d616765732f63686170656c2d6c6f676f2d3230302e706e67>
</div>
<hr>

## Google Summer of Code 2021 Final Report
This article summaries the work I did during my summer as being a participant of Google Summer of Code program with Chapel organization. I have to say that my association with Chapel has started as early as January and I started working on the Matrix Exponential Project from April. Following is a short report summary of the work, following which, I tried detailing each PR.

## Report Summary
[Here](https://github.com/chapel-lang/chapel/commits?author=prashanth018&since=2021-04-01&until=2021-08-31) is the list of commits that I have made relevant to this Project. You can also find my Project Proposal [here](https://github.com/prashanth018/GSoC-21/blob/main/Matrix%20Exponentials%20Proposal.pdf).

#### What's merged:
1. [Matrix exponentiation using Scaling and Squaring Algorithm](https://github.com/prashanth018/GSoC-21/blob/main/README.md#matrix-exponentiation-using-scaling-and-squaring-algorithm)
2. [Matrix Exponentials - Performance & LAPACK based solvePQ](https://github.com/prashanth018/GSoC-21/blob/main/README.md#matrix-exponentials---performance--lapack-based-solvepq)
3. [Sparse-Dense Matrix Multiplication](https://github.com/prashanth018/GSoC-21/blob/main/README.md#sparse-dense-matrix-multiplication)

#### Yet to merge:
1. [one-norm estimate & Tests](https://github.com/prashanth018/GSoC-21/blob/main/README.md#one-norm-estimate-and-tests)
2. [Action method & Tests](https://github.com/prashanth018/GSoC-21/blob/main/README.md#action-method-and-test)

#### Issues created/discovered:
1. [oneNormEst method should support matrices with eltType=complex](https://github.com/chapel-lang/chapel/issues/18158)
2. [Efficient way to handle Sparse matrix inputs for Matrix Exponentials](https://github.com/chapel-lang/chapel/issues/18157)
3. [Functionality to Multiply a Sparse and a Dense Matrix](https://github.com/chapel-lang/chapel/issues/18092)
4. [Addition of NumPy like methods in Linear Algebra module](https://github.com/chapel-lang/chapel/issues/18091)
5. [Handling array views correctly in the LinearAlgebra module](https://github.com/chapel-lang/chapel/issues/18159)
6. [Add LAPACK implementation for solve method in LinearAlgebra module](https://github.com/chapel-lang/chapel/issues/17912)

## Description

### Background

Ideally, exponential of matrix is just an application of taylor series expansion of exponential to a matrix. We could have gotten away by just plugging-in matrix in the taylor series expansion of exponentials. This taylor series expansion requires us to take powers of input matrix i.e., _A^2,A^4,A^6,A^8,A^10_.
  
**Optimization Question**: Do we need to take all the above specified matrix powers? Can we trade off some precision? Can we improve performance by caching the results?
  
**Research**: There is a [paper](https://epubs.siam.org/doi/abs/10.1137/09074721X) by Awad H. Al-Mohy which introduces _exactDs_ and _approxDs_ a.k.a D values of a Matrix. _exactDs_ are computed using exact onenorms and _approxDs_ are computed using estimate of onenorms. This paper finds a relation between the D values of a matrix and the precision. Something like, if max of _D4_ and _D6_ doesn't exceed _1.495585_ then we can get away by only computing _pade3_ which internally computes only _A^2,A^4_. We also make use of Lazy computation and caching of matrices. ExpmPadeHelper class helps us achieve all of this.

### Matrix exponentiation using Scaling and Squaring Algorithm
PR: [#17523](https://github.com/chapel-lang/chapel/pull/17523) <br>
This PR incorporates the implementation of expm method which takes in a Square Matrix _A_ as an input and returns the exponential of the Matrix. Input matrix can be of any data type. This PR also incorporates the _cosm_ and _sinm_ functionality which returns the cos and sin of the input matrix respectively. This PR includes _sincos_ functionality which just calls _sinm_ and _cosm_ internally and returns the sin and cos of a Matrix. This PR also includes tests for the _expm_, _sinm_ and _cosm_, _sincos_ tested over various input matrices like: Gradient Matrix, Edge Detection Matrix, Identity Matrix and various other forms of Matrices over various other data types. This PR especially incorporates [ExpmPadeHelper](https://github.com/prashanth018/GSoC-21/blob/main/README.md#background) class which is the crux of expm.

### Matrix Exponentials - Performance & LAPACK based solvePQ
PR: [#17966](https://github.com/chapel-lang/chapel/pull/17966) <br>
Calculation of exponential of a Matrix would eventually require a system of Linear Equations to be solved. This PR optimizes the usage of native solve method and this PR also adds functionality to use _LAPACK.gesv_ which is a Lapack routine to solve system of linear equations. This PR also adds the performance tests for _expm_, _sinm_, _cosm_, _sincos_.

### Sparse-Dense Matrix Multiplication
PR: [#18152](https://github.com/chapel-lang/chapel/pull/18152) <br>
Chapel doesn't allow the multiplication of a Sparse and a Dense Matrix. This PR incorporates a method _sparseDenseMatmut_ to carry out that functionality. _dot_ method in Chapel now supports product of a Sparse and a Dense Matrix. This PR also includes tests for this method.

### one-norm estimate and Tests
PR: [#18149](https://github.com/chapel-lang/chapel/pull/18149) <br>
This PR includes functionality to estimate the onenorms of a Matrix. The function _oneNormEst_ does this and can be used as an alternative to _norm_ function. _norm_ function runs in O(n^2) time while, _oneNormEst_ takes O(k.N) time. This PR also includes many helper functions such as _absSum_, _maxAlongAxis_, _ones_, _zeros_, _elementaryVector_, _argsort_, _everyColOfXParallelToColOfY_, _signRoundUp_, _columnNeedsResampling_, _columnResample_, _sparseDenseMatmul_, _getDense_ which are all private methods and aid the functionality of _oneNormEst_ method. Besides, this PR incorporates major refactoring of ExpmPadeHelper class, making it modular and readable. This PR also includes documentation wherever required and includes tests for Sparse matrices.

### Action method and Test
PR: 

## Miscellaneous

#### PRs
1. [Adding checksumming to mason](https://github.com/chapel-lang/chapel/pull/17380)
2. [Changes to choice method of Random module to support sampling on N-Dimensions](https://github.com/chapel-lang/chapel/pull/17168)
3. [Chpldoc not properly generated for few usecases](https://github.com/chapel-lang/chapel/pull/17058)

#### Issues
1. [Make Random.choice() method support multi-dimensional array](https://github.com/chapel-lang/chapel/issues/17136)
