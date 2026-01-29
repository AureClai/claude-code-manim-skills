# Calculus Animation Examples

## Example 1: Riemann Integration

Shows rectangles approximating the area under a curve, with increasing precision.

```python
from manim import *

class RiemannIntegration(Scene):
    """
    Animation explaining Riemann Integration.

    Mathematical concept: Definite integrals as limits of Riemann sums
    Key insight: Area under curve = limit of rectangle approximations
    """

    def construct(self):
        self.introduce_concept()
        self.build_components()
        self.demonstrate()
        self.conclude()

    def introduce_concept(self):
        """Display title and problem statement."""
        title = Text("Riemann Integration", font_size=48)
        subtitle = MathTex(r"\int_a^b f(x) \, dx")
        subtitle.next_to(title, DOWN)

        self.play(Write(title))
        self.play(Write(subtitle))
        self.wait()
        self.play(FadeOut(title), subtitle.animate.to_edge(UP))

    def build_components(self):
        """Set up axes and function."""
        self.axes = Axes(
            x_range=[0, 4, 1],
            y_range=[0, 5, 1],
            x_length=8,
            y_length=5,
            axis_config={"include_numbers": True}
        )

        self.func = lambda x: 0.5 * x**2
        self.graph = self.axes.plot(self.func, color=BLUE, x_range=[0, 4])
        graph_label = self.axes.get_graph_label(
            self.graph, label=r"f(x) = \frac{1}{2}x^2"
        )

        self.play(Create(self.axes))
        self.play(Create(self.graph), Write(graph_label))
        self.wait()

    def demonstrate(self):
        """Show Riemann sums with increasing rectangles."""
        explanation = Text("Approximate area with rectangles", font_size=32)
        explanation.to_edge(DOWN)
        self.play(Write(explanation))

        prev_rects = None
        for n in [4, 8, 16, 32]:
            n_label = MathTex(f"n = {n}").to_corner(UR)

            rects = self.axes.get_riemann_rectangles(
                self.graph,
                x_range=[0, 4],
                dx=4/n,
                input_sample_type="center",
                stroke_width=0.5,
                stroke_color=WHITE,
                fill_opacity=0.6,
            )

            if prev_rects is None:
                self.play(Write(n_label), Create(rects))
            else:
                self.play(
                    Transform(prev_rects, rects),
                    Transform(prev_n_label, n_label)
                )
                rects = prev_rects

            prev_rects = rects
            prev_n_label = n_label
            self.wait(0.5)

        self.play(FadeOut(explanation))

    def conclude(self):
        """Show the exact integral result."""
        limit_text = MathTex(
            r"\lim_{n \to \infty} \sum_{i=1}^{n} f(x_i^*) \Delta x"
        )
        equals = MathTex(r"=")
        integral = MathTex(r"\int_0^4 \frac{1}{2}x^2 \, dx")
        result = MathTex(r"= \frac{32}{3}")

        equation = VGroup(limit_text, equals, integral, result)
        equation.arrange(RIGHT)
        equation.to_edge(DOWN)

        self.play(Write(equation))

        # Fill exact area
        area = self.axes.get_area(self.graph, x_range=[0, 4], color=GREEN, opacity=0.5)
        self.play(FadeIn(area))
        self.wait(2)
```

## Example 2: Derivative as Tangent Slope

```python
from manim import *

class DerivativeTangent(Scene):
    """
    Animation explaining the derivative as the slope of tangent line.

    Mathematical concept: f'(x) = lim[h→0] (f(x+h) - f(x))/h
    Key insight: Derivative gives instantaneous rate of change
    """

    def construct(self):
        # Setup
        axes = Axes(x_range=[-1, 4], y_range=[-1, 10])
        func = lambda x: x**2
        graph = axes.plot(func, color=BLUE)

        self.play(Create(axes), Create(graph))

        # Point of tangency
        x_val = ValueTracker(1)

        # Point on curve
        dot = always_redraw(
            lambda: Dot(
                axes.c2p(x_val.get_value(), func(x_val.get_value())),
                color=RED
            )
        )

        # Tangent line
        def get_tangent():
            x = x_val.get_value()
            slope = 2 * x  # derivative of x^2
            y = func(x)

            tangent = axes.plot(
                lambda t: slope * (t - x) + y,
                x_range=[x - 1.5, x + 1.5],
                color=YELLOW
            )
            return tangent

        tangent = always_redraw(get_tangent)

        # Slope display
        slope_text = always_redraw(
            lambda: MathTex(
                f"f'({x_val.get_value():.1f}) = {2*x_val.get_value():.1f}"
            ).to_corner(UR)
        )

        self.play(Create(dot), Create(tangent), Write(slope_text))

        # Animate x moving
        self.play(x_val.animate.set_value(3), run_time=4)
        self.play(x_val.animate.set_value(0.5), run_time=3)
        self.play(x_val.animate.set_value(2), run_time=2)
        self.wait()
```

## Example 3: Limit Visualization

