# Crear un poligono en Blender

> [!CAUTION]
> -Necesario installar Python

En este documento veremos el paso a paso de como crear un poligono en Blender

## Paso 1
Importamos los paquetes que me permiten manipular el codigo y traer funciones matematicas.
```python
import bpy
import math
```
## Paso 2
Definimos el poligono que vamos a crear juntos con sus atributos dentro del parentesis, para luego crear :
-La Malla : Lo que nos permite darle forma al objeto.
-Objeto : Es todo lo que rodea a nuestra figura.
```python
def crear_poligono_2d(nombre, lados, radio):
    # Crear una nueva malla y un nuevo objeto
    malla = bpy.data.meshes.new(nombre)
    objeto = bpy.data.objects.new(nombre, malla)
    
```
## Paso 3
Añadimos esta linea para que nuestro poligono sea visible.
```python
 bpy.context.collection.objects.link(objeto)
```  
## Paso 4
Definimos los vertices y las aristas.
```python
    vertices = []
    aristas = []
```
## Paso 5
Aqui se realizaran las operaciones matematicas complejas como :
-Los angulos de x , y.
-Se generan los vertices en el espacio.
```python
# Cálculo de vertices usando coordenadas polares a cartesianas
    for i in range (lados):
        angulo = 2 * math.pi * i / lados
        x = radio * math.cos(angulo)
        y = radio * math.sin(angulo)
        vertices.append((x, y, 0)) # Z = 0 para mantenerlo en 2D
```
## Paso 6
Esta es la parte mas importante del codigo, ya que: 
-Se repetira las veces necesarias dependiendo el poligono
-Se conectaran los puntos.
-El % es necesario para que el punto n final se conecte con el 0 (el conteo inicia con 0)
-El malla.from.... es lo que convierte el python a Blender
```python
#Definir las conexiones (aristas) entre los vertices
    for i in range (lados):
        aristas.append((i, (i + 1) % lados))
        
    # Cargar los datos en la malla
    malla.from_pydata(vertices,aristas, [])
    malla.update()
```
## Paso 7
Y en la ultima parte hacemos un comando para limpiar el escenario y manipular el poligono a nuestro antojo.
```python
# Limpiar la escena antes de empezar
bpy.ops.object.select_all(action="SELECT")
bpy.ops.object.delete()
