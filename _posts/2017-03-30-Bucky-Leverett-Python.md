---
layout: post
title: Buckley Leverett analysis in Python
comments: true
---
<p> I thought it would be interesting to write some functions to perform a Buckley-Leverett analysis using python and matplotlib. </p>

The Buckley-Leverett partial differential equation is:

$$ -\frac {df_w}{dS_w} \frac{\partial S_w}{\partial x} = \frac {A\phi}{q}\frac{\partial S_w}{\partial t} $$

As $$ S_w(x,t) $$ we can write the total derivative as:

$$ dS_w = \frac{\partial S_w}{\partial x}dx + \frac{\partial S_w}{\partial t}dt $$

and, given that the fluid front is of constant saturation in the analysis, we can say: 


$$ 0 = \frac{\partial S_w}{\partial x}dx + \frac{\partial S_w}{\partial t}dt $$

Substituting this into the Buckley-Leverett equation, gives:

$$ \frac{dx}{dt} = \frac{q}{A\phi}\frac{df_w}{dS_w} $$

Integration of both sides over time $$ t=0 $$ to $$ t=t $$:

$$ \int_{t=0} ^{t=t} \frac{dx}{dt}dt = \int_{t=0} ^{t=t}\frac{q}{A\phi}\frac{df_w}{dS_w}dt $$

gives an expression for the position of the fluid front $$ x_f $$ at time $$ t $$:

$$ x_f = \frac{q t}{A \phi}(\frac{df_w}{S_w})_f $$

As we know that the fractional flow in the simple case of horizontal flow can be calculated as:

$$ f_w = \frac{1}{1+\frac{k_ro}{\mu_o}\frac{mu_w}{k_rw}} $$

we can plot this, along with it's derivative $$ \frac{df_w}{dS_w} $$ calculated numerically:

![fracflow ]({{ site.baseurl }}assets/fig1.png){: .center-image }

To determine the water saturation at the shock front, we can construct chords starting at the connate water saturation, working our way up the fractional flow curve until we find the maximum gradient - this point gives us the shock saturation. In this case, $$ S_w = 0.81 $$:
![satfront ]({{ site.baseurl }}assets/fig2.png){: .center-image }

```py
import numpy as np;
```