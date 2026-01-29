# Animation Patterns for Educational Math Videos

## Pattern 1: Step-by-Step Equation Reveal

Show equations building up piece by piece.

```python
def equation_reveal(self):
    # Start with simple form
    eq1 = MathTex(r"(a + b)^2")
    self.play(Write(eq1))
    self.wait()

    # Expand step by step
    eq2 = MathTex(r"= (a + b)(a + b)")
    eq2.next_to(eq1, DOWN)
    self.play(Write(eq2))
    self.wait()

    eq3 = MathTex(r"= a^2 + ab + ba + b^2")
    eq3.next_to(eq2, DOWN)
    self.play(Write(eq3))
    self.wait()

    eq4 = MathTex(r"= a^2 + 2ab + b^2")
    eq4.next_to(eq3, DOWN)
    self.play(Write(eq4))

    # Highlight final result
    box = SurroundingRectangle(eq4, color=YELLOW)
    self.play(Create(box))
```

## Pattern 2: Transform Between Equations

Morph one equation into another.

```python
def equation_transform(self):
    eq1 = MathTex(r"x^2 - 4")
    self.play(Write(eq1))
    self.wait()

    eq2 = MathTex(r"(x-2)(x+2)")
    self.play(TransformMatchingTex(eq1, eq2))
    self.wait()
```

## Pattern 3: Graph with Tracing Dot

Show a function being traced by a moving dot.

```python
def trace_graph(self):
    axes = Axes(x_range=[-3, 3], y_range=[-1, 9])
    func = lambda x: x**2
    graph = axes.plot(func, color=BLUE)

    self.play(Create(axes))

    # Value tracker for animation
    t = ValueTracker(-3)

    # Dot that follows the curve
    dot = always_redraw(
        lambda: Dot(
            axes.c2p(t.get_value(), func(t.get_value())),
            color=YELLOW
        )
    )

    # Traced path
    trace = always_redraw(
        lambda: axes.plot(func, x_range=[-3, t.get_value()], color=BLUE)
    )

    self.add(dot, trace)
    self.play(t.animate.set_value(3), run_time=4, rate_func=linear)
```

## Pattern 4: Area Under Curve (Riemann Sum)

Show rectangles approximating area.

```python
def riemann_sum(self):
    axes = Axes(x_range=[0, 4], y_range=[0, 5])
    func = lambda x: 0.5 * x**2
    graph = axes.plot(func, color=BLUE)

    self.play(Create(axes), Create(graph))

    # Start with few rectangles
    for n in [4, 8, 16, 32]:
        rects = axes.get_riemann_rectangles(
            graph,
            x_range=[0, 4],
            dx=4/n,
            input_sample_type="center",
            fill_opacity=0.5,
            stroke_width=0.5,
        )

        if n == 4:
            self.play(Create(rects))
        else:
            self.play(Transform(rects, rects))
        self.wait(0.5)
```

## Pattern 5: Tangent Line Animation

Show derivative as tangent slope.

```python
def tangent_line(self):
    axes = Axes(x_range=[-2, 4], y_range=[-1, 9])
    func = lambda x: x**2
    deriv = lambda x: 2*x
    graph = axes.plot(func, color=BLUE)

    self.play(Create(axes), Create(graph))

    t = ValueTracker(1)

    # Point on curve
    dot = always_redraw(
        lambda: Dot(axes.c2p(t.get_value(), func(t.get_value())), color=RED)
    )

    # Tangent line at point
    tangent = always_redraw(
        lambda: axes.plot(
            lambda x: deriv(t.get_value()) * (x - t.get_value()) + func(t.get_value()),
            x_range=[t.get_value() - 1.5, t.get_value() + 1.5],
            color=YELLOW
        )
    )

    # Slope label
    slope_label = always_redraw(
        lambda: MathTex(f"m = {2*t.get_value():.1f}").to_corner(UR)
    )

    self.add(dot, tangent, slope_label)
    self.play(t.animate.set_value(3), run_time=4)
```

## Pattern 6: Vector Transformation

Show how vectors transform under a matrix.

```python
def vector_transform(self):
    plane = NumberPlane()
    self.play(Create(plane))

    # Original vectors
    v1 = Arrow(ORIGIN, [1, 0, 0], color=RED, buff=0)
    v2 = Arrow(ORIGIN, [0, 1, 0], color=GREEN, buff=0)

    self.play(Create(v1), Create(v2))

    # Transformation matrix
    matrix = [[2, 1], [0, 1]]  # shear

    # Apply transformation
    self.play(
        v1.animate.put_start_and_end_on(ORIGIN, [2, 0, 0]),
        v2.animate.put_start_and_end_on(ORIGIN, [1, 1, 0]),
        plane.animate.apply_matrix(matrix),
        run_time=2
    )
```

