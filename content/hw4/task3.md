+++
title = "Direct Lighting"
template = "page.html"
[extra]
mathjax = "tex-mml"
+++

## Collisions with Spheres

To implement sphere collision, we followed the design in the spec. For a point mass, if it ever ends up within the sphere, we calculate where it should be if the sphere displaced it along a ray drawn from the center of the sphere to the point mass to a point just outside of the sphere, then we apply this correction vector to the point mass. The results of this are demonstrated below, with springs constants $ks = 5000, 500,$ and $50000$.

<p style="text-align:center">
<img src="./sphere5000.png" style="width:30%">
<img src="./sphere500.png" style="width:30%">
<img src="./sphere50000.png" style="width:30%">
<br>
Left: a cloth with $ks=5000$ draped over a sphere. 
Center: a cloth with $ks=500$ draped over a sphere. 
Right: a cloth with $ks=50000$ draped over a sphere. 
</p>

Notice how in the above pictures, a higher spring constant is associated with a "stiffer cloth" — the cloth with the lowest $ks$ value is very flexible and drapes over the sphere very conformally. Comparing this to the higher spring constant cloth, we see that it sticks out more from the sphere and resists bending more.

## Collisions with Planes

Similarly to sphere collision, to implement plane collision, if at any time step a point mass crosses the plane, we displace it in the direction of its previous location in the direction of the plane normal. An image of our cloth resting on the plane is below:

<p style="text-align:center">
<img src="./plane.png" style="width:30%">
</p>

Overall, this part of the project went smoothly, and we didn't have any major bugs. The one bug we encountered was doing some incorrect math during plane collisions that ended up incorrectly applying friction to the cloth, which was offended by our clearly incorrect math and promptly departed towards infinity, never to be seen again. Once we fixed our math, the cloth was much happier laying at rest near the origin of our space.
