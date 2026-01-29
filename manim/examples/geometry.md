# Geometry Animation Examples

## Example 1: Pythagorean Theorem

Visual proof using squares on triangle sides.

```python
from manim import *

class PythagoreanTheorem(Scene):
    """
    Animation proving the Pythagorean theorem visually.

    Mathematical concept: a² + b² = c²
    Key insight: Area of squares on legs equals area of square on hypotenuse
    """

    def construct(self):
        self.introduce_concept()
        self.build_construction()
        self.demonstrate_proof()
        self.conclude()

    def introduce_concept(self):
        title = Text("Pythagorean Theorem", font_size=48)
        theorem = MathTex(r"a^2 + b^2 = c^2")
        theorem.next_to(title, DOWN)

        self.play(Write(title))
        self.play(Write(theorem))
        self.wait()
        self.play(FadeOut(title), theorem.animate.to_edge(UP))

    def build_construction(self):
        # Right triangle
        A = [-2, -1, 0]
        B = [1, -1, 0]
        C = [-2, 2, 0]

        self.triangle = Polygon(A, B, C, color=WHITE)

        # Labels
        a_label = MathTex("a").next_to(Line(A, C), LEFT)
        b_label = MathTex("b").next_to(Line(A, B), DOWN)
        c_label = MathTex("c").next_to(Line(B, C), RIGHT)

        # Right angle marker
        right_angle = RightAngle(
            Line(A, B), Line(A, C), length=0.3, color=YELLOW
        )

        self.play(Create(self.triangle))
        self.play(
            Write(a_label), Write(b_label), Write(c_label),
            Create(right_angle)
        )
        self.wait()

        # Store for later
        self.A, self.B, self.C = A, B, C

    def demonstrate_proof(self):
        # Create squares on each side
        a_len = 3  # |AC|
        b_len = 3  # |AB|
        c_len = np.sqrt(18)  # |BC|

        # Square on side a (left side)
        a_square = Square(side_length=a_len, fill_opacity=0.5, fill_color=RED)
        a_square.next_to(Line(self.A, self.C), LEFT, buff=0)

        # Square on side b (bottom)
        b_square = Square(side_length=b_len, fill_opacity=0.5, fill_color=BLUE)
        b_square.next_to(Line(self.A, self.B), DOWN, buff=0)

        # Square on hypotenuse (approximate position)
        c_square = Square(side_length=c_len, fill_opacity=0.5, fill_color=GREEN)
        c_square.rotate(np.arctan(3/3))
        c_square.move_to([2.5, 1.5, 0])

        self.play(Create(a_square))
        a_area = MathTex(r"a^2", color=RED).move_to(a_square)
        self.play(Write(a_area))

        self.play(Create(b_square))
        b_area = MathTex(r"b^2", color=BLUE).move_to(b_square)
        self.play(Write(b_area))

        self.play(Create(c_square))
        c_area = MathTex(r"c^2", color=GREEN).move_to(c_square)
        self.play(Write(c_area))
        self.wait()

    def conclude(self):
        conclusion = MathTex(
            r"\text{Area}(a^2) + \text{Area}(b^2) = \text{Area}(c^2)"
        )
        conclusion.to_edge(DOWN)
        self.play(Write(conclusion))

        box = SurroundingRectangle(conclusion, color=YELLOW)
        self.play(Create(box))
        self.wait(2)
```

## Example 2: Circle Theorems - Inscribed Angle