## Pattern 7: Geometric Construction

Build up a geometric figure step by step.

```python
def geometric_construction(self):
    # Start with triangle
    A, B, C = [-2, -1, 0], [2, -1, 0], [0, 2, 0]
    triangle = Polygon(A, B, C, color=WHITE)

    self.play(Create(triangle))

    # Add labels
    label_A = MathTex("A").next_to(A, DL)
    label_B = MathTex("B").next_to(B, DR)
    label_C = MathTex("C").next_to(C, UP)

    self.play(Write(label_A), Write(label_B), Write(label_C))

    # Draw altitude
    foot = [0, -1, 0]
    altitude = DashedLine(C, foot, color=YELLOW)
    self.play(Create(altitude))

    # Mark right angle
    right_angle = RightAngle(
        Line(foot, B), Line(foot, C), length=0.3, color=YELLOW
    )
    self.play(Create(right_angle))
```

## Pattern 8: Parameter Slider Effect

Show how changing a parameter affects a graph.

```python
def parameter_slider(self):
    axes = Axes(x_range=[-4, 4], y_range=[-2, 2])
    self.play(Create(axes))

    a = ValueTracker(1)

    # Graph that depends on parameter
    graph = always_redraw(
        lambda: axes.plot(
            lambda x: np.sin(a.get_value() * x),
            color=BLUE
        )
    )

    # Parameter display
    param_text = always_redraw(
        lambda: MathTex(f"y = \\sin({a.get_value():.1f}x)").to_corner(UR)
    )

    self.add(graph, param_text)

    # Animate parameter change
    self.play(a.animate.set_value(3), run_time=3)
    self.play(a.animate.set_value(0.5), run_time=3)
```

## Pattern 9: Zoom Into Detail

Zoom camera to show detail.

```python
# Use MovingCameraScene
class ZoomExample(MovingCameraScene):
    def construct(self):
        circle = Circle(radius=2)
        self.play(Create(circle))

        # Zoom in
        self.play(
            self.camera.frame.animate.scale(0.5).move_to(circle.get_top()),
            run_time=2
        )

        # Show detail at zoomed level
        detail = Dot(circle.get_top(), color=RED)
        self.play(Create(detail))

        # Zoom back out
        self.play(
            self.camera.frame.animate.scale(2).move_to(ORIGIN),
            run_time=2
        )
```

## Pattern 10: Before/After Comparison

Show two states side by side.

```python
def comparison(self):
    # Before state
    before_title = Text("Before").scale(0.7)
    before_obj = Circle(color=RED)
    before_group = VGroup(before_title, before_obj).arrange(DOWN)
    before_group.shift(LEFT * 3)

    # After state
    after_title = Text("After").scale(0.7)
    after_obj = Square(color=GREEN)
    after_group = VGroup(after_title, after_obj).arrange(DOWN)
    after_group.shift(RIGHT * 3)

    # Show comparison
    self.play(FadeIn(before_group), FadeIn(after_group))

    # Arrow connecting them
    arrow = Arrow(before_obj.get_right(), after_obj.get_left())
    self.play(Create(arrow))
```

## Pattern 11: Cascading Animations

Stagger multiple similar animations.

```python
def cascade(self):
    dots = VGroup(*[Dot() for _ in range(10)])
    dots.arrange(RIGHT)

    # All fade in with lag
    self.play(LaggedStart(
        *[FadeIn(dot, scale=0.5) for dot in dots],
        lag_ratio=0.1
    ))

    # All change color with lag
    self.play(LaggedStart(
        *[dot.animate.set_color(BLUE) for dot in dots],
        lag_ratio=0.1
    ))
```

## Pattern 12: Highlight and Explain

Draw attention to specific parts while explaining.

```python
def highlight_explain(self):
    equation = MathTex(r"E = mc^2")
    self.play(Write(equation))
    self.wait()

    # Highlight energy
    e_box = SurroundingRectangle(equation[0][0], color=YELLOW)
    e_label = Text("Energy", font_size=24).next_to(e_box, UP)
    self.play(Create(e_box), Write(e_label))
    self.wait()
    self.play(FadeOut(e_box), FadeOut(e_label))

    # Highlight mass
    m_box = SurroundingRectangle(equation[0][2], color=GREEN)
    m_label = Text("Mass", font_size=24).next_to(m_box, UP)
    self.play(Create(m_box), Write(m_label))
    self.wait()
    self.play(FadeOut(m_box), FadeOut(m_label))

    # Highlight speed of light
    c_box = SurroundingRectangle(equation[0][3:], color=BLUE)
    c_label = Text("Speed of light squared", font_size=24).next_to(c_box, UP)
    self.play(Create(c_box), Write(c_label))
```