```python
from manim import *

class LimitVisualization(Scene):
    """
    Animation showing how a limit works visually.

    Mathematical concept: lim[x→a] f(x) = L
    Key insight: As x approaches a, f(x) approaches L
    """

    def construct(self):
        axes = Axes(x_range=[-1, 5], y_range=[-1, 5])

        # Function with a hole at x=2
        func = lambda x: (x**2 - 4) / (x - 2) if abs(x - 2) > 0.01 else 4

        # Plot in two parts to show the hole
        graph_left = axes.plot(func, x_range=[-0.5, 1.99], color=BLUE)
        graph_right = axes.plot(func, x_range=[2.01, 4.5], color=BLUE)

        # The limit point
        limit_point = Dot(axes.c2p(2, 4), color=YELLOW)
        hole = Circle(radius=0.1, color=BLUE).move_to(axes.c2p(2, 4))

        self.play(Create(axes))
        self.play(Create(graph_left), Create(graph_right))
        self.play(Create(hole))

        # Show approaching from left
        left_dot = Dot(axes.c2p(0, func(0)), color=GREEN)
        self.play(Create(left_dot))

        for x in [1, 1.5, 1.8, 1.9, 1.95, 1.99]:
            self.play(
                left_dot.animate.move_to(axes.c2p(x, func(x))),
                run_time=0.5
            )

        # Show approaching from right
        right_dot = Dot(axes.c2p(4, func(4)), color=RED)
        self.play(Create(right_dot))

        for x in [3, 2.5, 2.2, 2.1, 2.05, 2.01]:
            self.play(
                right_dot.animate.move_to(axes.c2p(x, func(x))),
                run_time=0.5
            )

        # Show limit exists
        limit_label = MathTex(r"\lim_{x \to 2} \frac{x^2 - 4}{x - 2} = 4")
        limit_label.to_edge(UP)
        self.play(Write(limit_label), Create(limit_point))
        self.wait(2)
```

## Example 4: Taylor Series Approximation

```python
from manim import *

class TaylorSeries(Scene):
    """
    Animation showing Taylor series approximating sin(x).

    Mathematical concept: Taylor expansion around a point
    Key insight: Polynomials can approximate any smooth function
    """

    def construct(self):
        axes = Axes(
            x_range=[-4, 4],
            y_range=[-2, 2],
            x_length=10,
            y_length=5
        )

        # Actual sine function
        sin_graph = axes.plot(np.sin, color=BLUE)
        sin_label = MathTex(r"\sin(x)").next_to(sin_graph, UR)

        self.play(Create(axes), Create(sin_graph), Write(sin_label))
        self.wait()

        # Taylor polynomials of increasing order
        taylor_terms = [
            (1, r"x", lambda x: x),
            (3, r"x - \frac{x^3}{6}", lambda x: x - x**3/6),
            (5, r"x - \frac{x^3}{6} + \frac{x^5}{120}",
             lambda x: x - x**3/6 + x**5/120),
            (7, r"x - \frac{x^3}{6} + \frac{x^5}{120} - \frac{x^7}{5040}",
             lambda x: x - x**3/6 + x**5/120 - x**7/5040),
        ]

        taylor_graph = None
        taylor_label = None

        for order, formula, func in taylor_terms:
            new_graph = axes.plot(func, color=YELLOW, x_range=[-3.5, 3.5])
            new_label = MathTex(f"n={order}: " + formula, font_size=32)
            new_label.to_edge(DOWN)

            if taylor_graph is None:
                self.play(Create(new_graph), Write(new_label))
            else:
                self.play(
                    Transform(taylor_graph, new_graph),
                    Transform(taylor_label, new_label)
                )
                new_graph = taylor_graph
                new_label = taylor_label

            taylor_graph = new_graph
            taylor_label = new_label
            self.wait()

        self.wait(2)
```

## Example 5: Fundamental Theorem of Calculus

```python
from manim import *

class FundamentalTheorem(Scene):
    """
    Animation illustrating the Fundamental Theorem of Calculus.

    Mathematical concept: ∫[a,b] f'(x)dx = f(b) - f(a)
    Key insight: Integration and differentiation are inverse operations
    """

    def construct(self):
        # Title
        title = MathTex(
            r"\int_a^b f'(x) \, dx = f(b) - f(a)"
        ).scale(1.2)
        self.play(Write(title))
        self.wait()
        self.play(title.animate.to_edge(UP).scale(0.7))

        # Setup axes
        axes = Axes(x_range=[0, 5], y_range=[0, 6], x_length=8, y_length=4)
        axes.shift(DOWN)

        # f(x) and f'(x)
        f = lambda x: 0.5 * x**2
        f_prime = lambda x: x

        f_graph = axes.plot(f, color=BLUE)
        f_prime_graph = axes.plot(f_prime, color=GREEN)

        f_label = axes.get_graph_label(f_graph, label=r"f(x) = \frac{1}{2}x^2")
        f_prime_label = axes.get_graph_label(f_prime_graph, label=r"f'(x) = x")

        self.play(Create(axes))
        self.play(Create(f_graph), Write(f_label))
        self.wait()
        self.play(Create(f_prime_graph), Write(f_prime_label))
        self.wait()

        # Show area under f'(x) from 1 to 3
        a, b = 1, 3
        area = axes.get_area(f_prime_graph, x_range=[a, b], color=YELLOW, opacity=0.5)

        a_line = axes.get_vertical_line(axes.c2p(a, f_prime(a)), color=RED)
        b_line = axes.get_vertical_line(axes.c2p(b, f_prime(b)), color=RED)

        self.play(Create(a_line), Create(b_line))
        self.play(FadeIn(area))

        # Calculate values
        f_a = f(a)  # 0.5
        f_b = f(b)  # 4.5
        integral_value = f_b - f_a  # 4

        result = MathTex(
            f"f({b}) - f({a}) = {f_b} - {f_a} = {integral_value}"
        )
        result.next_to(axes, DOWN)
        self.play(Write(result))
        self.wait(2)
```
