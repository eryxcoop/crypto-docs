# STARKs sobre BoboVM
# BoboVM
Es una máquina virtual simple para construir un protocolo de STARKs con fines pedagógicos.

# Estructura general
La máquina tiene 2 registros ($pc$, $ap$), una memoria y un conjunto de 4 instrucciones. A medida que se ejecutan una serie de instrucciones el estado de la memoria y de los registros va evolucionando.
## Registros
- $pc$: el program counter indica qué instrucción se está leyendo.
- $ap$: el allocation pointer indica el primer lugar de memoria que todavía no fue escrito.

## Memoria
La memoria tiene 256 direcciones que se van escribiendo de menor a mayor. En cada dirección entra algo de 64 bits.

## Instrucciones
Hay 4 instrucciones: **put**, **add**, **jmp** y **end**.
- **put [literal_0]** : Escribe un literal en la memoria.
- **add [literal_0], [literal_1]** : suma dos cosas y lo guarda en la memoria.
- **jmp [literal_0], [literal_1]** : es un salto condicional.
- **end** : termina el programa.

### Codificación
Cada instrucción ocupa 3 bytes repartidos de la siguiente manera:
![](https://hackmd.io/_uploads/HJ2fWqwv3.png)

- **dirección_op0** : Una dirección de memoria entre 0 y 255.
- **dirección_op1** : Una dirección de memoria entre 0 y 255.
- **literal** : Guarda un entero entre 0 y 255.
- **flags** : indica con un 0 o un 1 en el lugar correspondiente qué tipo de instrucción es, solo uno de los flags puede estar encendido al mismo tiempo. Los 8 bits de flags se reparten así:
![](https://hackmd.io/_uploads/rySqKqDIn.png)

Los enteros pueden ser positivos o negativos, la interpretación es:
$$f(x) = x - 128$$

Es decir que guardar un 128 es guardar un 0, guardar un 127 es un $-1$.

| inst            |   code      |
|-----------------|-------------|
|put 3            | 0x00008340  |
|put -1           | 0x00007f40
|add [-1], [-2]   | 0x7f7e0010
|jmp [-1], 1      | 0x7f008104
|end              | 0x00000001

### Especificación

- **put [literal]** :
    - La memoria en $ap$ pasa a ser **[literal]**
    - Aumenta $pc$ en 1
    - Aumenta $ap$ en 1
    
- **add [direccion_op0], [direccion_op1]**
    - Busca el número $x$ de la memoria en la posición $ap$ + **[direccion_op0]**
    - Busca el número $y$ de la memoria en la posición $ap$ + **[direccion_op1]**
    - La memoria en $ap$ pasa a ser $x$ + $y$
    - Aumenta $pc$ en 1
    - Aumenta $ap$ en 1

- **jmp [direccion_op0], [literal]** :
    - Busca el número $x$ de la memoria en la posición $ap$ + **[direccion_op0]**
    - Si $x$ es cero:
        - Aumeta $pc$ en 1
    - Si $x$ es distinto de 0:
        - $pc$ pasa a ser **[literal]**

- **end** :

## Traza de ejemplo
Llamamos traza a una tabla que guarda los estados sucesivos del programa. El siguiente ejemplo pone un 3 y un -1 en la memoria, después los suma y pone un 2 en la memoria, y luego hace un jump condicional. Como 2 es distinto de cero, vuelve hasta el **put -1** y sigue iterando hasta llegar a cero.
```
put 3
put -1
add [-1], [-2]
jmp [-1], 1
end
```

Si pudieramos ver la evolución del programa sería algo así (técnicamente la columna instrucción no es parte de esta traza, porque se puede deducir de la memoria y del $pc$, la columna clock tampoco porque siempre son los números naturales hasta la cantidad de pasos):
| clock    |   pc   |    instrucción    |  ap  |
| -------- |--------|-------------------|------|
|    1     |    0   |  put 3            |  5   | 
|    2     |    1   |  put -1           |  6   |
|    3     |    2   |  add [-1], [-2]   |  7   |
|    4     |    3   |  jmp [-1], 1      |  8   |
|    5     |    1   |  put -1           |  8   |
|    6     |    2   |  add [-1], [-2]   |  9   |
|    7     |    3   |  jmp [-1], 1      |  10  |
|    8     |    1   |  put -1           |  10  |
|    9     |    2   |  add [-1], [-2]   |  11  |
|    10    |    3   |  jmp [-1], 1      |  12  |

La memoria termina siendo:

| dirección |    valor        |
| --------- | --------------- |
|    0      | put 3           |
|    1      | put -1          |
|    2      | add [-1], [-2]  |
|    3      | jmp [-1], 1     |
|    4      | end             |
|    5      | 3               |
|    6      | -1              |
|    7      | 2               |
|    8      | -1              |
|    9      | 1               |
|    10     | -1              |
|    11     | 0               |

# Traza "aumentada"
Dentro de poco vamos a querer expresar restricciones matemáticas sobre la "traza" del programa (la ejecución del mismo). Para expresar estas restricciones de una manera más fácilmente vamos a agregar columnas auxiliares.

La longitud de la traza es exactamente la cantidad de pasos que hizo la máquina durante su ejecución. Vamos nombrando las columnas por secciones:

## Columnas de operaciones

| pc       |   inst     | is_add   | is_jmp  | is_put | is_end |  lit  |
| -------- | ---------- |----------|---------|--------|--------|-------|
| 0        | 0x00008340 |    0     |    0    |    1   |    0   |   3
| 1        | 0x00007f40 |    0     |    0    |    1   |    0   |  -1
| 2        | 0x7f7e0010 |    1     |    0    |    0   |    0   |   0
| 3        | 0x7f008104 |    0     |    1    |    0   |    0   |   1
| 1        | 0x00007f40 |    0     |    0    |    1   |    0   |  -1 
| 2        | 0x7f7e0010 |    1     |    0    |    0   |    0   |   0
| 3        | 0x7f008104 |    0     |    1    |    0   |    0   |   1
| 1        | 0x00007f40 |    0     |    0    |    1   |    0   |  -1
| 2        | 0x7f7e0010 |    1     |    0    |    0   |    0   |   0
| 3        | 0x7f008104 |    0     |    1    |    0   |    0   |   1
| 4        | 0x00000001 |    0     |    0    |    0   |    1   |   0

## Columnas de memoria

|  ap  | val_op0 |  add_op0 | val_op1 |  add_op1 | write   |   write_addr   |
|------|---------|----------|---------|----------|---------|---------|
|   5  |   0     |      0   |    0    |    0     |    3    |    5    |
|   6  |   0     |      0   |    0    |    0     |   -1    |    6    |
|   7  |   -1    |      7   |    3    |    6     |    2    |    7    |
|   8  |   2     |      8   |    0    |    0     |    0    |    0    |
|   8  |   0     |      0   |    0    |    0     |   -1    |    8    |
|   9  |   -1    |      9   |    2    |    8     |    1    |    9    |
|   10 |   1     |      10  |    0    |    0     |    0    |    0    |
|   10 |   0     |      0   |    0    |    0     |   -1    |   10    |
|   11 |   -1    |      11  |    1    |    10    |    0    |    0    |
|   11 |   0     |      12  |    0    |    0     |    0    |    0    |
|   11 |   0     |      0   |    0    |    0     |    0    |    0    |

### Restricciones de transición

#### Desempaquetado de instrucción

$$\text{inst}=\text{add_op0} * 2^{24} + \text{add_op1}*2^{16}+\text{lit}*2^{8} +\text{is_put}*2^{6}+\text{is_add}*2^{4}+\text{is_jmp}*2^{2}+\text{is_end}$$
#### Indicador de operación
$\text{is_add} (\text{is_add} - 1) = 0$
$\text{is_mul} (\text{is_mul} - 1) = 0$
$\text{is_put} (\text{is_put} - 1) = 0$
$\text{is_end} (\text{is_end} - 1) = 0$
$\text{is_add} + \text{is_mul} + \text{is_put} + \text{is_end} - 1= 0$

#### Evolución de PC
Casos que no son **jmp**:
$(1 - \text{is_jmp})(pc'-(pc+1))=0$

Caso de **jmp** que salta:
$\text{is_jmp}*\text{val_op0}*(pc'-\text{lit})=0$

Casos de **jmp** que no salta:
$\text{is_jmp}*(1-\text{val_op0}*\text{val_op0}^{-1})*(pc'-(pc + 1))=0$

La columna virtual $\text{val_op0}^{-1}$ tiene el inverso de **val_op0** y vale 0 cuando **val_op0=0**.

#### Evolución de AP
$(1-\text{end})(1-\text{jmp})(ap'-(ap+1))=0$
$(1-\text{add})(1-\text{put})(ap'-ap)=0$

#### Suma
$\text{write}=\text{val_op0}+\text{val_op1}$
$\text{addr_op0}=ap+\text{lit1}$

#### Put
$\text{write}=\text{val_op0}$
$\text{write_addr}=ap$

#### Restricciones de memoria
