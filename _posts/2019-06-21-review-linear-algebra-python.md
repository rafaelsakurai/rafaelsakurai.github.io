---
layout: post
title: "Review of Linear Algebra in Python"
date: 2019-06-21 23:00:00
tags: [numpy, vector, matrix, distance, euclidian]
published: true
excerpt: Review some basic concepts of Linear Algebra, and how to calculate using Python with NumPy.
comments: true
image: 2019-06-21-revisao-algebra-linear-python-01.png
---

# Numpy

[NumPy](http://www.numpy.org) is a package for scientific computing that allows calculation algebraic manipulation of matrices with **n** dimensions.

To import NumPy in your Python code:

{% highlight python %}
import numpy as np
{% endhighlight %}

> In the NumPy website, there is a [tutorial](https://numpy.org/devdocs/user/quickstart.html) that explains how to start.

The basic data type is **ndarray** which represents a multidimensional array. Examples:

{% highlight python %}
scores = np.array([7.8, 9.1, 8.6, 7.4])
houses = np.array([104, 515, 1], [140, 635, 1], [50, 210, 0])
{% endhighlight %}

# Linear Algebra

## Vector

Vectors can be understood as points in space, and we can represent the data using vectors. E.g. the data that represents a house, student test scores, values that make up the price of a product, etc, can be stored in a vector.

In Python, we represent the vector using an **array** of NumPy, so if we have only the information of square meters of the house, we have a vector with one axis:

{% highlight python %}
house = np.array([67])
{% endhighlight %}

Adding the house value, we have a vector with two axes:

{% highlight python %}
house = np.array([67, 250])
{% endhighlight %}

If we keep adding more house details like the number of bedrooms, we have a vector with three axes:

{% highlight python %}
house = np.array([67, 250, 2])
{% endhighlight %}

And we keep adding more house details to the vector and at each value, we have a new axis.

**Figure 1** shows a vector that can be visually represented until three axes.

\\
**Figure 1:** Visualizing a vector with 1, 2, and 3 axes.

<figure>
    <a href="/images/posts/2019-06-21-revisao-algebra-linear-python-01.png"><img src="/images/posts/2019-06-21-revisao-algebra-linear-python-01.png" alt="Visualizing a vector with 1, 2 and 3 axes."></a>
</figure>


## Useful methods to work with np.array

{% highlight python %}
x = np.array([1, 2, 3, 4])
x.sum() # sum the values of the vector = 10
x.min() # min value of the vector = 1
x.max() # max value of the vector = 4
{% endhighlight %}


## Sum of vectors

Two or more vectors (with the same length) can be summed through the sum of each element in the same position, e.g: when we have a vector with the value of products and another vector with taxes, we can sum both vectors to know how much you spend at each purchased product.

{% highlight python %}
products = np.array([5, 6, 7, 8])
taxes = np.array([1, 2, 3, 4])
sum = products + taxes  #[6, 8, 10, 12]
{% endhighlight %}

## Scalar sum

In a vector we can sum one value with all elements, this is a scalar sum.
E.g. imagine an array of students test score, and we want add 0.5 for each value:

{% highlight python %}
tests = np.array([6.5, 8.0, 7.5, 4.5, 9.0, 6.5, 6.0, 5.5, 7.0, 8.0, 6.5, 7.5])
tests += 0.5  #[7.0, 8.5, 8.0, 5.0, 9.5, 7.0, 6.5, 6.0, 7.5, 8.5, 7.0, 8.0]
{% endhighlight %}

## Operations with NumPy's array

Using **tests** vector from the previous example, we would like to know how many students have a score test greater or than 7.0. For this, we can apply a comparison in each element:

{% highlight python %}
pass = tests >= 7.0
# the result is a boolean vector: 
# array([True, True, True, False, True, True, False, False, True, True, True, True], dtype=bool) 
# True indicates a score greater or equal to 7.0, and False indicates scores less than 7.0.
{% endhighlight %}

We can count the **True** values:

{% highlight python %}
amountPass = sum(tests >= 7.0)  #9
{% endhighlight %}

The same can be done with students that have the score less than 7.0 and fail:

{% highlight python %}
fails = tests < 7.0
# array([False, False, False, True, False, False, True, True, False, False, False, False], dtype=bool)
{% endhighlight %}

And count how many values are **True**, that have a score less than **7,0**:

{% highlight python %}
amountFail = sum(tests < 7.0)  # 3
{% endhighlight %}

## Multiplication of vectors

Two or more vectors can be multiplied. We need multiply each value of the same positions of the vectors:

{% highlight python %}
x = np.array([1, 2, 3, 4])
y = np.array([5, 6, 7, 8])
mult = x * y  #[5, 12, 21, 32]
{% endhighlight %}

If we have a vector with scores test from one student:

{% highlight python %}
scores = np.array([8.0, 7.0, 7.5, 9.5, 10.0])
{% endhighlight %}

And a vector with weight of each test, used to calculate the final score:

{% highlight python %}
weights = np.array([0.2, 0.1, 0.1, 0.3, 0.3])
{% endhighlight %}

If multiply both vectors and sum all values, we have the student final score:

{% highlight python %}
finalScore = sum(scores * weights)
{% endhighlight %}

## Scalar multiplication

We can multiply a vector by a scalar, so we have a value that will be multiplied by each element of the vector. E.g. suppose that we have a vector of purchased products:

{% highlight python %}
products = np.array([18.0, 16.5, 17.0, 19.5, 18.5])
{% endhighlight %}

And we want calculate 10% of taxes for each value:

{% highlight python %}
taxes = valores * 0.1  #[1.8, 1.65, 1.7, 1.95, 1.85]
{% endhighlight %}

If you want to know the sum of all taxes we can sum the vector values **sum(taxes)**.

## Scalar product of vectors

**Figure 2** shows a scalar product of two vectors.

\\
**Figure 2:** Equation from the scalar product of two vectors.

<figure>
    <a href="/images/posts/2019-06-21-revisao-algebra-linear-python-02.png"><img src="/images/posts/2019-06-21-revisao-algebra-linear-python-02.png" alt="Equation from the scalar product of two vectors."></a>
</figure>

Where **x** and **y** are two vectors from same length, and **n** the length of vectors, the scalar product is the sum of both vectors multiplied:

{% highlight python %}
x = np.array([1, 2, 3, 4])
y = np.array([5, 6, 7, 8])
prod = np.dot(x, y)  # 70
{% endhighlight %}

To calculate the sum of squares of a vector, we can use the scalar product, like showing in **Figure 3**.

\\
**Figure 3:** Sum of squares of a vector.

<figure>
    <a href="/images/posts/2019-06-21-revisao-algebra-linear-python-03.png"><img src="/images/posts/2019-06-21-revisao-algebra-linear-python-03.png" alt="Sum of squares of a vector."></a>
</figure>

{% highlight python %}
x = np.array([1, 2, 3, 4])
prod = np.dot(x, x)  # 30
{% endhighlight %}

The result will be the sum of the multiplication of **x** vector by itself.

## Distance between vectors

In **Figure 4**, we have the vector **[1,4] (green square)** and the vector **[2,1] (blue square)**. If we add a new vector **[4,2] (red triangle)**, but we would like to classify this new vector, as the green or blue vector using the vector distance of other vectors. What is the square closest to the red triangle?

\\
**Figure 4:** Distance between vectors.

<figure>
    <a href="/images/posts/2019-06-21-revisao-algebra-linear-python-04.png"><img src="/images/posts/2019-06-21-revisao-algebra-linear-python-04.png" alt="Distance between vectors."></a>
</figure>

In **Figure 5**, we can see that the blue square is closest to the red triangle, so we can use this measure to classify the red triangle as being a blue square.

\\
**Figure 5:** Distance between vectors.

<figure>
    <a href="/images/posts/2019-06-21-revisao-algebra-linear-python-05.png"><img src="/images/posts/2019-06-21-revisao-algebra-linear-python-05.png" alt="Distância entre vetores."></a>
</figure>

One flavor to calculate the distance between vectors is using the **Euclidean Distance**, the equation is shown in **Figure 6**.

\\
**Figure 6:** Euclidean Distance.

<figure>
    <a href="/images/posts/2019-06-21-revisao-algebra-linear-python-06.png"><img src="/images/posts/2019-06-21-revisao-algebra-linear-python-06.png" alt="Euclidean Distance."></a>
</figure>

In **Figure 7**, we calculate the euclidean distance between the vector **[1,4] (green square)** with the vector **[4,2] (red triangle)**.

\\
**Figure 7:** Euclidean Distance between green square and red triangle.

<figure>
    <a href="/images/posts/2019-06-21-revisao-algebra-linear-python-07.png"><img src="/images/posts/2019-06-21-revisao-algebra-linear-python-07.png" alt="Euclidean Distance between green square and red triangle."></a>
</figure>

In **Figure 8**, we calculate the Euclidean distance between the vector **[2,1] (blue square)** with the vector **[4,2] (red triangle)**.

\\
**Figure 8:** Euclidean distance between blue square and red triangle.

<figure>
    <a href="/images/posts/2019-06-21-revisao-algebra-linear-python-08.png"><img src="/images/posts/2019-06-21-revisao-algebra-linear-python-08.png" alt="Euclidean distance between blue square and red triangle."></a>
</figure>

So, now we know that the shortest distance is in relation to the blue square.

Using Python, we can calculate the distance between vectors:

{% highlight python %}
gs = np.array([1,4])    # Green square
bs = np.array([2,1])    # Blue square
rt = np.array([4,2])    # Red triangle
{% endhighlight %}

With:

{% highlight python %}
distanceFromGreenSquare = np.sqrt(sum((qv - tv) ** 2)) #3.6055
distanceFromBlueSquare  = np.sqrt(sum((qa - tv) ** 2)) #2.2360
{% endhighlight %}

> The ** is used to calculate the left number raised to the right number. Sample: 5² is calculated with 5 ** 2.

# Matrixes

Matrix is a set of vectors, normally represented with a capital letter, like **A[m, n]** that have **m** lines by **n** columns. The NumPy has the **matrix** object that represents a matrix, example:

{% highlight python %}
A = np.matrix([[1, 2, 3, 4],
               [5, 6, 7, 8]])
{% endhighlight %}

In this example, we say that the matrix **A** has size **[2, 4]**, or two lines and four columns. To access a specific matrix position, we need to inform the value of line and column, remember that the index starts with **zero**, the value of position **A[1, 3]** é **8**.

## Sum of matrices

Sum of matrices:

{% highlight python %}
A = np.matrix([[1, 2, 3, 4], 
               [5, 6, 7, 8]])
B = np.matrix([[1, 2, 3, 4], 
               [5, 6, 7, 8]])

SUM = A + B
# [[ 2,  4,  6,  8],
#  [10, 12, 14, 16]]
{% endhighlight %}

## Transposed

Using Numpy we can easily get transposed from a matrix:

{% highlight python %}
A = np.matrix([[1, 2, 3, 4], 
               [5, 6, 7, 8]])
A.T    # [[1, 5],
       #  [2, 6],
       #  [3, 7],
       #  [4, 8]]
{% endhighlight %}

## Matrix multiplication

When we multiply two matrices, the matrix **A** has the dimension **m x n**, and matrix **B** has the dimension **n x m**, then with the result, we have a matrix with dimension **m x m**.

{% highlight python %}
A = np.matrix([[1, 2, 3, 4], 
               [5, 6, 7, 8]])
B = np.matrix([[1, 2],
               [3, 4], 
               [5, 6],
               [7, 8]])
mult = A * B  # [[ 50,  60],
              # [114, 140]]
{% endhighlight %}