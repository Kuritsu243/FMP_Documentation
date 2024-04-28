# Shader Development
<show-structure depth="2" />

Throughout my project, I experimented slightly with shaders. I have no prior knowledge / experience using
Unity's built-in shader graph feature, so I thought that it would be a nice introduction to doing so.

## Cel-Shading

### Outline—Initial Testing 

I was going to experiment with a cel-shaded look in my project, as a way to compensate for the low-mid poly 
environment and character models, and to give the scene a further sense of personality. In doing so, I created this shader:

![Shader Graph Displaying the Basic Outline Shader](basic_outline_shadergraph.png)

Which resulted in once the material being applied, looked like this:

![Unity Inspector Showing a Basic Cube with Outline Applied](basic_outline_scene_view.png)

This initially looked pretty good, however, once being applied to more convex shapes issues would appear:

![Unity Inspector Showing issues with the Outline on more Complex Meshes](outline_shader_fail.png)

### Outline - Conclusion

Due to these issues and my lack of knowledge regarding Unity Shaders (both in HLSL and via Shader Graph), 
I scrapped this idea early on. The amount of time I would have eventually spent on this was used to further develop
my mechanic concepts.

## Portal 

### Portal—Initial Testing 

I had the plan to make a portal shader, to simulate a portal appearing throughout my game scenes, as a signifier to the end of each level / scene. 
Following [this tutorial on YouTube](https://www.youtube.com/watch?v=GeeKVPwM5Xw), I ended up making this shader via shader graph, with the result looking like this:

![Shader Graph Displaying the Portal Shader](portal_shader_graph.png){thumbnail="true"}

Once applying this material to a sprite renderer within my scene, the end result looked like this:

<video src="portal_shader_showcase.mp4"/>

### Portal—Conclusion

This shader was definitely worth learning how to make, and was a nice further introduction to Unity's Shader Graph system.