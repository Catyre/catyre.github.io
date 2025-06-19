---
dg-home: false
dg-publish: false
---

##### Abstract
Exact methods for creating a 3D map of No Man's Sky's many galaxies in a 3D space outside of the game has proven difficult given the little access players have to the game's programming.  This document explores methods of mapping out the galaxies of NMS (for now, just Calypso Hubspace) given just the information we can gather from normal gameplay.  There are some interesting challenges to such a problem that I think are tackleable.

##### Problem description
Given two vectors $\vec{r}_1$ and $\vec{r}_2$ in galactic coordinates $(r_{1}, \theta_{1}, z_{1})$  and $(r_{2}, \theta_{2}, z_{2})$ representing two star systems, how can we create a to-scale and reliable mapping of the No Man's Sky galaxy map in a third party software to render and use as reference.  The goal here is to be able to deduce accurate coordinates of any second (then third, then fourth, etc.) system in a coordinate frame centered around a given anchor system.  Mathematically, we are looking for a method to convert $(r,\theta,z)$ centered around the galactic core (call it "galactic coordinates") to $(x, y, z)$ centered on our anchor star (anchored coordinates).

##### Law of cosines
![Reference for law of cosines](images/law_of_cosines.png)
In this picture, A is the galactic core, B is the anchor system, and C is the guidestar.  The whole triangle is from a side view of the galaxy (with proportions exaggerated...in-game, c and b are about 1000x larger than a).  a, b, and c are given in-game.  These are $|\vec{r'}|$, $|\vec{r}_2|$, and $|\vec{r}_1|$ respectively.  $\beta$ is the angle needed for our eventual conversion to anchored coordinates.  The law of cosines says that:

$$b^2=a^2+c^2-2ac\cos{\beta}\implies\cos\beta=\frac{a^2+c^2-b^2}{2ac}$$

So, 

$$\beta = \cos^{-1}\left(\frac{a^2+c^2-b^2}{2ac}\right)$$

or, in terms of the variables we defined, 

$$\boxed{\beta = \cos^{-1}\left(\frac{r'^2+r_1^2-r_2^2}{2r'r_1}\right)}$$

If we define the $\theta$ axis such that the line c is $0,\ 2\pi$, then the six coordinates (one for the core, anchor, and guide in both galactic and anchored coordinates) so far are:

| Spatial object | Galactic Coordinates         | Anchored Coordinates                   |
| -------------- | ---------------------------- | -------------------------------------- |
| Galactic core  | $\vec{0}$                    | $\langle r_{1,}0, 0>$                  |
| Anchor star    | $\langle r_{1}, 0, 0\rangle$ | $\vec{0}$                              |
| Guidestar      | $\langle r_2, 0, z\rangle$         | $\langle r'\cos\beta, 0, r'\sin\beta\rangle$ |

##### The Cartographic Method
1. Decide a center "anchor star" for local mapping (in the diagram below, point B)
2. Note the distance from galactic core to the anchor star, $|\vec{r}_1|$
3. Choose a second system on the plane formed by keeping $\phi$ and $r$ constant but allow $\theta$ to change.  This star will be called the guidestar
4. Note distance from core to the guidestar (this is $|\vec{r}_2|$)
5. Note the distance between the anchor star and the guidestar.  This is $|\vec{r'}|$
6. Use the equations below to determine $\beta$
7. Repeat steps 3-6 except let $r$, $\theta$ and $z$ change for the new third system (in other words, pick any other star nearby)
8. Convert second and third system coordinates to cartesian, with the origin being the anchor system

Although not a coordinate, the magnitude of the vector pointing from anchor to guidestar ($|\vec{r'}|$) should also be recorded.

Assuming we know all of these values, we now have two different vectors in two different coordinates systems (a cylindrical one centered on the galactic core, and a cartesian one centered on the anchor system)

#### Sample data (WIP)
We will use a random star in the Calhubspace as an example/proof of concept.  See the diagrams below for more clear visual explanations of this method.

![[Diagram 1.png]]
It is not clear in the picture due to perspective, however this triangle is, in the ideal case, entirely along the z-axis.  This greatly simplifies calculations later when we are building the anchored coordinate system
![[Diagram 2.png]]

![[example 1.png|960x540]]
$r_1$ = 671,330 LY

<hr>

Note: Tried to keep $\theta_1 = \theta_2$ valid by looking straight up relative to galactic plane and traveling until I hit another star
![[example 2.png|960x540]]
$r'$ = 400 LY
$r_2$ = 670,942 LY

Using the equations in the explanation above, that gives us that $\beta = 0.2455$ radians or $14.065$ degrees.  This means that in anchored coordinates, this guidestar has a position of $<388.007, 0, 97.213>$ LY.  As a check that things at least make numerical sense, the magnitude of this vector should match the distance we are given in-game:

$$|<388.007, 0, 97.213>| = \sqrt{388.007^2 + 97.213^2}$$
$$= 399.999...$$
(close enough)

Okay, so we have the coordinates of two stars in a coordinate system centered on any star of our choosing, so what?

What this means is that the position of any other star (within margin of error) in the nearby area can be mapped onto this anchored coordinate system, which means we have numbers that we can give a third-party program to make a rendering of a local map.

$$r'=83LY$$
$$r_1=400LY$$
$$r_2=320LY$$