```python
from manim import *

class InscribedAngle(Scene):
    """
    Animation showing inscribed angle theorem.

    Mathematical concept: Inscribed angle = half central angle
    Key insight: Angle at center is twice angle at circumference
    """

    def construct(self):
        # Circle
        circle = Circle(radius=2, color=WHITE)
        center = Dot(ORIGIN, color=YELLOW)
        center_label = MathTex("O").next_to(center, DOWN)

        self.play(Create(circle), Create(center), Write(center_label))

        # Points on circle
        A = circle.point_at_angle(0)
        B = circle.point_at_angle(2*PI/3)
        P = circle.point_at_angle(4*PI/3)

        dot_A = Dot(A, color=RED)
        dot_B = Dot(B, color=RED)
        dot_P = Dot(P, color=BLUE)

        self.play(Create(dot_A), Create(dot_B), Create(dot_P))

        # Labels
        label_A = MathTex("A").next_to(A, RIGHT)
        label_B = MathTex("B").next_to(B, UL)
        label_P = MathTex("P").next_to(P, DL)
        self.play(Write(label_A), Write(label_B), Write(label_P))

        # Arc AB (the intercepted arc)
        arc = Arc(radius=2, start_angle=0, angle=2*PI/3, color=YELLOW)
        self.play(Create(arc))

        # Central angle
        line_OA = Line(ORIGIN, A, color=GREEN)
        line_OB = Line(ORIGIN, B, color=GREEN)
        central_angle = Angle(line_OA, line_OB, radius=0.5, color=GREEN)

        self.play(Create(line_OA), Create(line_OB), Create(central_angle))
        central_label = MathTex(r"\theta", color=GREEN).move_to([0.3, 0.5, 0])
        self.play(Write(central_label))

        # Inscribed angle
        line_PA = Line(P, A, color=BLUE)
        line_PB = Line(P, B, color=BLUE)
        inscribed_angle = Angle(line_PA, line_PB, radius=0.4, color=BLUE)

        self.play(Create(line_PA), Create(line_PB), Create(inscribed_angle))
        inscribed_label = MathTex(r"\frac{\theta}{2}", color=BLUE)
        inscribed_label.next_to(P, UP + RIGHT, buff=0.5)
        self.play(Write(inscribed_label))

        # Theorem statement
        theorem = MathTex(
            r"\text{Inscribed angle} = \frac{1}{2} \times \text{Central angle}"
        )
        theorem.to_edge(DOWN)
        self.play(Write(theorem))
        self.wait(2)
```

## Example 3: Similar Triangles

```python
from manim import *

class SimilarTriangles(Scene):
    """
    Animation showing similar triangle properties.

    Mathematical concept: Similar triangles have proportional sides
    Key insight: Same shape, different size
    """

    def construct(self):
        title = Text("Similar Triangles", font_size=40)
        self.play(Write(title))
        self.wait()
        self.play(title.animate.to_edge(UP))

        # First triangle
        t1 = Polygon(
            [-4, -1, 0], [-2, -1, 0], [-4, 1, 0],
            color=BLUE, fill_opacity=0.3
        )
        t1_labels = VGroup(
            MathTex("a").next_to(Line([-4,-1,0], [-4,1,0]), LEFT),
            MathTex("b").next_to(Line([-4,-1,0], [-2,-1,0]), DOWN),
            MathTex("c").next_to(Line([-2,-1,0], [-4,1,0]), UR),
        )

        # Second triangle (scaled by 2)
        t2 = Polygon(
            [0, -1.5, 0], [4, -1.5, 0], [0, 2.5, 0],
            color=RED, fill_opacity=0.3
        )
        t2_labels = VGroup(
            MathTex("2a").next_to(Line([0,-1.5,0], [0,2.5,0]), LEFT),
            MathTex("2b").next_to(Line([0,-1.5,0], [4,-1.5,0]), DOWN),
            MathTex("2c").next_to(Line([4,-1.5,0], [0,2.5,0]), UR),
        )

        self.play(Create(t1), Write(t1_labels))
        self.wait()
        self.play(Create(t2), Write(t2_labels))
        self.wait()

        # Show ratio
        ratio = MathTex(
            r"\frac{2a}{a} = \frac{2b}{b} = \frac{2c}{c} = 2"
        )
        ratio.to_edge(DOWN)
        self.play(Write(ratio))

        # Indicate similarity symbol
        similarity = MathTex(r"\triangle_1 \sim \triangle_2")
        similarity.next_to(title, DOWN)
        self.play(Write(similarity))
        self.wait(2)
```

## Example 4: Angle Sum in Triangle

