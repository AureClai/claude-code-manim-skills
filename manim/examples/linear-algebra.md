# Linear Algebra Animation Examples

## Example 1: Matrix Transformation

Shows how a 2x2 matrix transforms the plane.

```python
from manim import *

class MatrixTransformation(Scene):
    """
    Animation showing how matrices transform space.

    Mathematical concept: Linear transformations via matrices
    Key insight: Matrices describe how basis vectors are transformed
    """

    def construct(self):
        self.introduce_concept()
        self.show_transformation()

    def introduce_concept(self):
        title = Text("Matrix Transformation", font_size=48)
        matrix = MathTex(r"A = \begin{bmatrix} 2 & 1 \\ 0 & 1 \end{bmatrix}")
        matrix.next_to(title, DOWN)

        self.play(Write(title))
        self.play(Write(matrix))
        self.wait()
        self.play(
            FadeOut(title),
            matrix.animate.to_corner(UL)
        )

    def show_transformation(self):
        # Create number plane
        plane = NumberPlane(
            x_range=[-4, 4],
            y_range=[-3, 3],
            background_line_style={"stroke_opacity": 0.5}
        )

        # Basis vectors
        i_hat = Arrow(ORIGIN, RIGHT, buff=0, color=GREEN)
        j_hat = Arrow(ORIGIN, UP, buff=0, color=RED)

        i_label = MathTex(r"\hat{i}", color=GREEN).next_to(i_hat, DOWN)
        j_label = MathTex(r"\hat{j}", color=RED).next_to(j_hat, LEFT)

        self.play(Create(plane))
        self.play(
            Create(i_hat), Create(j_hat),
            Write(i_label), Write(j_label)
        )
        self.wait()

        # Transformation matrix
        matrix = [[2, 1], [0, 1]]

        # Show where basis vectors go
        explanation = Text("Watch where the basis vectors land", font_size=28)
        explanation.to_edge(DOWN)
        self.play(Write(explanation))
        self.wait()

        # Apply transformation
        self.play(
            plane.animate.apply_matrix(matrix),
            i_hat.animate.put_start_and_end_on(ORIGIN, [2, 0, 0]),
            j_hat.animate.put_start_and_end_on(ORIGIN, [1, 1, 0]),
            i_label.animate.next_to([2, 0, 0], DOWN),
            j_label.animate.next_to([1, 1, 0], LEFT),
            run_time=3
        )

        # New basis vector values
        new_i = MathTex(r"\hat{i} \to \begin{bmatrix} 2 \\ 0 \end{bmatrix}", color=GREEN)
        new_j = MathTex(r"\hat{j} \to \begin{bmatrix} 1 \\ 1 \end{bmatrix}", color=RED)

        new_vectors = VGroup(new_i, new_j).arrange(DOWN).to_corner(UR)
        self.play(Write(new_vectors))
        self.wait(2)
```

## Example 2: Eigenvector Visualization

