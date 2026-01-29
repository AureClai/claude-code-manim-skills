# Manim API Quick Reference

## Imports

```python
from manim import *
```

## Scene Types

| Class | Use Case |
|-------|----------|
| `Scene` | Standard 2D animations |
| `ThreeDScene` | 3D animations with camera control |
| `MovingCameraScene` | 2D with camera pan/zoom |
| `ZoomedScene` | Picture-in-picture zoom effect |

## Core Mobjects (Mathematical Objects)

### Shapes

```python
# Basic shapes
circle = Circle(radius=1, color=BLUE)
square = Square(side_length=2, color=RED)
rectangle = Rectangle(width=4, height=2)
triangle = Triangle()
polygon = Polygon([0,0,0], [2,0,0], [1,2,0])
line = Line(start=LEFT, end=RIGHT)
arrow = Arrow(start=LEFT, end=RIGHT)
dot = Dot(point=[1, 2, 0], radius=0.08)
arc = Arc(radius=1, start_angle=0, angle=PI/2)

# With styling
circle = Circle(
    radius=1,
    color=BLUE,
    fill_color=BLUE,
    fill_opacity=0.5,
    stroke_width=4
)
```

### Text and Math

```python
# Plain text
text = Text("Hello World", font_size=48)
text = Text("Bold", weight=BOLD)

# LaTeX (text mode)
tex = Tex(r"This is \textbf{bold} text")

# Math (math mode by default)
equation = MathTex(r"\int_0^1 x^2 \, dx = \frac{1}{3}")

# Isolate parts for coloring
eq = MathTex(r"{{a^2}} + {{b^2}} = {{c^2}}")
eq.set_color_by_tex("a^2", RED)
eq.set_color_by_tex("b^2", GREEN)
eq.set_color_by_tex("c^2", BLUE)

# Multiple lines
equations = VGroup(
    MathTex(r"f(x) = x^2"),
    MathTex(r"f'(x) = 2x"),
).arrange(DOWN)
```

### Graphs and Axes

```python
# 2D Axes
axes = Axes(
    x_range=[-3, 3, 1],      # [min, max, step]
    y_range=[-2, 8, 1],
    x_length=6,               # visual width
    y_length=4,               # visual height
    axis_config={
        "color": WHITE,
        "include_tip": True,
        "include_numbers": True,
    },
)

# Plot function
graph = axes.plot(lambda x: x**2, color=BLUE)
graph = axes.plot(lambda x: np.sin(x), x_range=[-PI, PI])

# Labels
x_label = axes.get_x_axis_label("x")
y_label = axes.get_y_axis_label("y")
graph_label = axes.get_graph_label(graph, label="f(x)")

# Coordinate system helper
number_plane = NumberPlane()  # shows grid

# Points and lines on axes
dot = Dot(axes.c2p(2, 4))  # coords to point
v_line = axes.get_vertical_line(axes.c2p(2, 4))
h_line = axes.get_horizontal_line(axes.c2p(2, 4))

# Area under curve
area = axes.get_area(graph, x_range=[0, 2], color=BLUE, opacity=0.3)

# Riemann rectangles
rects = axes.get_riemann_rectangles(
    graph,
    x_range=[0, 2],
    dx=0.25,
    input_sample_type="center",  # or "left", "right"
    stroke_width=1,
    fill_opacity=0.5,
)
```

### 3D Objects

```python
# 3D Axes
axes_3d = ThreeDAxes(
    x_range=[-3, 3, 1],
    y_range=[-3, 3, 1],
    z_range=[-3, 3, 1],
)

# 3D shapes
sphere = Sphere(radius=1)
cube = Cube(side_length=2)
cylinder = Cylinder(radius=1, height=2)
cone = Cone(base_radius=1, height=2)
torus = Torus(major_radius=2, minor_radius=0.5)

# Surface from function
surface = Surface(
    lambda u, v: axes_3d.c2p(u, v, np.sin(u) * np.cos(v)),
    u_range=[-PI, PI],
    v_range=[-PI, PI],
    resolution=(30, 30),
)

# Parametric curve in 3D
curve_3d = ParametricFunction(
    lambda t: np.array([np.cos(t), np.sin(t), t/4]),
    t_range=[0, 4*PI],
)
```

### Groups

```python
# Group mobjects together
group = VGroup(circle, square, triangle)
group.arrange(RIGHT, buff=0.5)  # horizontal layout
group.arrange(DOWN)              # vertical layout

# Apply to all
group.set_color(RED)
group.shift(UP)
```

## Positioning Methods

```python
# Absolute positioning
obj.move_to([x, y, z])
obj.move_to(ORIGIN)
obj.move_to(LEFT * 2 + UP)

# Relative positioning
obj.next_to(other, RIGHT, buff=0.5)
obj.next_to(other, UP)

# Alignment
obj.align_to(other, LEFT)
obj.align_to(other, UP)

# Edge positioning
obj.to_edge(UP)
obj.to_edge(LEFT, buff=1)
obj.to_corner(UL)  # upper left

# Shifting
obj.shift(RIGHT * 2)
obj.shift(UP + LEFT)

# Centering
obj.center()
```

