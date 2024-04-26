# Shader Development

Throughout my project, I experimented slightly with shaders. I have no prior knowledge / experience using
Unity's built-in shader graph feature, so I thought that it would be a nice introduction to doing so.

## Initial Testing 

I was going to experiment with a cel-shaded look in my project, as a way to compensate for the low-mid poly 
environment and character models, and to give the scene a further sense of personality. In doing so, I created this shader:

![Shader Graph Displaying the Basic Outline Shader](basic_outline_shadergraph.png)

Which resulted in once the material being applied, looked like this:

![Unity Inspector Showing a Basic Cube with Outline Applied](basic_outline_scene_view.png)

This initially looked pretty good, however once being applied to more convex shapes issues would appear:
