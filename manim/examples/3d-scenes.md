# 3D Scene Animation Examples

## Example 1: 3D Function Surface

Visualizes z = f(x, y) as a surface.

```python
from manim import *

class Surface3D(ThreeDScene):
    """
    Animation showing a 3D surface plot.

    Mathematical concept: z = sin(x) * cos(y)
    Key insight: Visualizing multivariable functions
    """

    def construct(self):
        # Camera setup
        self.set_camera_orientation(phi=75 * DEGREES, theta=-45 * DEGREES)

        # Title (faces camera)
        title = Text("Surface: z = sin(x)cos(y)", font_size=36)
        title.to_corner(UL)
        self.add_fixed_in_frame_mobjects(title)
        self.play(Write(title))

        # 3D axes
        axes = ThreeDAxes(
            x_range=[-PI, PI, 1],
            y_range=[-PI, PI, 1],
            z_range=[-1.5, 1.5, 0.5],
            x_length=6,
            y_length=6,
            z_length=3,
        )

        # Axis labels
        x_label = axes.get_x_axis_label("x")
        y_label = axes.get_y_axis_label("y")
        z_label = axes.get_z_axis_label("z")

        self.play(Create(axes), Write(x_label), Write(y_label), Write(z_label))

        # Create surface
        surface = Surface(
            lambda u, v: axes.c2p(u, v, np.sin(u) * np.cos(v)),
            u_range=[-PI, PI],
            v_range=[-PI, PI],
            resolution=(30, 30),
            fill_opacity=0.7,
            checkerboard_colors=[BLUE_D, BLUE_E],
            stroke_color=WHITE,
            stroke_width=0.5,
        )

        self.play(Create(surface), run_time=3)
        self.wait()

        # Rotate camera to show surface from different angles
        self.begin_ambient_camera_rotation(rate=0.15)
        self.wait(8)
        self.stop_ambient_camera_rotation()
```

## Example 2: Sphere and Great Circle

```python
from manim import *

class SphereGreatCircle(ThreeDScene):
    """
    Animation showing a sphere with great circles.

    Mathematical concept: Great circles on a sphere
    Key insight: Shortest path on sphere is along great circle
    """

    def construct(self):
        self.set_camera_orientation(phi=70 * DEGREES, theta=-60 * DEGREES)

        # Create sphere
        sphere = Sphere(
            radius=2,
            resolution=(30, 30),
            fill_opacity=0.5,
            fill_color=BLUE,
        )

        self.play(Create(sphere), run_time=2)

        # Add coordinate axes for reference
        axes = ThreeDAxes(
            x_range=[-3, 3], y_range=[-3, 3], z_range=[-3, 3],
            x_length=6, y_length=6, z_length=6,
        )
        self.play(Create(axes))

        # Great circle (equator)
        equator = ParametricFunction(
            lambda t: np.array([2 * np.cos(t), 2 * np.sin(t), 0]),
            t_range=[0, TAU],
            color=RED,
            stroke_width=4,
        )
        self.play(Create(equator))

        equator_label = Text("Equator", font_size=24)
        self.add_fixed_in_frame_mobjects(equator_label)
        equator_label.to_corner(UR)
        self.play(Write(equator_label))

        # Another great circle (meridian)
        meridian = ParametricFunction(
            lambda t: np.array([2 * np.cos(t), 0, 2 * np.sin(t)]),
            t_range=[0, TAU],
            color=GREEN,
            stroke_width=4,
        )
        self.play(Create(meridian))

        # Rotate to show 3D nature
        self.begin_ambient_camera_rotation(rate=0.2)
        self.wait(6)
        self.stop_ambient_camera_rotation()
```

## Example 3: Solid of Revolution

```python
from manim import *

class SolidOfRevolution(ThreeDScene):
    """
    Animation showing how a 2D curve generates a 3D solid when rotated.

    Mathematical concept: Volume by revolution
    Key insight: Rotating a curve around an axis creates a solid
    """

    def construct(self):
        self.set_camera_orientation(phi=75 * DEGREES, theta=-45 * DEGREES)

        # Title
        title = Text("Solid of Revolution", font_size=36)
        self.add_fixed_in_frame_mobjects(title)
        title.to_edge(UP)
        self.play(Write(title))

        # 3D axes
        axes = ThreeDAxes(
            x_range=[0, 4, 1],
            y_range=[-2, 2, 1],
            z_range=[-2, 2, 1],
        )
        self.play(Create(axes))

        # Function to revolve: y = sqrt(x)
        func = lambda x: np.sqrt(x) if x >= 0 else 0

        # Show the 2D curve first
        curve_2d = axes.plot(func, x_range=[0, 3], color=YELLOW)
        curve_label = MathTex(r"y = \sqrt{x}")
        self.add_fixed_in_frame_mobjects(curve_label)
        curve_label.to_corner(UL)

        self.play(Create(curve_2d), Write(curve_label))
        self.wait()

        # Show rotation
        rotation_text = Text("Rotate around x-axis...", font_size=24)
        self.add_fixed_in_frame_mobjects(rotation_text)
        rotation_text.to_edge(DOWN)
        self.play(Write(rotation_text))

        # Create surface of revolution
        surface = Surface(
            lambda u, v: axes.c2p(
                u,
                func(u) * np.cos(v),
                func(u) * np.sin(v)
            ),
            u_range=[0.01, 3],
            v_range=[0, TAU],
            resolution=(20, 30),
            fill_opacity=0.6,
            checkerboard_colors=[BLUE_D, BLUE_E],
        )

        self.play(
            Create(surface),
            FadeOut(curve_2d),
            run_time=3
        )

        self.play(FadeOut(rotation_text))

        # Volume formula
        volume = MathTex(r"V = \pi \int_0^3 x \, dx = \frac{9\pi}{2}")
        self.add_fixed_in_frame_mobjects(volume)
        volume.to_edge(DOWN)
        self.play(Write(volume))

        # Rotate to show the solid
        self.begin_ambient_camera_rotation(rate=0.2)
        self.wait(6)
        self.stop_ambient_camera_rotation()
```

## Example 4: Vector Field in 3D

```python
from manim import *

class VectorField3D(ThreeDScene):
    """
    Animation showing a 3D vector field.

    Mathematical concept: Vector fields in 3D space
    Key insight: Visualizing flow and force fields
    """

    def construct(self):
        self.set_camera_orientation(phi=70 * DEGREES, theta=-45 * DEGREES)

        # Axes
        axes = ThreeDAxes(
            x_range=[-3, 3], y_range=[-3, 3], z_range=[-2, 2],
        )
        self.play(Create(axes))

        # Title
        title = Text("Vector Field: F = (-y, x, 0)", font_size=32)
        self.add_fixed_in_frame_mobjects(title)
        title.to_edge(UP)
        self.play(Write(title))

        # Vector field function (rotation around z-axis)
        def field_func(point):
            x, y, z = point
            return np.array([-y, x, 0]) * 0.3

        # Create arrows at grid points
        arrows = VGroup()
        for x in np.arange(-2, 2.5, 1):
            for y in np.arange(-2, 2.5, 1):
                for z in np.arange(-1, 1.5, 1):
                    point = np.array([x, y, z])
                    direction = field_func(point)
                    if np.linalg.norm(direction) > 0.1:
                        arrow = Arrow3D(
                            start=point,
                            end=point + direction,
                            color=BLUE,
                            thickness=0.02,
                        )
                        arrows.add(arrow)

        self.play(Create(arrows), run_time=3)

        # Show a particle following the field
        particle = Sphere(radius=0.1, color=RED).move_to([1, 0, 0])
        self.play(Create(particle))

        # Animate particle in circular motion
        self.play(
            Rotate(particle, angle=2*PI, about_point=ORIGIN, axis=OUT),
            run_time=4
        )

        # Rotate camera
        self.begin_ambient_camera_rotation(rate=0.15)
        self.wait(5)
```

