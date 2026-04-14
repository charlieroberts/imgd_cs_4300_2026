We'll begin with a basic raymarching setup. This setup will contain no lighting (our "sphere" will look like a circle), but it's enough to get something up and running.

[Raymarching scaffolding in The Schwartz](https://charlieroberts.codeberg.page/TheSchwartz/?Y29uc3QgTUFYX0RJU1QgOiBmMzIgPSAxMC47IApjb25zdCBNSU5fRElTVCA6IGYzMiA9IC4wMDU7IAoKZm4gc2NlbmUoIHBvaW50OiB2ZWMzZiApIC0+IGYzMiB7CQogIHJldHVybiBzcGhlcmUoIHBvaW50LCAuNzUgKTsKfQoKZm4gc3BoZXJlKCBwb2ludDp2ZWMzZiwgcmFkaXVzOmYzMiApIC0+IGYzMiB7CiAgcmV0dXJuIGxlbmd0aChwb2ludCkgLSByYWRpdXM7Cn0KCmZuIHJheW1hcmNoKHJheW9yaWdpbiA6IHZlYzNmLCByYXlkaXJlY3Rpb246IHZlYzNmICkgLT4gZjMyIHsKICB2YXIgdG90YWxkaXN0YW5jZSA9IDAuMDsKICB2YXIgY29sb3IgPSB2ZWMzKDAuMCk7CgogIGZvcih2YXIgaTp1MzIgPSAwOyBpIDwgNTA7IGkrKykgewogICAgdmFyIHAgPSByYXlvcmlnaW4gKyByYXlkaXJlY3Rpb24gKiB0b3RhbGRpc3RhbmNlOwogICAgbGV0IG5leHRkaXN0YW5jZSA9IHNjZW5lKHApOwoKICAgIHRvdGFsZGlzdGFuY2UgKz0gbmV4dGRpc3RhbmNlOwoKICAgIGlmKHRvdGFsZGlzdGFuY2UgPiBNQVhfRElTVCB8fCBuZXh0ZGlzdGFuY2UgPCBNSU5fRElTVCApIHsKICAgICAgYnJlYWs7CiAgICB9CiAgfQogIHJldHVybiB0b3RhbGRpc3RhbmNlOwp9CgoKQGZyYWdtZW50IApmbiBmcyggQGJ1aWx0aW4ocG9zaXRpb24pIHBvcyA6IHZlYzRmICkgLT4gQGxvY2F0aW9uKDApIHZlYzRmIHsKICB2YXIgdXYgPSBwb3MueHkvcmVzLnh5OwoKCS8vIG1ha2UgMCwwIHRoZSBvcmlnaW4gaW5zdGVhZCBvZiAuNSwuNQogIHV2IC09IDAuNTsKICAvLyBjb3JyZWN0IGFzcGVjdCByYXRpbwogIHV2LnggKj0gcmVzLnggLyByZXMueTsKCiAgLy8gcmF5IG9yaWdpbiBha2EgY2FtZXJhIHBvc2l0aW9uCiAgbGV0IHJvID0gdmVjM2YoMC4wLCAwLjAsIDUuMCk7CiAgLy8gcmF5IGRpcmVjdGlvbiB0aHJvdWdoIHRoZSB3aW5kb3cgaW50byB0aGUgc2NlbmUKICBsZXQgcmQgPSBub3JtYWxpemUodmVjMyh1diwgLTEuMCkpOwogIC8vIGdldCBkaXN0YW5jZSB0byBzdXJmYWNlIGludGVyc2VjdGlvbiB3aXRoIHJheQogIGxldCBkc3QgPSByYXltYXJjaCggcm8sIHJkICk7CiAgCiAgLy8gbG9jYXRpb24gb2YgaW50ZXJzZWN0aW9uID0gCiAgLy8gY2FtZXJhIHBvc2l0aW9uICsgcmF5IGRpcmVjdGlvbiAqIGRpc3RhbmNlCiAgbGV0IHAgPSBybyArIHJkICogZHN0OwoKCS8vIGRlZmF1bHQgY29sb3IgLyBiZwogIHZhciBjb2xvciA9IHZlYzNmKDAuLDAuLDAuKTsKCiAgLy8gY29sb3IgcGl4ZWwgaWYgaW50ZXJzZWN0aW9uIGlzIHdpdGhpbiB0b2xlcmFuY2UKICBpZiggZHN0IDwgTUFYX0RJU1QgKSB7CiAgICBjb2xvciA9IHZlYzNmKGRzdCk7CiAgfQoKICByZXR1cm4gdmVjNGYoIGNvbG9yLCAxLiApOwp9)

The `scene` function is the entry point for our raymarching graph. It will be called everytime our `raymarching` function checks to see if a ray intersects the 3D scene. Right now, it's calling the signed distance formula (SDF) for a sphere. For SDFs, remember that:

- a positive value means the point is outside the geometry
- a value of 0 means the point is on the surface of the geometry
- a negative value means the point is inside the geometry

The basic strategy for the raymarching algorithm is:
- See how far a point on a ray is from a object / scene, and return the distance
- if the distance is within or on a geometry in the scene, return the distance, else, advance along the ray by the distance.

This is better [explained visually](https://www.shadertoy.com/view/lslXD8). If you look at the image of the circles (really spheres) intersecting with a scene consisting of a triangle and a rectangle, you can hopefully get the idea. Basically the distance you travel along the ray becomes the radius of the next circle (sphere in 3D). The algorithm was first described in a [1995 paper by John Hart](https://graphics.stanford.edu/courses/cs348b-20-spring-content/uploads/hart.pdf) where he used the term "Sphere Tracing"... but really it's just a optimization of ray marching more generally. This algorithm performs much better than just marching along the ray at a fixed interval. That said, there are still times where fixed intervals might make sense, like with volumetric rendering.

## Lights - normals
The simplest lights we can get going (and really, it's enough to do a lot of fun stuff) is to obtain the "normal" of every surface our rays strike. The normal is represents a vector that is perpendicular to the point that the ray strikes. To calculate this we need the "gradient" of the point.

The math of this is a bit beyond this tutorial, but you can [read all the details in this essay by iq](https://iquilezles.org/articles/normalsSDF/). The basic idea is to sample the scene at points immediately surrounding the point of interest. Here's the function iq uses most often:

```js
vec3 calcNormal( in vec3 ) // for function f(p) 
{ 
	const float h = 0.0001; // replace by an appropriate value 
	const vec2 k = vec2(1,-1); 
	return normalize( 
		k.xyy*f( p + k.xyy*h ) + 
		k.yyx*f( p + k.yyx*h ) + 
		k.yxy*f( p + k.yxy*h ) + 
		k.xxx*f( p + k.xxx*h ) 
	); 
}
```

If it's good enough for him it's good enough for us. We do need to conver it to WGSL:

```js
fn calcNormal( p:vec3f ) -> vec3f {
    const h:f32 = 0.0001; // replace by an appropriate value
    const k:vec2f = vec2(1.,-1.);
    return normalize( k.xyy*scene( p + k.xyy*h ) + 
                      k.yyx*scene( p + k.yyx*h ) + 
                      k.yxy*scene( p + k.yxy*h ) + 
                      k.xxx*scene( p + k.xxx*h ) );
}
```

Paste the above function into your code in The Schwartz, at the top level, outside of any other function. At the bottom of your fragment shader, replace the if statement that colors the sphere with the distance as follows:

```js
  if( dst < MAX_DIST ) {
    color = calcNormal(p);
  }
```

And voila! You now have a sphere that looks like it's lit. 

## A bit more complex - another shape and diffuse lighting
[iq's page on distance functions](https://iquilezles.org/articles/distfunctions/)  has a lot of geometries to play around with. Let's try and put a ground plane underneath our sphere; we'll improve our lighting once we make the sphere more complex. In order to do that we'll need two new functions:

1. A SDF representing a plane
2. A function to combine the results from two SDFs

Here's a WGSL SDF for a plane:
```js
fn plane( point:vec3f, normal:vec3f, distance:f32 ) -> f32 {
  // the normal must be normalized
  return dot(point,normal) + distance;
}
```

Calling `plane( p, vec3f(0.,-1.,0.), 1 )` will generate a plane facing up (due to the normal).

Good news, the function to combine results from two SDFs is just `min()`, this is usually known as a Union when talking about constructive solid geometry. You run this on the *distances returned by two SDFs*. So, the following would return the distance  of the plane and sphere:

```js
fn scene( p ) -> f32 {
  return min( 
	sphere(p,.75), 
	plane(p, vec3f(0.,-1.,0.), .5 ) 
  );
}
```

OK, you can see... something? Because the normal of the plane is -1 for the y value, it's not really showing very much if we're using the normal as our color... the green channel is effectively -1. We can get better results by taking the absolute value of the normal using `abs()`. But it still doesn't look all that great. We need to get some 'real' lighting into the scene.

### diffuse lighting
The idea behind diffuse lighting is straightforward:
1. Calculate the direction between the surface point you're lighting and the light.
2. Calculate the similarity between this direction and the normal of the point; this is done by taking the dot product of the two vectors.

In code:
```js
let lightposition = vec3f(2,-2,2);
let dir = normalize( lightposition - p );
let lightStrength = clamp( dot(nor,dir),0.,1.);
color = vec3f( lightStrength );
```

That looks better! We can add some exponential fog to improve the look more. We will base that off the distance... the further into the scene we go, the denser the fog will be. For the best effect, your fog should be the same color as your background. We'll add an `else` clause that will take care of this, and we'll also need a new variable `bgcolor` (background color).

```js
  let bgcolor = vec3f(.5,0.,0.);
  if( dst < MAX_DIST ) {
    let nor = calcNormal(p);
    let lightposition = vec3f(2,-2,2);
	let dir = normalize( lightposition - p );
	let lightStrength = clamp( dot(nor,dir),0.,1.);
    color = vec3f( lightStrength );
    // fog
    let fogAmount = .2; // tune this parameter
    let fog = 1. - exp( -dst * fogAmount );
    let fogcolor = bgcolor * fog;
    color = mix( color, fogcolor, fog );
  }else{
    color = bgcolor;
  }
```
## Infinite Spheres inside a Sphere

OK, let's take a look at one of the most satisfying aspects of raymarching: repetition. There's a *very* simple function to repeat sdfs across a scene. You can see the [general idea  plotted here](https://www.desmos.com/geometry/8e3bphhyaa); as we move across our scene our value of p will repeatedly ramp up and then drop down. The function below is ported to WGSL from the [same  iq page](https://iquilezles.org/articles/distfunctions/) we linked too earlier.

```js
fn repeat( p:vec3f, d:vec3f ) -> vec3f {
  return p - d*round(p/d);
}
```

With that in place, let's change our scene to be a repeated field of spheres:

```js
fn scene( p:vec3f ) -> f32 {
  return sphere( repeat(p, vec3f(2.)), .25); 
}
```

You'll notice there's a center sphere that's much larger than the others... that's simply because it's right in front of us and closest. We can move our camera by adding an offset to p:

```js
fn scene( p:vec3f ) -> f32 {
  var mp = p;
  mp.x += sin(frame/120.); 
  return sphere( repeat(mp, vec3f(2.)), .25); 
}
```

At this point you might want to bump up the maximum number of steps  you're using in your raymarch for loop... 30 or 40 will probably give enough fidelity. 

### spheres in a sphere
Before we looked at using a Union (via a call to `min()`) to combine the results of two signed distance functions. But we can also *subtract* one geometry from another, and create really interesting results. 

```js
fn sub( a:f32, b:f32 ) -> f32 {
  return max(-a,b);
}
```

With that function in hand, we can now create a large sphere, and then subtract our infinite field of spheres from it. It becomes especially interesting looking if we animate moving the repeated field along the z-axis.

```js
fn scene( p: vec3f ) -> f32 {
  var pz = p;
  pz.z += frame / 200.f;
  pz.x += .25;
  var d = sub( sphere( repeat(pz, vec3f(.5)), .2), sphere(p,2.));
  //d = min( d, plane( p, vec3f(0.,-1.,0.),1. ) );

  return d;
}
```

Uncomment the `min` line to put our plane back in the scene.

## Wrapping up and next up
[Here's the completed scene](https://charlieroberts.codeberg.page/TheSchwartz/?Y29uc3QgTUFYX0RJU1QgOiBmMzIgPSAxMC47IApjb25zdCBNSU5fRElTVCA6IGYzMiA9IC4wMTsKY29uc3QgTUFYX1NURVBTOiB1MzIgPSAyMHU7CgpmbiBzY2VuZSggcDp2ZWMzZiApIC0+IGYzMiB7CiAgdmFyIHB6ID0gcDsKICBwei56ICs9IGZyYW1lIC8gMjAwLmY7CiAgcHoueCArPSAuMjU7CiAgdmFyIGQgPSBzdWIoc3BoZXJlKCByZXBlYXQocHosIHZlYzNmKC41KSksIC4yKSwgc3BoZXJlKHAsMi4pKTsKICBkID0gbWluKCBkLCBwbGFuZSggcCwgdmVjM2YoMC4sLTEuLDAuKSwxLiApICk7CgogIHJldHVybiBkOyAKfQoKZm4gc3ViKCBhOmYzMiwgYjpmMzIgKSAtPiBmMzIgewogIHJldHVybiBtYXgoLWEsYik7Cn0KCmZuIHJlcGVhdCggcDp2ZWMzZiwgZDp2ZWMzZiApIC0+IHZlYzNmIHsKICByZXR1cm4gcCAtIGQqcm91bmQocC9kKTsKfQoKZm4gc3BoZXJlKCBwb2ludDp2ZWMzZiwgcmFkaXVzOmYzMiApIC0+IGYzMiB7CiAgcmV0dXJuIGxlbmd0aChwb2ludCkgLSByYWRpdXM7Cn0KCmZuIHBsYW5lKCBwb2ludDp2ZWMzZiwgbm9ybWFsOnZlYzNmLCBkaXN0YW5jZTpmMzIgKSAtPiBmMzIgewogIC8vIHRoZSBub3JtYWwgbXVzdCBiZSBub3JtYWxpemVkCiAgcmV0dXJuIGRvdChwb2ludCxub3JtYWwpICsgZGlzdGFuY2U7Cn0KCmZuIGNhbGNOb3JtYWwoIHA6dmVjM2YgKSAtPiB2ZWMzZiB7CiAgICBjb25zdCBoOmYzMiA9IDAuMDAwMTsgLy8gcmVwbGFjZSBieSBhbiBhcHByb3ByaWF0ZSB2YWx1ZQogICAgY29uc3Qgazp2ZWMyZiA9IHZlYzIoMS4sLTEuKTsKICAgIHJldHVybiBub3JtYWxpemUoIGsueHl5KnNjZW5lKCBwICsgay54eXkqaCApICsgCiAgICAgICAgICAgICAgICAgICAgICBrLnl5eCpzY2VuZSggcCArIGsueXl4KmggKSArIAogICAgICAgICAgICAgICAgICAgICAgay55eHkqc2NlbmUoIHAgKyBrLnl4eSpoICkgKyAKICAgICAgICAgICAgICAgICAgICAgIGsueHh4KnNjZW5lKCBwICsgay54eHgqaCApICk7Cn0KCmZuIHJheW1hcmNoKHJheW9yaWdpbiA6IHZlYzNmLCByYXlkaXJlY3Rpb246IHZlYzNmICkgLT4gZjMyIHsKICB2YXIgdG90YWxkaXN0YW5jZSA9IDAuMDsKICB2YXIgY29sb3IgPSB2ZWMzKDAuMCk7CgogIGZvcih2YXIgaTp1MzIgPSAwOyBpIDwgTUFYX1NURVBTOyBpKyspIHsKICAgIHZhciBwID0gcmF5b3JpZ2luICsgcmF5ZGlyZWN0aW9uICogdG90YWxkaXN0YW5jZTsKICAgIGxldCBuZXh0ZGlzdGFuY2UgPSBzY2VuZShwKTsKCiAgICB0b3RhbGRpc3RhbmNlICs9IG5leHRkaXN0YW5jZTsKCiAgICBpZih0b3RhbGRpc3RhbmNlID4gTUFYX0RJU1QgfHwgbmV4dGRpc3RhbmNlIDwgTUlOX0RJU1QgKSB7CiAgICAgIGJyZWFrOwogICAgfQogIH0KICByZXR1cm4gdG90YWxkaXN0YW5jZTsKfQoKCkBmcmFnbWVudCAKZm4gZnMoIEBidWlsdGluKHBvc2l0aW9uKSBwb3MgOiB2ZWM0ZiApIC0+IEBsb2NhdGlvbigwKSB2ZWM0ZiB7CiAgdmFyIHV2ID0gcG9zLnh5L3Jlcy54eTsKCgkvLyBtYWtlIDAsMCB0aGUgb3JpZ2luIGluc3RlYWQgb2YgLjUsLjUKICB1diAtPSAwLjU7CiAgLy8gY29ycmVjdCBhc3BlY3QgcmF0aW8KICB1di54ICo9IHJlcy54IC8gcmVzLnk7CgogIC8vIHJheSBvcmlnaW4gYWthIGNhbWVyYSBwb3NpdGlvbgogIGxldCBybyA9IHZlYzNmKDAuMCwgMC4wLCA1LjApOwogIC8vIHJheSBkaXJlY3Rpb24gdGhyb3VnaCB0aGUgd2luZG93IGludG8gdGhlIHNjZW5lCiAgbGV0IHJkID0gbm9ybWFsaXplKHZlYzModXYsIC0xLjApKTsKICAvLyBnZXQgZGlzdGFuY2UgdG8gc3VyZmFjZSBpbnRlcnNlY3Rpb24gd2l0aCByYXkKICBsZXQgZHN0ID0gcmF5bWFyY2goIHJvLCByZCApOwogIAogIC8vIGxvY2F0aW9uIG9mIGludGVyc2VjdGlvbiA9IAogIC8vIGNhbWVyYSBwb3NpdGlvbiArIHJheSBkaXJlY3Rpb24gKiBkaXN0YW5jZQogIGxldCBwID0gcm8gKyByZCAqIGRzdDsKCgkvLyBkZWZhdWx0IGNvbG9yIC8gYmcKICB2YXIgYmdjb2xvciA9IHZlYzNmKC4wLDAuLDAuKTsKICB2YXIgY29sb3IgPSB2ZWMzZigwLiwwLiwwLik7CgogIC8vIGNvbG9yIHBpeGVsIGlmIGludGVyc2VjdGlvbiBpcyB3aXRoaW4gdG9sZXJhbmNlCiAgaWYoIGRzdCA8IE1BWF9ESVNUICkgewogICAgbGV0IG5vciA9IGNhbGNOb3JtYWwocCk7CiAgICBsZXQgbGlnaHRwb3NpdGlvbiA9IHZlYzNmKDIsLTIsMik7CgkJbGV0IGRpciA9IG5vcm1hbGl6ZSggbGlnaHRwb3NpdGlvbiAtIHAgKTsKCQlsZXQgbGlnaHRTdHJlbmd0aCA9IGNsYW1wKCBkb3Qobm9yLGRpciksMC4sMS4pOwogICAgY29sb3IgPSB2ZWMzZiggbGlnaHRTdHJlbmd0aCApOwogICAgbGV0IGZvZ0Ftb3VudCA9IC4yOwogICAgbGV0IGZvZyA9IDEuIC0gZXhwKCAtZHN0ICogZm9nQW1vdW50ICk7CiAgICBsZXQgZm9nY29sb3IgPSBiZ2NvbG9yICogZm9nOwogICAgY29sb3IgPSBtaXgoIGNvbG9yLCBmb2djb2xvciwgZm9nICk7CiAgfWVsc2V7CiAgICBjb2xvciA9IGJnY29sb3I7CiAgfQoKICByZXR1cm4gdmVjNGYoIGNvbG9yLCAxLiApOwp9) We've actually got most of the basic raymarching setup complete! Some elements we would typically add to polish the scene include:
- Soft Shadows
- Ambient Occlusion
- Specular Highlights + Ambient Light (Phong shading)
- Gamma correction
- Light attenuation

[iq's blog](https://iquilezles.org/articles/) is a great place to learn more about many of these things, although details about phong shading can be found all over the place as it's commonly taught when introducing 3d graphics.

For more *fun*, try more geometries, more ways to combine geometries, more ways to warp space. The Mercury demogroup published [a great guide](https://mercury.sexy/hg_sdf/) to all sorts of methods for combining geometries with interesting transitions, and for warping space.

Post-processing effects can *really* improve the quality of a scene, especially depth-of-field (a bit tricky, you have to write the distance for each pixel to a depth buffer), antialiasing (anti-aliasing can also be performed just with multiple raymarch passes), and vingette.

Also note that you can bring this code directly into [gulls](https://codeberg.org/charlieroberts/gulls), you just need to add the `res` uniform.
