# 3D Scene Reference for Manim

## Basic 3D Scene Setup

```python
from manim import *

class Basic3D(ThreeDScene):
    def construct(self):
        # Set initial camera position
        self.set_camera_orientation(
            phi=75 * DEGREES,    # angle from z-axis (vertical tilt)
            theta=-45 * DEGREES  # rotation around z-axis
        )

        # Create 3D axes
        axes = ThreeDAxes(
            x_range=[-3, 3, 1],
            y_range=[-3, 3, 1],
            z_range=[-3, 3, 1],
            x_length=6,
            y_length=6,
            z_length=6,
        )

        # Add axis labels
        labels = axes.get_axis_labels(
            x_label="x",
            y_label="y",
            z_label="z"
        )

        self.play(Create(axes), Write(labels))
```

## Camera Control

```python
# Set camera orientation
self.set_camera_orientation(
    phi=75 * DEGREES,      # vertical angle (0 = top-down, 90 = side view)
    theta=-45 * DEGREES,   # horizontal rotation
    gamma=0,               # roll
    zoom=1,
    frame_center=ORIGIN
)

# Animate camera movement
self.move_camera(
    phi=60 * DEGREES,
    theta=30 * DEGREES,
    run_time=2
)

# Continuous rotation (good for showcasing 3D objects)
self.begin_ambient_camera_rotation(rate=0.2)  # radians per second
# ... show your objects ...
self.stop_ambient_camera_rotation()

# Focus on specific point
self.move_camera(frame_center=[1, 2, 3])
```

## 3D Shapes

```python
# Sphere
sphere = Sphere(
    radius=1,
    resolution=(20, 20),  # (u_segments, v_segments)
    fill_opacity=0.7
)

# Cube
cube = Cube(side_length=2, fill_opacity=0.5)

# Cylinder
cylinder = Cylinder(
    radius=1,
    height=2,
    direction=Z_AXIS,  # or X_AXIS, Y_AXIS
    fill_opacity=0.7
)

# Cone
cone = Cone(
    base_radius=1,
    height=2,
    direction=Z_AXIS
)

# Torus (donut)
torus = Torus(
    major_radius=2,
    minor_radius=0.5
)

# Prism
prism = Prism(dimensions=[2, 3, 1])  # width, height, depth

# Arrow in 3D
arrow_3d = Arrow3D(
    start=[0, 0, 0],
    end=[1, 2, 3],
    color=RED
)

# Line in 3D
line_3d = Line3D(
    start=[0, 0, 0],
    end=[2, 2, 2]
)

# Dot in 3D
dot_3d = Dot3D(point=[1, 1, 1], radius=0.1, color=YELLOW)
```

## Surfaces

### Surface from Function z = f(x, y)

```python
class FunctionSurface(ThreeDScene):
    def construct(self):
        self.set_camera_orientation(phi=75 * DEGREES, theta=-45 * DEGREES)

        axes = ThreeDAxes(x_range=[-3, 3], y_range=[-3, 3], z_range=[-2, 2])

        # Define surface
        surface = Surface(
            lambda u, v: axes.c2p(u, v, np.sin(u) * np.cos(v)),
            u_range=[-PI, PI],
            v_range=[-PI, PI],
            resolution=(30, 30),
            fill_opacity=0.7,
            checkerboard_colors=[BLUE_D, BLUE_E]
        )

        self.play(Create(axes), Create(surface))
        self.begin_ambient_camera_rotation(rate=0.2)
        self.wait(5)
```

### Parametric Surface

```python
# Sphere via parametric
sphere_surface = Surface(
    lambda u, v: np.array([
        np.cos(u) * np.sin(v),
        np.sin(u) * np.sin(v),
        np.cos(v)
    ]),
    u_range=[0, TAU],
    v_range=[0, PI],
    resolution=(30, 30)
)

# Torus via parametric
R, r = 2, 0.5  # major and minor radius
torus_surface = Surface(
    lambda u, v: np.array([
        (R + r * np.cos(v)) * np.cos(u),
        (R + r * np.cos(v)) * np.sin(u),
        r * np.sin(v)
    ]),
    u_range=[0, TAU],
    v_range=[0, TAU]
)
```

## 3D Curves

