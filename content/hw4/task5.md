+++
title = "Shaders"
template = "page.html"
date = 2024-02-28T00:00:00Z
[extra]
mathjax = "tex-mml"
+++

## Shaders — Fragments and Vertices

A shader is a program that runs in parallel on the GPU and renders our scene quickly. The pipeline that we learned about and used involved **vertex** and **fragment** shaders. A vertex shader takes in constants and information about each vertex from our scene, and outputs information about this vertex to the fragment shader, and also has the ability to modify scene geometry, which we used for the displacement shader. The fragment shader takes as input information from the vertex shader, and operates after the rasterization phase, outputting a color to be drawn to the screen.

## Blinn-Phong Shading

The Blinn-Phong model of shading can be succinctly expressed by the below equation:

$$
\color{black} \mathbf L = \underbrace{\mathbf k_a \mathbf I_a}\_\text{ambient} 
	+ \underbrace{\frac{\mathbf k_d \mathbf I}{r^2} \operatorname{max}(0, \mathbf n \cdot \mathbf l)}\_{\text{diffuse}} 
	+ \underbrace{\frac{\mathbf k_s \mathbf I}{r^2} \operatorname{max}(0, \mathbf n \cdot \mathbf h)^p}\_{\text{specular}}
$$

In the above equation, $\mathbf I, \mathbf n$ and $\mathbf l$ are passed in as uniforms (similar to constants) to the shader, $r$ is computed as the distance from the vertex to the light, and $\mathbf h = \frac{\mathbf v + \mathbf l}{|| \mathbf v + \mathbf l||}$ is the bisector of the camera position from the vertex and the light direction from the point. We then used the lighting value times the color value passed in as a uniform to render the final color of the fragment.

The remaining values $\mathbf k_a, \mathbf I_a, \mathbf k_d, \mathbf k_s$, and $p$ were chosen by us with the following values:

$$
\color{black} \mathbf k_a \mathbf I_a = (0.1, 0.1, 0.1, 1) \qquad \mathbf k_d = (2, 2, 2, 1) \qquad \mathbf k_s = (3, 3, 3, 1) \qquad p = 300
$$

Below, we demonstrate the 3 components, as well as the sum of the three components together.

<p style="text-align:center">
<img src="./phong_ambient.png" style="width:30%">
<img src="./phong_diffuse.png" style="width:30%">
<img src="./phong_specular.png" style="width:30%">
<br>
Left: the ambient component of our Blinn-Phong shader.
Center: the diffuse component.
Right: the specular component.
</p>

<p style="text-align:center">
<img src="./phong.png" style="width:50%">
<br>
The sum of all three above components together
</p>

## Texture Mapping

Similar to what we have seen before in the class, we can use textures as well in our shading. Given a vertex, the vertex shader receives as input a $uv$ coordinate for the texture which is directly outputted to the fragment shader. Below, we used a custom texture from something we drew in the past, and mapped it onto the cloth and sphere, shown below: 

<p style="text-align:center">
<img src="./texture_1.png" style="width:25.22%">
<img src="./custom_texture_sphere.png" style="width:30%">
<img src="./custom_texture.png" style="width:30%">
<br>
Left: the texture we used.
Center: the result of our texture shader before running any simulation steps.
Right: the result of out texture shader after the cloth has been draped over the sphere.
</p>

## Bump and Displacement Shading

For bump and displacement shading, we followed the spec. We used the red-channel heuristic to estimate the height of a pair of $uv$ coordinates, and then followed the spec to compute new normals and displaced vertex positions.

We ended up using a publicly available brush stroke texture to demonstrate our bump and displacement shading. The texture we used is shown below:

<p style="text-align:center">
<img src="./texture_2.png" style="width:25.22%">
</p>

Below, we show low and high quality renders (rendered with `-o 16 -a 16` and `-o 128 -a 128`)

<p style="text-align:center">
<img src="./sphere_bump_16.png" style="width:20%">
<img src="./cloth_bump_16.png" style="width:20%">
<img src="./sphere_displacement_16.png" style="width:20%">
<img src="./cloth_displacement_16.png" style="width:20%">
<br>
<img src="./sphere_bump_128.png" style="width:20%">
<img src="./cloth_bump_128.png" style="width:20%">
<img src="./sphere_displacement_128.png" style="width:20%">
<img src="./cloth_displacement_128.png" style="width:20%">
<br>
Above left: the sphere and cloth rendered with a bump shader at low quality. Above right: displacement shader at low quality. Below left: bump shader at high quality. Below right: displacement shade at high quality.
</p>

To generate these images, we chose rather extremal values for normal and height scaling to appropriately demonstrate displacement shading. Visually, one can see that both the sphere and cloth have had their vertices physically displaced and geometries altered to be more inline with the texture, whereas the bump shading only offers the illusion of texture, but no actual change in the geometry. 

When rendering with higher mesh qualities, we see more granularity in the detail shown by both renders. This is most notable in the displacement shading, where the low height divots in the sphere have differing shapes, due to a higher quality allowing for more detailed resolution of the height map. Additionally, notice the jagged angles present in the low resolution rendering of the sphere, and how they are absent in the higher quality of the sphere due to the difference in quality. You can also observe how the lighting seems to be more accurate compared to the texture in the bump shading as the quality improves — the striations in the brush strokes are clearly visible in the specular reflections when the quality is high, compared to how they are mostly glossed over when the quality is low.

## Mirror Shading

Lastly to implement mirror shading, we cast a ray from the camera to the object, then reflected it across the object's surface normal to obtain an outbound ray, and then sampled the environmental cubemap. A diagram of this approach is shown below:

<p style="text-align:center">
<img src = "./mirror.png" style="width:30%">
</p>

This approach takes considerable shortcuts when it comes to geometry and obstruction, but nonetheless produces surprisingly good results for how simplistic the model is, and most importantly, renders quite quickly — I was able to maintain 90 frames per second with this shader. The results of this mirror shader is shown below:

<p style="text-align:center">
<img src="./mirror_sphere.png" style="width:40%">
<img src="./mirror_cloth.png" style="width:40%">
<br>
Left: the sphere rendered with a mirror shader. Right: the cloth draped over the sphere with a mirror shader.
</p>

## Debugging woes

Overall, the process for implementing this part of ht project was very straightforward, with a few hiccups. The first hiccup was not knowing what the coefficients for Phong shading did, and choosing values that ended up creating a shader that was majority diffuse lighting. We were convinced that our implementation was incorrect for the longest time, as it looked "not shiny enough" until we increased the $p$ value sufficiently to create good looking specular highlights. The second issue we ran into was a reading comprehension problem, where when implementing the bump shader, we discovered (after about an hour) that in general,
$$
u + \frac{1}{w} \neq \frac{u + 1}{w}.
$$

Overall, this part of the project was very interesting, and it was very impressive to us that this pipeline could render images this quickly in real time, especially compared to our previous project where each render would take several minutes for one frame.



