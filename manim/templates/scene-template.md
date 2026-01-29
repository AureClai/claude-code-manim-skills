# Scene Template

Use this template as the foundation for generating Manim animations.

## Standard 2D Scene Template

```python
from manim import *

class {ClassName}(Scene):
    """
    Animation explaining {concept}.

    Mathematical concept: {mathematical_description}
    Key insight: {what_viewer_should_understand}
    """

    def construct(self):
        # Phase 1: Introduction
        self.introduce_concept()

        # Phase 2: Build-up
        self.build_components()

        # Phase 3: Core demonstration
        self.demonstrate()

        # Phase 4: Conclusion
        self.conclude()

    def introduce_concept(self):
        """Display title and set up the problem."""
        title = Text("{Title}", font_size=48)
        subtitle = MathTex(r"{main_equation}")
        subtitle.next_to(title, DOWN)

        self.play(Write(title))
        self.play(Write(subtitle))
        self.wait()
        self.play(
            FadeOut(title),
            subtitle.animate.to_edge(UP)
        )

    def build_components(self):
        """Introduce visual elements step by step."""
        # Create axes if needed
        self.axes = Axes(
            x_range=[{x_min}, {x_max}, {x_step}],
            y_range=[{y_min}, {y_max}, {y_step}],
            axis_config={"include_numbers": True}
        )
        self.play(Create(self.axes))

        # Create main objects
        # self.main_object = ...
        # self.play(Create(self.main_object))

    def demonstrate(self):
        """Animate the main concept."""
        # Use ValueTracker for dynamic animations
        # t = ValueTracker(0)
        # ...
        # self.play(t.animate.set_value(end_value), run_time=3)
        pass

    def conclude(self):
        """Highlight the key insight."""
        conclusion = MathTex(r"{final_equation}")
        conclusion.to_edge(DOWN)

        box = SurroundingRectangle(conclusion, color=YELLOW)

        self.play(Write(conclusion))
        self.play(Create(box))
        self.wait(2)
```

## 3D Scene Template

```python
from manim import *

class {ClassName}(ThreeDScene):
    """
    3D animation showing {concept}.

    Mathematical concept: {mathematical_description}
    Key insight: {what_viewer_should_understand}
    """

    def construct(self):
        # Camera setup
        self.set_camera_orientation(
            phi=75 * DEGREES,    # vertical tilt
            theta=-45 * DEGREES  # horizontal rotation
        )

        # Phase 1: Introduction
        self.introduce_concept()

        # Phase 2: Build 3D scene
        self.build_scene()

        # Phase 3: Core demonstration
        self.demonstrate()

        # Phase 4: Conclusion with camera rotation
        self.conclude()

    def introduce_concept(self):
        """Display title (fixed to frame, not in 3D space)."""
        title = Text("{Title}", font_size=36)
        self.add_fixed_in_frame_mobjects(title)
        title.to_corner(UL)
        self.play(Write(title))

    def build_scene(self):
        """Create 3D axes and objects."""
        self.axes = ThreeDAxes(
            x_range=[{x_min}, {x_max}, {x_step}],
            y_range=[{y_min}, {y_max}, {y_step}],
            z_range=[{z_min}, {z_max}, {z_step}],
        )
        self.play(Create(self.axes))

        # Create 3D objects
        # surface = Surface(...)
        # self.play(Create(surface))

    def demonstrate(self):
        """Animate the main concept in 3D."""
        # Create and animate 3D objects
        pass

    def conclude(self):
        """Show the result with rotating camera view."""
        # Add conclusion text (fixed to frame)
        conclusion = MathTex(r"{final_result}")
        self.add_fixed_in_frame_mobjects(conclusion)
        conclusion.to_edge(DOWN)
        self.play(Write(conclusion))

        # Rotate camera to showcase the 3D result
        self.begin_ambient_camera_rotation(rate=0.15)
        self.wait(5)
        self.stop_ambient_camera_rotation()
```

## Graph Animation Template

```python
from manim import *

class {ClassName}(Scene):
    """
    Animation showing graph of {function_description}.
    """

    def construct(self):
        # Create axes
        axes = Axes(
            x_range=[{x_min}, {x_max}, {x_step}],
            y_range=[{y_min}, {y_max}, {y_step}],
            x_length=8,
            y_length=5,
            axis_config={"include_numbers": True, "include_tip": True}
        )

        # Add axis labels
        x_label = axes.get_x_axis_label("{x_label}")
        y_label = axes.get_y_axis_label("{y_label}")

        self.play(Create(axes), Write(x_label), Write(y_label))

        # Define and plot function
        func = lambda x: {function_expression}
        graph = axes.plot(func, color=BLUE)
        graph_label = axes.get_graph_label(graph, label=r"{function_latex}")

        self.play(Create(graph), Write(graph_label))

        # Optional: animate with ValueTracker
        t = ValueTracker({start_value})

        dot = always_redraw(
            lambda: Dot(
                axes.c2p(t.get_value(), func(t.get_value())),
                color=RED
            )
        )

        self.add(dot)
        self.play(t.animate.set_value({end_value}), run_time=3)
        self.wait()
```

## Transformation Animation Template

```python
from manim import *

class {ClassName}(Scene):
    """
    Animation showing transformation from one state to another.
    """

    def construct(self):
        # Initial state
        initial = {initial_mobject}
        initial_label = MathTex(r"{initial_label}")
        initial_label.next_to(initial, DOWN)

        self.play(Create(initial), Write(initial_label))
        self.wait()

        # Transform to final state
        final = {final_mobject}
        final_label = MathTex(r"{final_label}")
        final_label.next_to(final, DOWN)

        self.play(
            Transform(initial, final),
            Transform(initial_label, final_label),
            run_time=2
        )
        self.wait()
```

## Proof Animation Template

```python
from manim import *

class {ClassName}(Scene):
    """
    Step-by-step proof animation.
    """

    def construct(self):
        title = Text("{Theorem Name}", font_size=40)
        self.play(Write(title))
        self.wait()
        self.play(title.animate.to_edge(UP))

        # Statement
        statement = MathTex(r"{theorem_statement}")
        self.play(Write(statement))
        self.wait()
        self.play(statement.animate.shift(UP * 2))

        # Proof steps
        steps = [
            r"{step_1}",
            r"{step_2}",
            r"{step_3}",
            # ...
        ]

        prev_step = None
        for i, step_tex in enumerate(steps):
            step = MathTex(step_tex)
            if prev_step:
                step.next_to(prev_step, DOWN)
            else:
                step.next_to(statement, DOWN, buff=1)

            self.play(Write(step))
            self.wait(0.5)
            prev_step = step

        # QED
        qed = MathTex(r"\square").to_corner(DR)
        self.play(Write(qed))
        self.wait()
```
