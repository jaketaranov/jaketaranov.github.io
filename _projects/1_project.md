---
layout: page
title: Ray Tracer in Python
description: A backward ray tracing algorithm in Python
img: assets/img/raytracer_site_2.png
importance: 1
category: work
related_publications: 
---

A forward ray tracing algorithm computes the ray of lights that follow from the source of the light to the objects and untimatley to the eye (light source to eye). The drawback of this approach is due to the fact that we must compute the light rays from each light source to each of the objects in the scene, doing this is redunant as there are many rays never come from the view plane into the eye. 

A backward ray tracing algorithm is a much more effecient approach to solving this problem. Using a backward approach we compute all the rays of light that enter the eye only (eye to light source) rather than computing all the rays of light that come from a light source (forward approach). We do this through casting rays from the eye and computing all intersections with objects and the resulting rgb color vectors from the interacting light sources, along with the diffuse and specular properties of the respected object.

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

The low level pseucode is as follows:
```
raytrace(ray):

    P = compute_closest_intersection(ray)
    color_local = shadow_ray(light1 , P) + ... + shawdow_ray(lightN, P)
    color_reflect = raytrace(reflected_ray)
    color = color_local + k_re * color_reflect
```

### Computing the Closest Intersection

We begin computing the closest interection by first computing the inverse transform ray. 

$$ M^{-1}\begin{pmatrix} S_x \\ S_y \\ S_z \end{pmatrix}
+tM^{-1}\begin{pmatrix} c_x \\ c_y \\ c_z \end{pmatrix}  = \tilde{S}' + \tilde{c}'t$$

Where $$M^{-1}$$ is the inverse of the object, $$S_x, S_y, S_z$$ are the coordinates of the ray origin, $$c_x, c_y, c_z$$ is the direction of the ray, and $$t$$ is a scalar value.

Using the quadratic formula we can now determine the interesctions and take the minimum value from our results use the closest intersection.

$$ |c|^2t^2 + 2(S\cdot c)t + |S|^2 = 0 $$ 

$$ At^2 + Bt + C = 0 $$

$$ \implies $$

$$ t_h = -\frac{B}{A} \pm \frac{\sqrt{B^2-AC}}{A} $$

$$ t_h = \frac{S \cdot c}{|c|^2} \pm \frac{\sqrt{(S \cdot c)^2 - |c|^2|S|^2}}{|c|^2} $$

Once we find the values for $$ t_h $$ we must take the minumum of all values to determine the distance of the closest intersection.

Given the closest intersection $$ t_h $$ we can then use this value in original untransformed ray $$ S+ct $$ to find the point of intersection.

### Computing the Shadow Ray

For each closest intersection found, we now must compute the shadow rays. This is done by with resepect to the intersected point, we compute the closest intersections with the all the light sources in the scene. Only if there is no intersection with the light sources is when we want to apply the lighting from the given source. 

In the case when there is a light source without any intersections near by we can compute the light at the given point with respect to the light source, ambient light, and specular and diffuse properties of the object the light source is projecting onto. 

The resulting forumla comes in the form

$$ ObjectColor{_r} = I_{a,r}K_{a,r} + I_{i,r}K_{diff,r}(N \cdot L) + I_{i,r}K_{spec,r}(R \cdot V)^n $$

$$ ObjectColor{_g} = I_{a,g}K_{a,g} + I_{i,g}K_{diff,g}(N \cdot L) + I_{i,g}K_{spec,g}(R \cdot V)^n $$

$$ ObjectColor{_b} = I_{a,b}K_{a,b} + I_{i,b}K_{diff,b}(N \cdot L) + I_{i,b}K_{spec,b}(R \cdot V)^n $$

Where $$ I_{a,c}K_{a,c} $$,  $$ I_{i,c}K_{diff,c}(N \cdot L) $$ and $$ I_{i,c}K_{spec,c}(R \cdot V)^n $$ are ambient light and properties, diffuse light and properties, and specular light and properties respectivley. And $$ K_a $$, $$ K_{diff} $$, $$ K_{spec} $$, and $$ n $$ are object or material properties and $$ I_a $$, $$ I_{diff} $$ and $$ I_{spec} $$ are properties of the light.

$$ (N \cdot L) $$ represents the angle between the light direction vector ($$ L $$) and the surface nor ($$ N $$), and $$ (R \cdot V) $$ represents the angle between the viewer ($$ V $$) and the light source reflected on the surface ($$ R $$). More details on ambient light can be found [here](http://www.conitec.net/shaders/shader_work2.htm). And more details on specular light can be found [here](https://en.wikipedia.org/wiki/Blinn%E2%80%93Phong_reflection_model)



You can also put regular text between your rows of images.
Say you wanted to write a little bit about your project before you posted the rest of the images.
You describe how you toiled, sweated, *bled* for your project, and then... you reveal its glory in the next row of images.



<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>


The code is simple.
Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system).
To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes.
Here's the code for the last row of images above:

{% raw %}
```html
<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
```
{% endraw %}