```python
from manim import *

class EigenvectorVisualization(Scene):
    """
    Animation showing eigenvectors remain on their span.

    Mathematical concept: Av = λv (eigenvectors only scale, don't rotate)
    Key insight: Eigenvectors are special directions that don't change direction
    """

    def construct(self):
        # Title
        title = Text("Eigenvectors: Vectors that only scale", font_size=36)
        self.play(Write(title))
        self.wait()
        self.play(title.animate.to_edge(UP).scale(0.7))

        # Setup
        plane = NumberPlane(
            x_range=[-4, 4],
            y_range=[-3, 3],
            background_line_style={"stroke_opacity": 0.4}
        )
        self.play(Create(plane))

        # Matrix with known eigenvectors
        # A = [[3, 1], [0, 2]] has eigenvectors [1,0] (λ=3) and [1,-1] (λ=2)
        matrix = [[3, 1], [0, 2]]

        # Regular vector (will rotate)
        regular_vec = Arrow(ORIGIN, [1, 1, 0], buff=0, color=YELLOW)
        regular_label = Text("Regular vector", font_size=24, color=YELLOW)
        regular_label.next_to(regular_vec.get_end(), RIGHT)

        # Eigenvector (will only scale)
        eigen_vec = Arrow(ORIGIN, [1, 0, 0], buff=0, color=RED)
        eigen_label = Text("Eigenvector", font_size=24, color=RED)
        eigen_label.next_to(eigen_vec.get_end(), DOWN)

        self.play(
            Create(regular_vec), Write(regular_label),
            Create(eigen_vec), Write(eigen_label)
        )
        self.wait()

        # Show transformation
        explanation = Text("Apply matrix transformation...", font_size=28)
        explanation.to_edge(DOWN)
        self.play(Write(explanation))

        # Transform
        # Regular: [1,1] -> [4, 2]
        # Eigen: [1,0] -> [3, 0]
        self.play(
            regular_vec.animate.put_start_and_end_on(ORIGIN, [4, 2, 0]),
            eigen_vec.animate.put_start_and_end_on(ORIGIN, [3, 0, 0]),
            regular_label.animate.next_to([4, 2, 0], RIGHT),
            eigen_label.animate.next_to([3, 0, 0], DOWN),
            run_time=2
        )

        # Highlight that eigenvector stayed on line
        eigen_line = DashedLine([-4, 0, 0], [4, 0, 0], color=RED)
        self.play(Create(eigen_line))

        result = MathTex(r"A\vec{v} = 3\vec{v}", color=RED)
        result.next_to(explanation, UP)
        self.play(Write(result))
        self.wait(2)
```

## Example 3: Determinant as Area

```python
from manim import *

class DeterminantArea(Scene):
    """
    Animation showing determinant as scaling factor of area.

    Mathematical concept: |det(A)| = area scaling factor
    Key insight: Determinant tells how much areas grow/shrink
    """

    def construct(self):
        title = Text("Determinant = Area Scaling Factor", font_size=36)
        self.play(Write(title))
        self.wait()
        self.play(title.animate.to_edge(UP).scale(0.8))

        # Initial unit square
        square = Square(side_length=2, fill_opacity=0.3, fill_color=BLUE)
        square.move_to(RIGHT + UP)

        area_label = MathTex(r"\text{Area} = 1").next_to(square, DOWN)

        self.play(Create(square), Write(area_label))
        self.wait()

        # Show matrix
        matrix_tex = MathTex(r"A = \begin{bmatrix} 2 & 1 \\ 1 & 3 \end{bmatrix}")
        det_tex = MathTex(r"\det(A) = 2 \cdot 3 - 1 \cdot 1 = 5")
        matrix_group = VGroup(matrix_tex, det_tex).arrange(DOWN)
        matrix_group.to_corner(UL)

        self.play(Write(matrix_group))
        self.wait()

        # Transform the square
        # Original corners: [0,0], [2,0], [2,2], [0,2] relative to center at [1,1]
        # After transform by [[2,1],[1,3]]:
        # This is a parallelogram

        new_vertices = [
            [0, 0, 0],
            [4, 2, 0],    # [2,0] * A^T
            [6, 8, 0],    # [2,2] * A^T
            [2, 6, 0],    # [0,2] * A^T
        ]

        parallelogram = Polygon(*new_vertices, fill_opacity=0.3, fill_color=GREEN)

        new_area = MathTex(r"\text{Area} = 5").next_to(parallelogram, DOWN)

        self.play(
            Transform(square, parallelogram),
            Transform(area_label, new_area),
            run_time=2
        )

        conclusion = Text("Area scaled by |det(A)| = 5", font_size=28)
        conclusion.to_edge(DOWN)
        self.play(Write(conclusion))
        self.wait(2)
```

## Example 4: Vector Addition

