<!--
author:   André Dietrich

email:    LiaScript@web.de

version:  0.0.1

language: en

narrator: US English Female

logo:     logo.png

comment:  Set of PyScript-macros to be used with LiaScript for creating interactive Python tutorial.

link:     https://cdn.jsdelivr.net/gh/liatemplates/pyscript@0.0.1/dist/pyscript.css
script:   https://cdn.jsdelivr.net/gh/liatemplates/pyscript@0.0.1/dist/pyscript.min.js

persistent:  true


@PyScript.env
<lia-keep>
<py-env>
@0
</py-env>
</lia-keep>
@end

@PyScript.repl: @PyScript.replWith( ,```@0```)

@PyScript.replWith
<lia-keep>
<style>
  .output {
    font-style: inherit;
    font-weight: inherit;
    font-size: inherit;
    line-height: inherit;
    margin-left: 0px;
    float: left;
  }

  .mt-2 {
    margin-left: 0px !important;
    margin-right: 0px !important;
    margin-bottom: 15px !important;
  }

  .editor-box {
    border: 1px solid black;
  }
</style>
<py-repl @0>@1</py-repl>
</lia-keep>
@end

-->

[![LiaScript](https://raw.githubusercontent.com/LiaScript/LiaScript/master/badges/course.svg)](https://LiaScript.github.io/course/?https://raw.githubusercontent.com/LiaTemplates/PyScript/main/README.md)

# PyScript - Template

          --{{0}}--
This document defines some basic macros for integrating the
[PyScript](https://pyscript.net/) into [LiaScript](https://LiaScript.github.io)
and to make Markdown code-blocks executable.

__Try it on LiaScript:__

https://liascript.github.io/course/?https://raw.githubusercontent.com/liaTemplates/PyScript/main/README.md

__See the project on Github:__

https://github.com/liaTemplates/PyScript

          --{{1}}--

There are three ways to use this template. The easiest way is to use the
`import` statement and the url of the raw text-file of the master branch or any
other branch or version. But you can also copy the required functionality
directly into the header of your Markdown document, see therefor the
[last slide](#implementation). And of course, you could also clone this project
and change it, as you wish.

           {{1}}
0. Add the header-definition `persistent: true` to your main comment or at least
   to all slides that run a PyScript.

1. Load the macros via

   `import: https://raw.githubusercontent.com/liaTemplates/PyScript/main/README.md`

   or use this specific version and you course will be stable:

   `import: https://raw.githubusercontent.com/LiaTemplates/PyScript/0.0.1/README.md`

2. Copy the definitions into your Project

3. Clone this repository on GitHub

## `@PyScript.repl`

          --{{0}}--
To use the default REPL configuration of PyScript, simply add this macro to the
head of your code-block. This will insert a new REPL with the code you have
passed to the LiaScript rendered version of the course.

```` markdown
``` python @PyScript.repl
print("Hello World")
```
````

---

__Result:__

``` python @PyScript.repl
print("Hello World")
```

## `@PyScript.replWith`

          --{{0}}--
Additionally, it is also possible to pass additional config parameters to the
REPL, by using the `@PyScript.replWith` macro. For more information, please
visit the [PyScript website](https://pyscript.net):

```` markdown
``` python @PyScript.replWith(auto-generate="true")
print("Hello World")
```
````

---

__Result:__

``` python @PyScript.replWith(auto-generate="true")
print("Hello World")
```

## `@PyScript.env`

          --{{0}}--
If you need to make use of the `<py-env>` to preload Python libraries, then use
the `@PyScript.env` macro. The content will be hidden to the LiaScript rendered
version of your course.

```` markdown
``` python @PyScript.env
- matplotlib
- numpy
```

``` python @PyScript.repl
import matplotlib.pyplot as plt
import matplotlib.tri as tri
import numpy as np

# First create the x and y coordinates of the points.
n_angles = 36
n_radii = 8
min_radius = 0.25
radii = np.linspace(min_radius, 0.95, n_radii)

angles = np.linspace(0, 2 * np.pi, n_angles, endpoint=False)
angles = np.repeat(angles[..., np.newaxis], n_radii, axis=1)
angles[:, 1::2] += np.pi / n_angles

x = (radii * np.cos(angles)).flatten()
y = (radii * np.sin(angles)).flatten()
z = (np.cos(radii) * np.cos(3 * angles)).flatten()

# Create the Triangulation; no triangles so Delaunay triangulation created.
triang = tri.Triangulation(x, y)

# Mask off unwanted triangles.
triang.set_mask(np.hypot(x[triang.triangles].mean(axis=1),
                         y[triang.triangles].mean(axis=1))
                < min_radius)

fig1, ax1 = plt.subplots()
ax1.set_aspect('equal')
tpc = ax1.tripcolor(triang, z, shading='flat')
fig1.colorbar(tpc)
ax1.set_title('tripcolor of Delaunay triangulation, flat shading')

fig1
```
````

---

__Result:__

``` python @PyScript.env
- matplotlib
- numpy
```

``` python @PyScript.repl
import matplotlib.pyplot as plt
import matplotlib.tri as tri
import numpy as np

# First create the x and y coordinates of the points.
n_angles = 36
n_radii = 8
min_radius = 0.25
radii = np.linspace(min_radius, 0.95, n_radii)

angles = np.linspace(0, 2 * np.pi, n_angles, endpoint=False)
angles = np.repeat(angles[..., np.newaxis], n_radii, axis=1)
angles[:, 1::2] += np.pi / n_angles

x = (radii * np.cos(angles)).flatten()
y = (radii * np.sin(angles)).flatten()
z = (np.cos(radii) * np.cos(3 * angles)).flatten()

# Create the Triangulation; no triangles so Delaunay triangulation created.
triang = tri.Triangulation(x, y)

# Mask off unwanted triangles.
triang.set_mask(np.hypot(x[triang.triangles].mean(axis=1),
                         y[triang.triangles].mean(axis=1))
                < min_radius)

fig1, ax1 = plt.subplots()
ax1.set_aspect('equal')
tpc = ax1.tripcolor(triang, z, shading='flat')
fig1.colorbar(tpc)
ax1.set_title('tripcolor of Delaunay triangulation, flat shading')

fig1
```

## implementation

In order to use PyScript, we had to make a few changes to the standard project,
in order to prevent event-bubbling, sanitize CSS, and add some
[bug-fixes](https://github.com/pyscript/pyscript/issues/480), that are not yet
in the PyScript project. And that is why, we currently have to load the script
from our local repository.

The rest is simply LiaScript-macro syntax ... Do not forget to add
`persistent: true` to your own project.

``` html
link:     https://cdn.jsdelivr.net/gh/liatemplates/pyscript@0.0.1/dist/pyscript.css
script:   https://cdn.jsdelivr.net/gh/liatemplates/pyscript@0.0.1/dist/pyscript.min.js

persistent:  true


@PyScript.env
<lia-keep>
<py-env>
@0
</py-env>
</lia-keep>
@end

@PyScript.repl: @PyScript.replWith( ,```@0```)

@PyScript.replWith
<lia-keep>
<style>
  .output {
    font-style: inherit;
    font-weight: inherit;
    font-size: inherit;
    line-height: inherit;
    margin-left: 0px;
    float: left;
  }

  .mt-2 {
    margin-left: 0px !important;
    margin-right: 0px !important;
    margin-bottom: 15px !important;
  }

  .editor-box {
    border: 1px solid black;
  }
</style>
<py-repl @0>@1</py-repl>
</lia-keep>
@end

```