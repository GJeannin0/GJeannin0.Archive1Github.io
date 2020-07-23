# Frustum Culling

My name is Guillaume Jeannin, I'm a student at the SAE Institute of Geneva in Games Programming.
For the computer's graphics module, our class worked on creating a Minecraft like in a custom game engine in C++.
In this project, my mission was to implement a Frustum Culling system.

![Frustum](/images/frustum.png)

# Minecraft like Context

![Chunks](/images/mclike.jpg)

In our Minecraft like game, the player plays in an endless open world where the terrain is procedurally generated.
The terrain is composed of blocks and subdivided in several cubic chunks.
The place these chunks and blocks take in space is defined in an axis aligned bounding boxe or AABB struct.

In this context it is important to save performance, one way to do it is by rendering only what's in the player's field of view.

![FOV](/images/fov.jpg)

The shape of the field of view is this kind of truncated cone shape, and it's called a frustum.

# The Frustum

To represent the frustum, I coded a struct defining a plane by a point and its normal, and a struct containing the six different planes that are the faces of the frustum.
To know wich side of the plane is outside of the frustum, the normal of each plane always points towards inside of the frustum.
I generate it by getting the values defining the field of view from the camera.

![Camera](/images/cam.png)

I then calculate the requiered vertices positions and define each plane with the three corresponding vertices.

# Culling outside of the Frustum

To cull blocks and chunks that are outside of the frustum, we check whether they are inside or outside of the frustum before rendering them.
To determine if they're inside, we use the separated axis theorem or SAT, which states that if it is possible to draw a plane between two convex polytopes, these polytopes do not intersect eachother.
We check if the AABB of the block or chunk instersects the frustum.

To do this, we calculate on which side of each plane the AABB is, and if it is inside every plane, it is inside of the frustum.
The method consists of calculating the minimal signed distance between each plane and the AABB.
By using a projection on the normal of the plane, we determine on which side of the plane the AABB is.

# Conclusion

Working on this project was a good experience.
How to do frustum culling wasn't obvious to me at the first look, but I had the time I needed to figure it out. 
I learned how to do Frustum culling and gained some experience in group projects.

