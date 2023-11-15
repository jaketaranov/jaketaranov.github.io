---
layout: page
title: C++ Orbital Simulation
description: Orbital simulation of celestial bodies
img: assets/img/sim_ss.png
importance: 2
category: work
related_publications: 
---
[Github Repo](https://github.com/JakeTaranov/oribtsim)


In an effort to learn C++ with SFML I undertook the task of designing and developing a program to simulate and display the orbits of celestial bodies. 


<div class="row" align="center">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.html path="assets/video/sim_1.mp4" title="Simulation Example 1" class="img-fluid rounded z-depth-1" controls=true %}
    </div>
</div>
<div class="caption">
    A simulation of two bodies orbiting each other. 
</div>

I have always had a natural curiosity with space, and I was fortunate enough to explore this curiousity with some electivates taken at UVIC (ASTR 101, 102). Along with my ambition to learn C++, this project was a great learning experience for both.

## Input
The program reads in a json file specified via command line arguments that specifies the bodies and other parameters in the simulation. 


```json
{
  "bodies": [
    {
      "mass": 10000,
      "radius": 10,
      "pos_x": 600,
      "pos_y": 400,
      "vel_x": 0.5,
      "vel_y": 1.5,
      "drawline": true
    },
    {
      "mass": 10000,
      "radius": 10,
      "pos_x": 1000,
      "pos_y": 450,
      "vel_x": -0.5,
      "vel_y": -1.5,
      "drawline": true
    }
  ],
  "stay_in_bounds": true
}
```
<div class="caption">
    example taken from <em>stable_orbit.json</em>
</div>

<ul>
  <li> <strong> mass </strong>: mass of body n (int) </li>
  <li> <strong> radius </strong>: radius of body n (int) </li>
  <li> <strong> pos_x </strong>: initial x position of body n (int) </li>
  <li> <strong> pos_y </strong>: initial y position of body n (int)</li>
  <li> <strong> vel_x </strong>: initial x velocity of body n (int) </li>
  <li> <strong> vel_y </strong>: initial y velocity of body n (int) </li>
  <li> <strong> drawline </strong>: determines if the program will draw a trailing line of the body n's previous movement (bool) </li>
  <li> <strong> stay_in_bounds </strong>: determines wether all the bodies will stay in the screen bounds by boncing off the edges (bool)</li>
</ul>

## Updating the Bodies Positions and Velocities

The main loop responsible for updating the current bodies position and velocity works by:

<ol>
    <li> fetching all the other bodies except current body we want to update </li>
    <li> updating the position and velocity with respect to all other bodies using Newton's law of gravitational attraction </li> 
    <li> updating the visual aspects of the simulation (body, lines, bounces off edges, etc.) </li>
</ol>

```c++
void Body::update_physics(std::vector<Body> other_bodies)
{
    // Loop through all other bodies in the system
    for (int i = 0; i < other_bodies.size(); i++) {

        // Get information about the current other body
        Body other_body = other_bodies[i];
        Vec other_pos = other_body.get_pos();

        // Calculate the direction of the force acting on the current body
        Vec force_dir = pos.subtract(other_pos);

        // Calculate the distance between the current body and the other body
        float distance = force_dir.get_magnitude();

        // Constrain the distance to avoid division by zero or other numerical issues
        distance = constrain(distance, (float)Constants::constrain_lower, (float)Constants::constrain_upper);

        // Calculate gravitational force using Newton's law of universal gravitation
        float gForce = (Constants::G * other_body.mass) / pow(distance, 2);

        // Calculate the angle of the force direction vector
        float ang = force_dir.getAngle();

        // Calculate the acceleration components in x and y directions
        float m_acc_x = gForce * cos(ang);
        float m_acc_y = gForce * sin(ang);

        // Update the velocity of the current body based on the calculated acceleration
        vel.add_to_x(m_acc_x * Constants::dt);
        vel.add_to_y(m_acc_y * Constants::dt);

        // Update the position of the current body based on the updated velocity
        pos.add_to_x(vel.get_x());
        pos.add_to_y(vel.get_y());
    }
}
```
<div class="caption">
    <em>update_physics</em> function responsible for updating the physics of body <em> n </em> with respect to all other bodies
</div>

<div class="row" align="center">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.html path="assets/video/sim_3.mp4" title="Simulation Example 2" class="img-fluid rounded z-depth-1" controls=true %}
    </div>
</div>
<div class="caption">
    A simulation of three bodies orbiting each other. 
</div>
