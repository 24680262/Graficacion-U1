# Proyecto Integrador.

> [!CAUTION]
> -Necesario installar Python

En este documento de GitHub veremos como realizamos el proyecto integrador de la unidad 1, el cual consiste en un escenario procedural, agregando la animacion de la cámara a través del camino.

## Paso 1
Importamos los paquetes que ocuparemos en este programa.
```python
import bpy
import random
import math
```
## Paso 2
Creamos un material básico con un color específico.
```python
def crear_material(nombre, color_rgb):
    mat = bpy.data.materials.new(name=nombre)
    mat.diffuse_color = (*color_rgb, 1.0) # RGBA
    return mat
```
## Paso 3
Limpiar la escena previa
```python
def generar_escenario():
    bpy.ops.object.select_all(action='SELECT')
    bpy.ops.object.delete()
```
Definimos los materiales (Basados en modelos de color RGB)
```python
    mat_pared_a = crear_material("ParedOscura", (.1, .1, .1))
    mat_pared_b = crear_material("ParedDetalle", (0.8, 0.2, 0.0)) # Un naranja rojizo
```
Definimos los parámetros del escenario.
```python
    largo_pasillo = 20
    ancho_pasillo = 4
    amplitud = 5
    frecuencia = 0.3
```
## Paso 4
Aqui trabajamos el largo del pasillo ademas de como se iran haciendo las curvas.
```python
 for i in range(largo_pasillo):
        desplazamiento_curva = math.sin(i * frecuencia) * amplitud

        y_pos = i * 2
        x_izq = -ancho_pasillo + desplazamiento_curva
        x_der = ancho_pasillo + desplazamiento_curva
        x_centro = desplazamiento_curva
```
## Paso 5
Aqui se trabaja la parte de la pared izquierda, el como va a estar distruibuida la pared y los colores de esta misma.
```python
bpy.ops.mesh.primitive_cube_add(location=(x_izq, y_pos, 1))
        pared_izq = bpy.context.active_object

        # Aplicar material de forma alternada (Lógica de programación)
        if i % 2 == 0:
            pared_izq.data.materials.append(mat_pared_a)
        else:
            pared_izq.data.materials.append(mat_pared_b)
            # Escalamiento para darle variedad visual
            pared_izq.scale.z = 1.5
```
## Paso 6
Hacemos lo mismo con la pared derecha, pero solo manejaremos un color, por lo cual no usamos un ciclo if.
```python
bpy.ops.mesh.primitive_cube_add(location=(x_der, y_pos, 1))
        pared_der = bpy.context.active_object
        pared_der.data.materials.append(mat_pared_a)
```
## Paso 7
Ahora bien, nos toca trabajar el suelo, el como va a estar distruibuido y cual sera su medida principal
```python
bpy.ops.mesh.primitive_plane_add(location=(x_centro, y_pos, 0))
        suelo_segmento = bpy.context.active_object
        suelo_segmento.scale.x = ancho_pasillo 
        suelo_segmento.scale.y = 1.0
        suelo_segmento.data.materials.append(mat_pared_a)
```
## Paso 8
Creamos una camara para que haga el recorrido que planeamos.
```python
 bpy.ops.object.camera_add()
    camara = bpy.context.active_object
    bpy.context.scene.camera = camara
    camara.rotation_euler[0] = math.radians(80)
```
## Paso 9
Esta es la logica de nuestro recorrido, fundamental para ver como va a fluir todo.
```python
frames_totales = 250
    bpy.context.scene.frame_end = frames_totales
```
## Paso 10
Localizamos nuestra camara en el recorrido y el ritmo del recorrido
```python
for f in range(frames_totales + 1):
        i_animado = (f / frames_totales) * (largo_pasillo - 5)     
        y_cam = i_animado * 2
        x_cam = math.sin(i_animado * frecuencia) * amplitud
        z_cam = 1.5 # Altura de los ojos
        camara.location = (x_cam, y_cam, z_cam)
        camara.keyframe_insert(data_path="location", frame=f)
```
## Paso 11
Damos luz a nuestro escenario y lo creamos
```python
bpy.ops.object.light_add(type='SUN', location=(0, 0, 10))
generar_escenario()
```