## Styling Methods

```python
# Colors
obj.set_color(BLUE)
obj.set_fill(RED, opacity=0.5)
obj.set_stroke(WHITE, width=2)

# Scaling
obj.scale(2)        # double size
obj.scale(0.5)      # half size

# Rotation
obj.rotate(PI/4)              # 45 degrees
obj.rotate(PI/2, axis=RIGHT)  # 3D rotation

# Flip
obj.flip(RIGHT)  # horizontal flip
obj.flip(UP)     # vertical flip
```

## Animation Classes

### Creation/Destruction

```python
self.play(Create(obj))           # draw outline
self.play(Write(text))           # write text
self.play(FadeIn(obj))           # fade in
self.play(FadeIn(obj, shift=UP)) # fade in from below
self.play(FadeOut(obj))          # fade out
self.play(Uncreate(obj))         # reverse of Create
self.play(Unwrite(text))         # reverse of Write
self.play(GrowFromCenter(obj))   # grow from center
self.play(SpinInFromNothing(obj))
```

### Transformation

```python
self.play(Transform(obj1, obj2))
self.play(ReplacementTransform(obj1, obj2))
self.play(TransformMatchingTex(eq1, eq2))
self.play(TransformMatchingShapes(shape1, shape2))
self.play(FadeTransform(obj1, obj2))
self.play(MoveToTarget(obj))  # requires obj.generate_target()
```

### Movement

```python
self.play(obj.animate.shift(RIGHT * 2))
self.play(obj.animate.move_to([1, 2, 0]))
self.play(obj.animate.rotate(PI/2))
self.play(obj.animate.scale(2))
self.play(Rotate(obj, PI/2))
self.play(Circumscribe(obj))  # draw circle around
```

### Emphasis

```python
self.play(Indicate(obj))           # flash highlight
self.play(Flash(point))            # flash at point
self.play(ShowPassingFlash(obj))   # light passes through
self.play(Wiggle(obj))             # wiggle
self.play(Circumscribe(obj))       # circle around
self.play(FocusOn(obj))            # zoom focus
```

### Multiple Animations

```python
# Simultaneous
self.play(Create(obj1), FadeIn(obj2))

# Sequential with lag
self.play(LaggedStart(
    FadeIn(obj1),
    FadeIn(obj2),
    FadeIn(obj3),
    lag_ratio=0.3
))

# Succession
self.play(Succession(
    Create(obj1),
    FadeIn(obj2),
    obj3.animate.shift(RIGHT)
))
```

## Scene Methods

```python
self.add(obj)           # instantly add
self.remove(obj)        # instantly remove
self.play(animation)    # play animation
self.wait(seconds)      # pause
self.wait()             # default 1 second pause

# Multiple animations with timing
self.play(anim1, anim2, run_time=2)
self.play(anim, rate_func=smooth)
self.play(anim, rate_func=linear)
self.play(anim, rate_func=there_and_back)
```

## Value Trackers and Updaters

```python
# Create tracker
t = ValueTracker(0)

# Read value
current = t.get_value()

# Animate value change
self.play(t.animate.set_value(5), run_time=3)

# Always-updating mobject
dot = always_redraw(
    lambda: Dot(axes.c2p(t.get_value(), func(t.get_value())))
)

# Manual updater
def update_func(mob):
    mob.move_to(axes.c2p(t.get_value(), 0))

obj.add_updater(update_func)
# Later: obj.remove_updater(update_func)
```

## Camera Control (3D)

```python
# In ThreeDScene
self.set_camera_orientation(phi=75*DEGREES, theta=-45*DEGREES)
self.move_camera(phi=60*DEGREES, theta=30*DEGREES, run_time=2)
self.begin_ambient_camera_rotation(rate=0.1)
self.stop_ambient_camera_rotation()
```

## Constants

### Directions
```python
UP, DOWN, LEFT, RIGHT
UL, UR, DL, DR  # corners
ORIGIN
OUT, IN  # 3D z-axis
```

### Colors
```python
WHITE, BLACK, GREY, GREY_A, GREY_B, GREY_C, GREY_D, GREY_E
RED, RED_A, RED_B, RED_C, RED_D, RED_E
GREEN, GREEN_A, GREEN_B, GREEN_C, GREEN_D, GREEN_E
BLUE, BLUE_A, BLUE_B, BLUE_C, BLUE_D, BLUE_E
YELLOW, GOLD, ORANGE, PINK, PURPLE, TEAL, MAROON
```

### Math
```python
PI, TAU  # TAU = 2*PI
DEGREES  # multiply to convert: 45 * DEGREES
```
