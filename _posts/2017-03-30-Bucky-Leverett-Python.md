---
layout: post
title: Buckley Leverett analysis in Python
comments: true
---
The Buckley-Leverett partial differential equation is:

$$ -\frac {df_w}{dS_w} \frac{\partial S_w}{\partial x} = \frac {A\phi}{q}\frac{\partial S_w}{\partial t} $$

As $$ S_w(x,t) $$ we can write the total derivative as:
$$ dS_w = \frac{\partial S_w}{\partial x}dx + \frac{\partial S_w}{\partial t}dt $$

and, given that the fluid front is of constant saturation in the analysis, we can say: 


$$ 0 = \frac{\partial S_w}{\partial x}dx + \frac{\partial S_w}{\partial t}dt $$

Substituting this into the Buckley-Leverett equation, gives:

$$ \frac{dx}{dt} = \frac{q}{A\phi}\frac{df_w}{dS_w} $$

Integration of both sides over time $$ t=0 $$ to $$ t=t $$:




test code

```py
import numpy as np;
```