## Example 5: Parametric Curve - Helix

```python
from manim import *

class Helix3D(ThreeDScene):
    """
    Animation showing a 3D helix (spiral).

    Mathematical concept: Parametric curves in 3D
    Key insight: Using parameter t to describe position in space
    """

    def construct(self):
        self.set_camera_orientation(phi=75 * DEGREES, theta=-45 * DEGREES)

        # Axes
        axes = ThreeDAxes(
            x_range=[-2, 2], y_range=[-2, 2], z_range=[0, 8],
            z_length=6,
        )
        self.play(Create(axes))

        # Parametric equations
        equations = VGroup(
            MathTex(r"x(t) = \cos(t)"),
            MathTex(r"y(t) = \sin(t)"),
            MathTex(r"z(t) = \frac{t}{2}"),
        ).arrange(DOWN)

        self.add_fixed_in_frame_mobjects(equations)
        equations.to_corner(UL)
        self.play(Write(equations))

        # Helix curve
        helix = ParametricFunction(
            lambda t: axes.c2p(np.cos(t), np.sin(t), t / 2),
            t_range=[0, 4 * PI],
            color=YELLOW,
            stroke_width=4,
        )

        # Trace the helix
        self.play(Create(helix), run_time=4)

        # Add a moving dot
        t = ValueTracker(0)

        dot = always_redraw(
            lambda: Sphere(radius=0.1, color=RED).move_to(
                axes.c2p(
                    np.cos(t.get_value()),
                    np.sin(t.get_value()),
                    t.get_value() / 2
                )
            )
        )

        self.add(dot)
        self.play(t.animate.set_value(4 * PI), run_time=4, rate_func=linear)

        # Rotate camera
        self.begin_ambient_camera_rotation(rate=0.2)
        self.wait(5)
```

## Example 6: Cross Section of Sphere

```python
from manim import *

class SphereCrossSection(ThreeDScene):
    """
    Animation showing cross sections of a sphere.

    Mathematical concept: Slicing 3D objects
    Key insight: Understanding 3D shapes through 2D slices
    """

    def construct(self):
        self.set_camera_orientation(phi=75 * DEGREES, theta=-45 * DEGREES)

        # Create transparent sphere
        sphere = Sphere(
            radius=2,
            fill_opacity=0.3,
            resolution=(30, 30),
        )
        self.play(Create(sphere))

        # Create a slicing plane
        plane = Surface(
            lambda u, v: np.array([u, v, 0]),
            u_range=[-2.5, 2.5],
            v_range=[-2.5, 2.5],
            fill_opacity=0.5,
            fill_color=YELLOW,
            checkerboard_colors=[YELLOW_D, YELLOW_E],
        )

        self.play(Create(plane))

        # Show the circular cross section
        circle = Circle(radius=2, color=RED, stroke_width=4)
        circle.rotate(PI/2, axis=RIGHT)
        self.play(Create(circle))

        # Move the plane up and show changing cross section
        for z in [0.5, 1, 1.5]:
            new_radius = np.sqrt(4 - z**2)
            new_circle = Circle(radius=new_radius, color=RED, stroke_width=4)
            new_circle.rotate(PI/2, axis=RIGHT)
            new_circle.shift(OUT * z)

            self.play(
                plane.animate.shift(OUT * (z if z == 0.5 else 0.5)),
                Transform(circle, new_circle),
                run_time=1.5
            )
            self.wait(0.5)

        # Label
        radius_label = MathTex(r"r = \sqrt{R^2 - z^2}")
        self.add_fixed_in_frame_mobjects(radius_label)
        radius_label.to_edge(DOWN)
        self.play(Write(radius_label))

        self.wait(2)
```