```python
# Parametric curve
helix = ParametricFunction(
    lambda t: np.array([
        np.cos(t),
        np.sin(t),
        t / 4
    ]),
    t_range=[0, 4 * PI],
    color=YELLOW
)

# Curve on surface
curve_on_surface = ParametricFunction(
    lambda t: np.array([
        t,
        np.sin(t),
        np.cos(t)
    ]),
    t_range=[-PI, PI]
)
```

## 3D Graphs on Axes

```python
class Graph3D(ThreeDScene):
    def construct(self):
        self.set_camera_orientation(phi=75 * DEGREES, theta=-45 * DEGREES)

        axes = ThreeDAxes()

        # 3D parametric curve
        curve = axes.plot_parametric_curve(
            lambda t: np.array([
                np.cos(t),
                np.sin(t),
                t / 2
            ]),
            t_range=[-2 * PI, 2 * PI],
            color=YELLOW
        )

        # Surface plot
        surface = axes.plot_surface(
            lambda x, y: x**2 - y**2,
            u_range=[-2, 2],
            v_range=[-2, 2],
            colorscale=[(RED, 0), (YELLOW, 0.5), (GREEN, 1)]
        )

        self.play(Create(axes), Create(curve))
```

## Solid of Revolution

```python
class SolidOfRevolution(ThreeDScene):
    def construct(self):
        self.set_camera_orientation(phi=75 * DEGREES, theta=-30 * DEGREES)

        axes = ThreeDAxes()

        # Function to revolve
        func = lambda x: 0.5 + 0.5 * np.sin(x)

        # Create surface of revolution around x-axis
        surface = Surface(
            lambda u, v: axes.c2p(
                u,
                func(u) * np.cos(v),
                func(u) * np.sin(v)
            ),
            u_range=[0, 2 * PI],
            v_range=[0, TAU],
            resolution=(30, 30),
            fill_opacity=0.7
        )

        self.play(Create(axes), Create(surface))
        self.begin_ambient_camera_rotation(rate=0.15)
        self.wait(5)
```

## Vector Fields in 3D

```python
class VectorField3D(ThreeDScene):
    def construct(self):
        self.set_camera_orientation(phi=75 * DEGREES, theta=-45 * DEGREES)

        # Simple vector field
        def field_func(point):
            x, y, z = point
            return np.array([-y, x, 0]) * 0.3

        vector_field = ArrowVectorField(
            field_func,
            x_range=[-2, 2, 0.5],
            y_range=[-2, 2, 0.5],
            z_range=[-1, 1, 0.5],
        )

        self.play(Create(vector_field))
```

## Lighting and Shading

```python
# Surfaces automatically get shading based on camera position
# You can customize fill colors and opacity

surface = Surface(
    func,
    fill_opacity=0.8,
    checkerboard_colors=[BLUE_D, BLUE_E],  # alternating colors
    stroke_color=WHITE,
    stroke_width=0.5
)

# For solid colors
surface.set_fill_by_value(
    axes=axes,
    colorscale=[(RED, -1), (WHITE, 0), (BLUE, 1)],
    axis=2  # color by z-value
)
```

## Common 3D Animation Patterns

### Rotating View

```python
self.play(Create(object_3d))
self.begin_ambient_camera_rotation(rate=0.2)
self.wait(10)  # rotate for 10 seconds
self.stop_ambient_camera_rotation()
```

### Fly-Around

```python
# Multiple camera angle changes
for theta in [0, 45, 90, 135, 180]:
    self.move_camera(theta=theta * DEGREES, run_time=1)
```

### Cross-Section Animation

```python
# Show a plane slicing through an object
plane = Surface(
    lambda u, v: np.array([u, v, 0]),
    u_range=[-2, 2],
    v_range=[-2, 2],
    fill_opacity=0.3,
    fill_color=YELLOW
)

# Animate plane moving through object
self.play(plane.animate.shift(OUT * 2), run_time=3)
```

### Building Up 3D Objects

```python
# Create slices that stack to form a solid
slices = VGroup()
for z in np.linspace(-1, 1, 20):
    radius = np.sqrt(1 - z**2)  # sphere slice
    circle = Circle(radius=radius).shift(OUT * z)
    slices.add(circle)

self.play(LaggedStart(*[Create(s) for s in slices], lag_ratio=0.1))
```
