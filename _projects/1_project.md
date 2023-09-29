---
layout: page
title: Ray Tracing Engine
description: A backward ray tracing algorithm in Python 
img: assets/img/raytracer_site_2.png
importance: 
category: work
related_publications: 
---
[GitHub Repo](https://github.com/JakeTaranov/RayTracer)


A forward ray tracing algorithm computes the ray of lights that follow from the source of the light to the objects and ultimately to the eye (light source to eye). The drawback of this approach is due to the fact that we must compute the light rays from each light source to each of the objects in the scene, doing this is redundant as there are many rays that never come from the view plane into the eye. 

A backward ray tracing algorithm is a much more efficient approach to solving this problem. Using a backward approach we compute all the rays of light that enter the eye only (eye to light source) rather than computing all the rays of light that come from a light source (forward approach). We do this through casting rays from the eye and computing all intersections with objects and the resulting rgb color vectors from the interacting light sources, along with the diffuse and specular properties of the respected object.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/raytracer_site_2.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/raytracer_site_3.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/raytracer_site.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Example output scenes.
</div>

The low level pseudocode is as follows:
```
raytrace(ray):

    P = compute_closest_intersection(ray)
    color_local = shadow_ray(light1 , P) + ... + shawdow_ray(lightN, P)
    color_reflect = raytrace(reflected_ray)
    color = color_local + k_re * color_reflect
```

### Computing the Closest Intersection

We begin computing the closest intersection by first computing the inverse transform ray. 

$$ M^{-1}\begin{pmatrix} S_x \\ S_y \\ S_z \end{pmatrix}
+tM^{-1}\begin{pmatrix} c_x \\ c_y \\ c_z \end{pmatrix}  = \tilde{S}' + \tilde{c}'t$$

Where $$M^{-1}$$ is the inverse of the object, $$S_x, S_y, S_z$$ are the coordinates of the ray origin, $$c_x, c_y, c_z$$ is the direction of the ray, and $$t$$ is a scalar value.

Using the quadratic formula we can now determine the intersections and take the minimum value from our results using the closest intersection.

$$ |c|^2t^2 + 2(S\cdot c)t + |S|^2 = 0 $$ 

$$ At^2 + Bt + C = 0 $$

$$ \implies $$

$$ t_h = -\frac{B}{A} \pm \frac{\sqrt{B^2-AC}}{A} $$

$$ t_h = \frac{S \cdot c}{|c|^2} \pm \frac{\sqrt{(S \cdot c)^2 - |c|^2|S|^2}}{|c|^2} $$

Once we find the values for $$ t_h $$ we must take the minimum of all values to determine the distance of the closest intersection.

Given the closest intersection $$ t_h $$ we can then use this value in original untransformed ray $$ S+ct $$ to find the point of intersection.

### Computing the Shadow Ray

For each closest intersection found, we now must compute the shadow rays. This is done with respect to the intersected point, we compute the closest intersections with all the light sources in the scene. Only if there is no intersection with the light sources is when we want to apply the lighting from the given source. 

In the case when there is a light source without any intersections near by we can compute the light at the given point with respect to the light source, ambient light, and specular and diffuse properties of the object the light source is projecting onto along with the light itself. 

The resulting formula comes in the form

$$ ObjectColor{_r} = I_{a,r}K_{a,r} + I_{i,r}K_{diff,r}(N \cdot L) + I_{i,r}K_{spec,r}(R \cdot V)^n $$

$$ ObjectColor{_g} = I_{a,g}K_{a,g} + I_{i,g}K_{diff,g}(N \cdot L) + I_{i,g}K_{spec,g}(R \cdot V)^n $$

$$ ObjectColor{_b} = I_{a,b}K_{a,b} + I_{i,b}K_{diff,b}(N \cdot L) + I_{i,b}K_{spec,b}(R \cdot V)^n $$

Where $$ I_{a,c}K_{a,c} $$,  $$ I_{i,c}K_{diff,c}(N \cdot L) $$ and $$ I_{i,c}K_{spec,c}(R \cdot V)^n $$ are ambient light and properties, diffuse light and properties, and specular light and properties respectively. And $$ K_a $$, $$ K_{diff} $$, $$ K_{spec} $$, and $$ n $$ are object or material properties and $$ I_a $$, $$ I_{diff} $$ and $$ I_{spec} $$ are properties of the light.

$$ (N \cdot L) $$ represents the angle between the light direction vector ($$ L $$) and the surface norm ($$ N $$), and $$ (R \cdot V) $$ represents the angle between the viewer ($$ V $$) and the light source reflected on the surface ($$ R $$). More details on ambient light can be found [here](http://www.conitec.net/shaders/shader_work2.htm). And more details on specular light can be found [here](https://en.wikipedia.org/wiki/Blinn%E2%80%93Phong_reflection_model).


### Repeat with Reflected Rays

This algorithm is inherently recursive, as for every reflected ray from as a result of an intersection we must now trace that the respected ray and compute the results it has on the final rgb values of a given pixel. We can technically have rays of light that will reflect off objects an infinite amount of times. From a pragmatic standpoint, this is very detrimental to our algorithm. In my program I have chosen to set a limit of 3 reflections per ray, I experimented with greater values but the increase in image realism does not scale linearly with the increase in run time as a result. 

### Conclusion 

This explanation of my program is not complete and I am aware of this. I simply wanted to make a brief introductory article to help explain some of the fundamental math in the program. My goals were to convey some of the main takeaways I personally found from this project, that being how we find the intersections of rays and objects, and how we calculate the rgb values at a given pixel. I hope this is enough to inspire you to learn more or just give you enough support to better understand my [repository](https://github.com/JakeTaranov/RayTracer).