```python
from manim import *

class VectorAddition(Scene):
    """
    Animation showing vector addition geometrically.

    Mathematical concept: u + v = resultant vector
    Key insight: Parallelogram rule / tip-to-tail method
    """

    def construct(self):
        plane = NumberPlane(x_range=[-1, 6], y_range=[-1, 5])
        self.play(Create(plane))

        # Vectors
        u = Arrow(ORIGIN, [3, 1, 0], buff=0, color=RED)
        v = Arrow(ORIGIN, [1, 3, 0], buff=0, color=BLUE)

        u_label = MathTex(r"\vec{u}", color=RED).next_to(u.get_center(), DOWN)
        v_label = MathTex(r"\vec{v}", color=BLUE).next_to(v.get_center(), LEFT)

        self.play(Create(u), Create(v), Write(u_label), Write(v_label))
        self.wait()

        # Tip-to-tail method
        explanation = Text("Tip-to-tail method", font_size=32).to_edge(UP)
        self.play(Write(explanation))

        # Move v to tip of u
        v_copy = Arrow([3, 1, 0], [4, 4, 0], buff=0, color=BLUE)
        self.play(Create(v_copy))

        # Resultant
        resultant = Arrow(ORIGIN, [4, 4, 0], buff=0, color=YELLOW)
        result_label = MathTex(r"\vec{u} + \vec{v}", color=YELLOW)
        result_label.next_to(resultant.get_center(), RIGHT)

        self.play(Create(resultant), Write(result_label))

        # Show equation
        equation = MathTex(
            r"\begin{bmatrix} 3 \\ 1 \end{bmatrix} + "
            r"\begin{bmatrix} 1 \\ 3 \end{bmatrix} = "
            r"\begin{bmatrix} 4 \\ 4 \end{bmatrix}"
        )
        equation.to_edge(DOWN)
        self.play(Write(equation))
        self.wait(2)
```

## Example 5: Dot Product Projection

```python
from manim import *

class DotProductProjection(Scene):
    """
    Animation showing dot product as projection.

    Mathematical concept: a · b = |a| |b| cos(θ)
    Key insight: Dot product measures how much vectors align
    """

    def construct(self):
        plane = NumberPlane(x_range=[-1, 5], y_range=[-1, 4])
        self.play(Create(plane))

        # Vectors
        a = Arrow(ORIGIN, [4, 0, 0], buff=0, color=RED)
        b = Arrow(ORIGIN, [3, 2, 0], buff=0, color=BLUE)

        a_label = MathTex(r"\vec{a}", color=RED).next_to(a.get_end(), DOWN)
        b_label = MathTex(r"\vec{b}", color=BLUE).next_to(b.get_end(), UR)

        self.play(Create(a), Create(b), Write(a_label), Write(b_label))
        self.wait()

        # Show angle
        angle = Angle(a, b, radius=0.5, color=YELLOW)
        angle_label = MathTex(r"\theta").next_to(angle, RIGHT)
        self.play(Create(angle), Write(angle_label))

        # Projection
        proj_point = [3, 0, 0]  # b projected onto a
        proj_line = DashedLine([3, 2, 0], proj_point, color=GREEN)
        proj_vec = Arrow(ORIGIN, proj_point, buff=0, color=GREEN)

        self.play(Create(proj_line))
        self.play(Create(proj_vec))

        proj_label = MathTex(r"\text{proj}_{\vec{a}} \vec{b}", color=GREEN)
        proj_label.next_to(proj_vec, DOWN)
        self.play(Write(proj_label))

        # Formula
        formula = MathTex(
            r"\vec{a} \cdot \vec{b} = |\vec{a}| \times |\text{proj}_{\vec{a}} \vec{b}|"
        )
        formula.to_edge(UP)
        self.play(Write(formula))

        # Calculate
        calculation = MathTex(
            r"\vec{a} \cdot \vec{b} = (4)(3) + (0)(2) = 12"
        )
        calculation.to_edge(DOWN)
        self.play(Write(calculation))
        self.wait(2)
```
