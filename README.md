# Reto-6
Add the required exceptions in the Reto 1 code assigments.
In the package Shape identify at least cases where exceptions are needed (maybe when validate input data, or math procedures) explain them clearly using comments and add them to the code.
P.D. Happy codding!

**Description of the shape exercise**

In this exercise, a Python package called shape_package was implemented, containing a base class Shape and derived classes such as Point, Rectangle, and Square. The goal was to apply the principles of inheritance, encapsulation, and modularization by separating the code into modules within a package to improve its organization and reusability. Each class inherits or extends the properties and methods of Shape, representing different geometric shapes. Additionally, exceptions were added to handle common errors, such as negative values in dimensions or invalid data types, which strengthens the robustness of the code and demonstrates good object-oriented programming practices.
```python
import math
from typing import List

class Point:
    """Represents a point on the (x, y) plane."""

    def __init__(self, x: float = 0.0, y: float = 0.0):
        # Type validation: we accept int or float
        if not (isinstance(x, (int, float)) and isinstance(y, (int, float))):
            raise TypeError("The x and y coordinates must be numbers (int or float).")
        self.__x = float(x)
        self.__y = float(y)

    def get_x(self) -> float:
        return self.__x

    def set_x(self, x: float):
        if not isinstance(x, (int, float)):
            raise TypeError(" x must be numeric.")
        self.__x = float(x)

    def get_y(self) -> float:
        return self.__y

    def set_y(self, y: float):
        if not isinstance(y, (int, float)):
            raise TypeError("y must be numeric.")
        self.__y = float(y)

    def compute_distance(self, other: "Point") -> float:
        """ Calculate the Euclidean distance between two points."""
        if not isinstance(other, Point):
            raise TypeError("compute_distance espera otro Point como argumento.")
        dx = self.__x - other.get_x()
        dy = self.__y - other.get_y()
        return math.hypot(dx, dy)

    def __repr__(self):
        return f"Point({self.__x}, {self.__y})"

class Line:
    """ Segment defined by two points (start, end)."""

    def __init__(self, start_point: Point, end_point: Point):
        if not isinstance(start_point, Point) or not isinstance(end_point, Point):
            raise TypeError("start_point and end_point must be instances of Point.")
        self.__start_point = start_point
        self.__end_point = end_point
        self.__length = self.compute_length()

    def get_start_point(self) -> Point:
        return self.__start_point

    def set_start_point(self, p: Point):
        if not isinstance(p, Point):
            raise TypeError("start_point must be a Point.")
        self.__start_point = p
        self.__length = self.compute_length()

    def get_end_point(self) -> Point:
        return self.__end_point

    def set_end_point(self, p: Point):
        if not isinstance(p, Point):
            raise TypeError("end_point must be a Point.")
        self.__end_point = p
        self.__length = self.compute_length()

    def get_length(self) -> float:
        return self.__length

    def compute_length(self) -> float:
        #compute_distance already validates types
        length = self.__start_point.compute_distance(self.__end_point)
        return length

    def __repr__(self):
        return f"Line({self.__start_point}, {self.__end_point}) len={self.__length:.3f}"

class Shape:
    """ Base class for simple polygons. Subclasses should override compute_area/perimeter."""

    def __init__(self, vertices: List[Point]):
        if not isinstance(vertices, list):
            raise TypeError("vertices must be a list of Points.")
        if any(not isinstance(v, Point) for v in vertices):
            raise TypeError("All vertex elements must be instances of Point.")
        if len(vertices) < 3:
            raise ValueError("A polygon must have at least 3 vertices.")
        self._vertices = vertices[:]  # copy the list 
        self._edges = self._create_edges()
        self._is_regular = False

    def _create_edges(self) -> List[Line]:
        edges: List[Line] = []
        n = len(self._vertices)
        for i in range(n):
            start = self._vertices[i]
            end = self._vertices[(i + 1) % n]
            edges.append(Line(start, end))
        return edges

    def get_vertices(self) -> List[Point]:
        return self._vertices

    def set_vertices(self, vertices: List[Point]):
        # Validation
        if not isinstance(vertices, list) or any(not isinstance(v, Point) for v in vertices):
            raise TypeError("3 vertices must be a list of Points.")
        if len(vertices) < 3:
            raise ValueError(" A polygon must have at least 3 vertices.")
        self._vertices = vertices[:]
        self._edges = self._create_edges()

    def get_edges(self) -> List[Line]:
        return self._edges

    #  Methods to be overridden in subclasses
    def compute_area(self) -> float:
        raise NotImplementedError("compute_area debe redefinirse en la subclase.")

    def compute_perimeter(self) -> float:
        raise NotImplementedError("compute_perimeter debe redefinirse en la subclase.")

    def __repr__(self):
        return f"{self.__class__.__name__} con {len(self._vertices)} vértices"

# Rectangle and Square with validations
class Rectangle(Shape):
    """ Rectangle defined by the bottom-left point (bottom_left), width, and height."""

    def __init__(self, bottom_left: Point, width: float, height: float):
        # Type and value validations
        if not isinstance(bottom_left, Point):
            raise TypeError(" bottom_left must be a Point.")
        if not isinstance(width, (int, float)) or not isinstance(height, (int, float)):
            raise TypeError("Width and height must be numeric.")
        if width <= 0 or height <= 0:
            raise ValueError("Width and height must be greater than 0.")
        #Create vertices in a clockwise direction
        vertices = [
            bottom_left,
            Point(bottom_left.get_x() + width, bottom_left.get_y()),
            Point(bottom_left.get_x() + width, bottom_left.get_y() + height),
            Point(bottom_left.get_x(), bottom_left.get_y() + height)
        ]
        super().__init__(vertices)
        self._width = float(width)
        self._height = float(height)

    def compute_area(self) -> float:
        return self._width * self._height

    def compute_perimeter(self) -> float:
        return 2.0 * (self._width + self._height)

    def get_width(self) -> float:
        return self._width

    def get_height(self) -> float:
        return self._height


class Square(Rectangle):
    """Square: rectangle with equal sides."""

    def __init__(self, bottom_left: Point, side: float):
        if not isinstance(side, (int, float)):
            raise TypeError("side must be numeric.")
        if side <= 0:
            raise ValueError("side must be numeric.")
        super().__init__(bottom_left, side, side)
        self._is_regular = True

    #compute_area and compute_perimeter inherited from Rectangle are correct
```
**Description of exercise 1 of challenge 1**
This program prompts the user for two numbers and performs the basic operations of addition, subtraction, multiplication, and division, displaying the results on screen. ValueError exceptions were added to handle non-numeric inputs, and ZeroDivisionError to prevent errors when dividing by zero.
```python
def introducir_numeros() -> tuple:
    """Pide dos números enteros y los devuelve.
    Lanza ValueError si la entrada no es convertible a int.
    """
    try:
        n1 = int(input("Introduce el numero 1: "))
        n2 = int(input("Introduce el numero 2: "))
    except ValueError:
        raise ValueError("Entrada inválida: por favor introduce números enteros válidos.")
    return n1, n2

def sumar(a, b):
    print(f"La suma de {a} + {b} = {a + b}")

def restar(a, b):
    print(f"La resta de {a} - {b} = {a - b}")

def multiplicar(a, b):
    print(f"La multiplicacion de {a} * {b} = {a * b}")

def dividir(a, b):
    try:
        resultado = a / b
    except ZeroDivisionError:
        print("Error: división por cero. No es posible dividir entre 0.")
        return
    print(f"La division de {a} / {b} = {resultado}")

if __name__ == "__main__":
    try:
        n1, n2 = introducir_numeros()
    except ValueError as e:
        print(e)
    else:
        sumar(n1, n2)
        restar(n1, n2)
        multiplicar(n1, n2)
        dividir(n1, n2)
````
**Description of exercise 2 of challenge 1**

In this exercise, a function was implemented that determines whether a sentence is a palindrome, ignoring uppercase letters, accents, and spaces. The unicodedata module was used to normalize the characters and facilitate comparison. Exceptions were added to handle cases where the user enters an empty string or an invalid data type.
```python
# reto1_02.py
import unicodedata