```python
from manim import *

class TriangleAngleSum(Scene):
    """
    Animation proving angles in triangle sum to 180°.

    Mathematical concept: α + β + γ = 180°
    Key insight: Use parallel line to show angles
    """

    def construct(self):
        # Triangle
        A = [-2, -1, 0]
        B = [2, -1, 0]
        C = [0, 2, 0]

        triangle = Polygon(A, B, C, color=WHITE)
        self.play(Create(triangle))

        # Angles with colors
        angle_A = Angle(
            Line(A, B), Line(A, C), radius=0.4, color=RED
        )
        angle_B = Angle(
            Line(B, C), Line(B, A), radius=0.4, color=GREEN
        )
        angle_C = Angle(
            Line(C, A), Line(C, B), radius=0.4, color=BLUE
        )

        self.play(Create(angle_A), Create(angle_B), Create(angle_C))

        # Labels
        alpha = MathTex(r"\alpha", color=RED).next_to(A, UP + RIGHT, buff=0.5)
        beta = MathTex(r"\beta", color=GREEN).next_to(B, UP + LEFT, buff=0.5)
        gamma = MathTex(r"\gamma", color=BLUE).next_to(C, DOWN, buff=0.5)

        self.play(Write(alpha), Write(beta), Write(gamma))
        self.wait()

        # Draw parallel line through C
        parallel = Line([-3, 2, 0], [3, 2, 0], color=YELLOW)
        parallel_label = Text("parallel to AB", font_size=20)
        parallel_label.next_to(parallel, UP)

        self.play(Create(parallel), Write(parallel_label))

        # Show alternate angles
        alt_angle_left = Angle(
            Line(C, [-3, 2, 0]), Line(C, A), radius=0.4, color=RED
        )
        alt_angle_right = Angle(
            Line(C, B), Line(C, [3, 2, 0]), radius=0.4, color=GREEN
        )

        self.play(Create(alt_angle_left), Create(alt_angle_right))

        # Show they form straight line
        straight_line = Line([-2, 2, 0], [2, 2, 0], color=WHITE, stroke_width=6)
        self.play(
            Indicate(straight_line),
            run_time=2
        )

        # Conclusion
        conclusion = MathTex(
            r"\alpha + \gamma + \beta = 180°"
        )
        conclusion.to_edge(DOWN)
        self.play(Write(conclusion))

        box = SurroundingRectangle(conclusion, color=YELLOW)
        self.play(Create(box))
        self.wait(2)
```

## Example 5: Construction - Perpendicular Bisector

```python
from manim import *

class PerpendicularBisector(Scene):
    """
    Animation showing construction of perpendicular bisector.

    Mathematical concept: Compass and straightedge construction
    Key insight: Points equidistant from endpoints lie on perpendicular bisector
    """

    def construct(self):
        title = Text("Constructing Perpendicular Bisector", font_size=32)
        title.to_edge(UP)
        self.play(Write(title))

        # Line segment AB
        A = [-2, 0, 0]
        B = [2, 0, 0]
        segment = Line(A, B, color=WHITE)

        dot_A = Dot(A, color=RED)
        dot_B = Dot(B, color=RED)
        label_A = MathTex("A").next_to(A, DOWN)
        label_B = MathTex("B").next_to(B, DOWN)

        self.play(Create(segment), Create(dot_A), Create(dot_B))
        self.play(Write(label_A), Write(label_B))
        self.wait()

        # Step 1: Draw arc from A
        step1 = Text("1. Draw arc from A", font_size=24).to_corner(UL)
        self.play(Write(step1))

        arc_A = Arc(
            radius=2.5, start_angle=-PI/3, angle=2*PI/3,
            arc_center=A, color=BLUE
        )
        self.play(Create(arc_A))

        # Step 2: Draw arc from B
        step2 = Text("2. Draw arc from B (same radius)", font_size=24)
        step2.next_to(step1, DOWN, aligned_edge=LEFT)
        self.play(Write(step2))

        arc_B = Arc(
            radius=2.5, start_angle=2*PI/3, angle=2*PI/3,
            arc_center=B, color=GREEN
        )
        self.play(Create(arc_B))

        # Intersection points
        P = [0, np.sqrt(2.5**2 - 2**2), 0]
        Q = [0, -np.sqrt(2.5**2 - 2**2), 0]

        dot_P = Dot(P, color=YELLOW)
        dot_Q = Dot(Q, color=YELLOW)
        self.play(Create(dot_P), Create(dot_Q))

        # Step 3: Draw line through intersections
        step3 = Text("3. Connect intersection points", font_size=24)
        step3.next_to(step2, DOWN, aligned_edge=LEFT)
        self.play(Write(step3))

        bisector = Line(
            [0, -2.5, 0], [0, 2.5, 0],
            color=YELLOW, stroke_width=3
        )
        self.play(Create(bisector))

        # Mark midpoint
        midpoint = Dot(ORIGIN, color=RED)
        M_label = MathTex("M").next_to(ORIGIN, DR)
        self.play(Create(midpoint), Write(M_label))

        # Mark right angle
        right_angle = RightAngle(
            segment, bisector, length=0.3, color=WHITE
        )
        self.play(Create(right_angle))

        # Conclusion
        result = MathTex(r"AM = MB \text{ and } PM \perp AB")
        result.to_edge(DOWN)
        self.play(Write(result))
        self.wait(2)
```
