---
layout: post
title: Buckley Leverett analysis in Python
comments: true
---
I thought it would be interesting to write some functions to perform a Buckley-Leverett analysis using python and matplotlib.

### The theory 

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

$$ x_f = \frac{q t}{A \phi}(\frac{df_w}{dS_w})_f $$

As we know that the fractional flow in the simple case of horizontal flow can be calculated as:

$$ f_w = \frac{1}{1+\frac{k_{ro}}{\mu_o}\frac{\mu_w}{k_{rw}}} $$

we can plot this, along with it's derivative $$ \frac{df_w}{dS_w} $$ calculated numerically:

![fracflow ]({{ site.baseurl }}assets/fig1.png){: .center-image }

To determine the water saturation at the shock front, we can construct chords starting at the connate water saturation, working our way up the fractional flow curve until we find the maximum gradient - this point gives us the shock saturation. In this case, $$ S_w = 0.81 $$:
![satfront ]({{ site.baseurl }}assets/fig2.png){: .center-image }

Then we can plot the saturation as a function of the position $$ x_f $$. 
![sat-dist]({{site.baseurl}}assets/fig3.png){: .center-image }

The curve suggests there exists two saturation solutions at each distance step, which is not physically possible. The shock front  saturation is a discontinuity, and this can be used to modify the plot above to yield a profile of the saturation behind and in front of the saturation front.
![sat-dist]({{site.baseurl}}assets/fig4.png){: .center-image }

* $$ S_{w i} $$ represents the initial water saturation, where the shock front has not yet reached
* $$ S_{w \ shock} $$ represents the water saturation at the shock front
* $$ 1-S_{or} $$ represents the water saturation behind the shock front, after the saturation front has swept through, this location is left at the residual oil saturation, $$ S_{or} $$ . 

### The code 

The Buckley-Leverett class contains the relevant attributes for each instance, including the permeabiliy end points which can be used to generate the relative permeability curves using Corey type relationships.


```py
class BuckleyLev():
    
    def __init__(self):
        self.params = {
            #non wetting phase viscosity
            "viscosity_o": 1.e-3,
            #wetting phase viscosity
            "viscosity_w": 0.5e-3,
            #initial water sat
            "initial_sw":0.4,
            #irreducable water saturation, Swirr
            "residual_w":0.1,
            #residual oil saturation, Sor
            "residual_o":0.1,
            #water rel perm at water curve end point
            "krwe":1,
            #oil rel perm at oil curve end point
            "kroe": 0.9,
            #porosity
            'poro':0.24,
            #water injection rate units m3/hr
            "inject_rate":200,
            #cross sectional area units m2
            "x-area":30
        }
```

Using the these instance attributes we can then add methods to the class for calculating the fractional flow:

```py
def fractional_flow(self,sw):
    #returns the fractional flow
    
    return 1./(1.+((self.k_rn(sw)/self.k_rw(sw))*(self.params["viscosity_w"]/self.params["viscosity_o"])))

BuckleyLev.fractional_flow = fractional_flow
```

and for calculating the derivative numerically a finite difference approximation:

```py
def fractional_flow_deriv(self,sw):
    #calculate derivative of fractional flow - dFw/dSw - Vsh
    
    f_deriv = (self.fractional_flow(sw+0.0001) - self.fractional_flow(sw))/0.0001
    
    return f_deriv

BuckleyLev.fractional_flow_deriv = fractional_flow_deriv
```
The full code is available here

By defining the attirbutes in this way, we can also visualise at the effects of evaluating the position of the shock as it progresses at different time steps, as follows:

![fracflow ]({{ site.baseurl }}assets/fig5.png){: .center-image }

Another interesting property is the actual proerties of the fluids involved in the displacement. The mobility ratio is defined as $$ \frac{k_{ro}}{\mu_o} $$. For a higher viscosity oil, the mobility ratio is lower. We can see the effects of this in the below figure - a more viscous oil means the water brekthrough occurs earlier. 

![fracflow ]({{ site.baseurl }}assets/fig7.png){: .center-image }