def normalizar(texto: str) -> str:
    if not isinstance(texto, str):
        raise TypeError("normalizar espera una cadena (str).")
    texto = texto.strip()
    if texto == "":
        raise ValueError("La cadena no puede estar vacía.")
    texto = texto.lower()
    texto = ''.join(
        c for c in unicodedata.normalize('NFD', texto)
        if unicodedata.category(c) != 'Mn'
    )
    texto = texto.replace(" ", "")
    return texto

def es_palindromo(frase: str) -> bool:
    frase = normalizar(frase)
    izquierda = 0
    derecha = len(frase) - 1
    while izquierda < derecha:
        if frase[izquierda] != frase[derecha]:
            return False
        izquierda += 1
        derecha -= 1
    return True

if __name__ == "__main__":
    pruebas = ["oso", "Radar", "Anilina", "Anita lava la tina", "Dábale arroz a la zorra el abad", "Python"]
    for p in pruebas:
        try:
            print(p, "=>", es_palindromo(p))
        except (TypeError, ValueError) as e:
            print("Error en prueba:", p, "-", e)
```
**Description of exercise 3 of challenge 1**
The goal of this program was to create a function that receives a list of integers and returns only the prime numbers. Controls were implemented using TypeError and ValueError exceptions to ensure that all elements in the list are valid integers.
```python
# reto1_03.py
import math
from typing import List

def filtrar_primos(lista) -> List[int]:
    if not hasattr(lista, "__iter__"):
        raise TypeError("filtrar_primos espera un iterable de enteros.")
    primos = []
    for numero in lista:
        if not isinstance(numero, int):
            raise TypeError("Todos los elementos deben ser enteros.")
        if numero >= 2:
            es_primo = True
            limite = int(math.isqrt(numero))  # más seguro y rápido
            for i in range(2, limite + 1):
                if numero % i == 0:
                    es_primo = False
                    break
            if es_primo:
                primos.append(numero)
    return primos

if __name__ == "__main__":
    numeros = [1, 2, 3, 4, 5, 10, 13, 15, 17, 20, 23]
    try:
        print(filtrar_primos(numeros))
    except TypeError as e:
        print("Error:", e)
```

