
## Semana 2

Notas de clase de la semana 2 de LPP.


## Tema 2: Programación funcional 

### Veremos hoy

- 1. El paradigma de Programación Funcional
- 2. Scheme como lenguaje de programación funcional
	- 2.1. Funciones y formas especiales
	- 2.2. Formas especiales en Scheme: `define`, `if`, `cond`
	- 2.3. Forma especial `quote` y símbolos
	- 2.4. Listas
    - **2.5. Recursión**
- **3. Tipos de datos compuestos en Scheme**
	- **3.1 El tipo de dato pareja**
	- **3.2 Las parejas son objetos de primera clase**
	- **3.3  Diagramas caja-y-puntero**
- **4. Listas en Scheme**
    - **4.1 Implementación de listas en Scheme**
    - **4.2 Listas con elementos compuestos**
    - **4.3 Funciones recursivas para construir listas**

---

## 2.5. Recursión

### Confía en la recursión

- En programación funcional las iteraciones se realizan con recursión.
- En una definición recursiva siempre tenemos un caso general y un caso
base. 
- El caso base define el valor que devuelve la función en el caso
elemental en el que no hay que hacer ningún cálculo. 
- El caso general
define una expresión que contiene una llamada a la propia función que
estamos definiendo.

Ejemplo: 

Por ejemplo, podemos definir la función `(suma-hasta x)` que devuelve
la suma de los números hasta el parámetro `x` cuyo valor pasamos en la
invocación de la función.

`(suma-hasta 5)` devolverá `0+1+2+3+4+5 = 15`.

Definición recursiva:

```scheme
(define (suma-hasta x)
   (if (= 0 x)
      0
      (+ (suma-hasta (- x 1)) x)))
```

> Para calcular la suma hasta x, llamamos a la recursión para que
> calcule la suma hasta x-1, obtenemos el resultado (confiamos en que la
> función funciona bien) y a ese resultado le sumamos el propio número
> x.

Un ejemplo concreto, cuando `x` vale 5, `(suma-hasta 5)`:

```txt
(+ (suma-hasta (- 5 1)) 5)
``` 

Evaluación:

```txt
(+ (suma-hasta (- 5 1)) 5) ⇒
(+ (suma-hasta 4) 5) ⇒ (confiamos en la recursión: (suma-hasta 4) = 10)
(+ 10 5) ⇒
15
```

- La llamada recursiva debe trabajar sobre un caso más sencillo que la
llamada general.


### Diseño de la función `(suma-hasta x)`


<img src="imagenes/suma-hasta.png" width="600px"/>

Generalizamos este ejemplo y lo expresamos en Scheme de la siguiente
forma:

```txt
(suma-hasta x)  => (+ (suma-hasta (- x 1)) x)
```

- Nos falta el caso base de la recursión. Debemos preguntarnos **¿cuál
es el caso más sencillo del problema, que podemos calcular sin hacer
ninguna llamada recursiva?**. En este caso podría ser el caso en el
que `x` es 0, en el que devolveríamos 0.

- Podemos ya escribirlo todo en Scheme:

```scheme
(define (suma-hasta x)
   (if (= 0 x)
      0
      (+ (suma-hasta (- x 1)) x)))
```


### Otro ejemplo de función recursiva `(alfabeto-hasta char)`

Como antes, veamos un ejemplo concreto:

```scheme
(alfabeto-hasta #\h) ; ⇒ "abcdefgh"
```

¿Cómo plantear el caso general? Tenemos que hacer una llamada
recursiva que haga casi todo el trabajo y nos devuelva la cadena con
el alfabeto casi calculada. 

<p style="margin-bottom:3cm;"/>

¿Podríamos llamar a la recursión para que nos devuelva el alfabeto
hasta el carácter anterior a la `#\h` (el carácter `\#g`)? 

Si confiamos en la recursión:

```scheme
(alfabeto-hasta #\g) ; ⇒ "abcdefg"
```

Sólo faltaría entonces añadir la `#\h` al final de la cadena:

```txt
"abcdefg" + \#h ⇒ "abcdefgh"
```

¿Cómo lo expresamos en Scheme?

<p style="margin-bottom:3cm;"/>

El caso general:

```scheme
(alfabeto-hasta char) = 
    (string-append (alfabeto-hasta (anterior char)) (string char))
```

Función `(anterior char)`:

```scheme
(define (anterior char)
  (integer->char (- (char->integer char) 1)))
```


Nos faltaría únicamente el caso base.

El caso base sería aquel en el que nos piden lo más sencillo: el
alfabeto hasta el carácter `#\a`, en el que habría que devolver la
cadena "a".

Solución final:

```scheme
(define (alfabeto-hasta char)
  (if (equal? char #\a)
      "a"
      (string-append (alfabeto-hasta (anterior char)) (string char))))
```


### Repaso: Selección de elementos de una lista: `car` y `cdr`

- Primer elemento: función `car`
- Resto de elementos: función `cdr`

Ejemplos:

```scheme
(define lista1 '(1 2 3 4))
(car lista1) ⇒ 1
(cdr lista1) ⇒ {2 3 4}
(define lista2 '((1 2) 3 4))
(car lista2) ⇒ {1 2}
(cdr lista2) ⇒ {3 4}
```



### Recursión y listas: Función `(suma-lista lista-nums)`

Supongamos que queremos definir una función `suma-lista` que reciba
como parámetro una lista de números y devuelva la suma de todos ellos.

Ejemplo:

```txt
(suma-lista '(12 3 5 1 8)) => 29
```

- En este caso podemos pensar que para sumar la lista de
números `(12 3 5 1 8)` podemos obtener un problema más sencillo (una
lista más pequeña) haciendo el `cdr` de la lista de números y llamando
a la recursión con el resultado. 

- La llamada recursiva devolverá la suma de esos números (confiamos en
la recursión) y a ese valor basta con sumarle el primer número de la
lista. Lo podemos representar en el siguiente dibujo:

<img src="imagenes/suma-lista.png" width="600px"/>

- Podemos generalizar este ejemplo y expresarlo en Scheme de la siguiente forma:

```txt
(suma-lista lista) => (+ (car lista) (suma-lista (cdr lista)))
```

- Falta el caso base, que es el caso más sencillo en que podemos
devolver un valor sin llamar a la recursión. En este caso, podría ser
cuando le pesamos a la función una lista sin elementos, en donde hay
que devolver 0.

Con todo junto, quedaría la recursión como sigue

```scheme
(define (suma-lista lista)
   (if (null? lista)
       0
	   (+ (car lista) (suma-lista (cdr lista)))))
```
   
### Función recursiva `veces`

Como último ejemplo vamos a definir la función `(veces lista id)` que
cuenta el número de veces que aparece un identificador en una lista.

- ¿Cómo planteamos el caso general? Llamaremos a la recursión con el
resto de la lista. Esta llamada nos devolverá el número de veces que
aparece el identificador en este resto de la lista. Y después sumamos
al valor devuelto 1 si el primer elemento de la lista coincide con el
identificador.

En Scheme:

```txt
(veces lista identificador) => (if (equal? (car lista) identificador)
                                   (+ 1 (veces (cdr lista) identificador))
                                   (veces (cdr lista) identificador))
```

Como caso base, si la lista es vacía devolvemos 0.

La versión completa:

```scheme
(define (veces lista id)
  (cond
    ((null? lista) 0)
    ((equal? (car lista) id) (+ 1 (veces (cdr lista) id)))
    (else (veces (cdr lista) id))))

(veces '(a b a a b b) 'a) 
⇒ 3 
```



## 3. Tipos de datos compuestos en Scheme

### El tipo de dato pareja

- Ya lo hemos visto en el seminario
- `cons` para construir parejas:

```scheme
(cons 1 2) ; ⇒ {1 . 2}
(define c (cons 1 2))
```



### Funciones de acceso `car` y `cdr`

- `car` y `cdr` devuelven la parte izquierda y derecha:

```scheme
(define c (cons 1 2))
(car c) ; ⇒ 1
(cdr c) ; ⇒ 2
```


### Definición declarativa


```scheme
(car (cons x y)) = x
(cdr (cons x y)) = y
```


### Función pair?


```scheme
(pair? 3) ; ⇒ #f
(pair? (cons 3 4)) ; ⇒ #t
```


### Las parejas pueden contener cualquier tipo de dato

```scheme
(define c (cons 'hola #f))
(car c) ; ⇒ 'hola
(cdr c) ; ⇒ #f
```

### Las parejas son objetos inmutables

- En programación funcional una vez creada una parea no está permitido modificar (mutar) su contenido.
- En Scheme hay funciones para mutar parejas, pero no las veremos hasta ver el paradigma de programación imperativa


### Las parejas son objetos de primera clase

En un lenguaje de programación un elemento es de primera clase cuando puede:

* Asignarse a variables
* Pasarse como argumento
* Devolverse por una función
* Guardarse en una estructura de datos mayor

Las parejas son objetos de primera clase.


### Asignación a una variable (1)

Una pareja puede asignarse a una variable:

```scheme
(define p1 (cons 1 2))
(define p2 (cons #f "hola"))
```

### Paso como argumento y devolverse como resultado de una función (2 y 3)

```scheme
(define (suma-parejas p1 p2)
    (cons (+ (car p1) (car p2))
          (+ (cdr p1) (cdr p2))))
```

Lo probamos ...

<p style="margin-bottom:2cm"/>


### Ejemplo de función que recibe distintos tipos de datos

- Scheme es débilmente tipeado
- Podemos pasar cualquier tipo de dato en los parámetros de las funciones, por ejemplo a la siguiente función `suma`

```scheme
(define (suma x y)
  (cond 
    ((and (number? x) (number? y)) (+ x y))
    ((and (pair? x) (pair? y)) (suma-parejas x y))
    ((and (string? x) (string? y)) (string-append x y))
    (else 'error)))
```

Lo probamos ...


<p style="margin-bottom:3cm;"/>



### Formar parte de otras parejas (4)

> El resultado de un `cons` puede usarse como parámetro de nuevas llamadas a `cons`.


```scheme
(define p1 (cons 1 2))
(define p2 (cons 3 4))
(define p (cons p1 p2))
```

### Diagramas caja-y-puntero

```scheme
(define p (cons (cons 1 2)
                (cons 3 4)))
```

<img src="./imagenes/pareja-pareja.png" width="300px"/>

Diagramas *caja-y-puntero* (*box-and-pointer* en inglés):

<img src="./imagenes/pareja-pareja2.png" width="250px"/>


### Ejemplos de diagramas caja-y-puntero

- Es conveniente indentar correctamente los `cons`:

```scheme
(define p (cons (cons 1
                      (cons 3 4))
                2))
```

- Es importante recordar que las expresiones se evalúan *de dentro a afuera*.
- ¿Qué estructura se construye con la sentencia anterior? Dibuja el diagrama *box-and-pointer*.

<p style="margin-bottom:3cm;"/>

- ¿Cuál sería el diagrama resultante de la siguiente expresión?


```scheme
(define p2 (cons 5 (cons p 6)))
```

<p style="margin-bottom:4cm;"/>

- ¿Cómo sería la expresión formada por `car` y `cdr`s que devolviera 3 a partir de la variable `p2`?

<p style="margin-bottom:4cm;"/>


### Funciones c????r

- Al trabajar con estructuras de parejas anidades es muy habitual realizar llamadas del tipo:

```scheme
(cdr (cdr (car p)))
```

- Es equivalente a la función `cadar` de Scheme:

```scheme
(cddar p)
```

- El nombre de la función se obtiene concatenando a la letra "c", las letras "a" o "d" según hagamos un car o un cdr y terminando con la letra "r".

- Hay definidas 2^4 funciones de este tipo: `caaaar`, `caaadr`, …, `cddddr`.


## 4 Listas en Scheme 

- Hemos comprobado que las listas y las parejas tienen las mismas
  funciones: `car`, `cdr` y `cons`. ¿Por qué? ¿Qué relación hay entre
  las parejas y las listas?


### Repaso: función cons con listas

- La función `cons` crea una lista nueva resultante de añadir un elemento
al comienzo de la lista:

```scheme
(cons 1 '(1 2 3 4)) ⇒ {1 1 2 3 4}
(cons 'hola '(como estás)) ⇒ {hola como estás}
(cons '(1 2) '(1 2 3 4))  ⇒ {{1 2} 1 2 3 4}
```


### Relación entre listas y parejas en Lisp y Scheme

- Hagamos algunas pruebas.

¿Una pareja es una lista?
Lo probamos ...

```scheme
(define p1 (cons 1 2))
(pair? p1) 
(list? p1) 
```

<p style="margin-bottom:2cm;"/>

¿Una lista vacía es una lista? ¿Es una pareja?
Lo probamos ...

```scheme
(list? '())
(pair? '())
```

<p style="margin-bottom:2cm;"/>

¿Una lista es una pareja?
Lo probamos ...

```scheme
(define lista '(1 2 3))
(list? lista)
(pair? lista)
```

<p style="margin-bottom:2cm;"/>

¿Una pareja con una lista vacía como parte izquierda es una lista?
Lo probamos ...

```scheme
(define p1 (cons 1 '()))
(pair? p1)
(list? p1)
```
<p style="margin-bottom:2cm;"/>

Con estos ejemplos ya tenemos pistas para deducir la relación entre listas y parejas en Scheme (y Lisp). Vamos a explicarlo.


### Definición de listas con parejas

Una lista es (definición recursiva):

* Una **pareja** que contiene:
    * *En su parte izquierda*: el primer elemento de la lista
    * *En su parte derecha*: el resto de la lista (volvemos a aplicar la definición)
* Un **símbolo especial** `'()` que denota la lista vacía.


### Ejemplo más sencillo: `{1}`

```scheme
(cons 1 '())
```
	
La pareja cumple las condiciones anteriores: 

* La parte izquierda de la pareja es el primer elemento de la lista (el número 1)
* La parte derecha es el resto de la lista (la lista vacía)

<img src="./imagenes/pareja-lista.png" width="150px"/>


- Es al mismo tiempo una pareja y una lista:

```scheme
(define l (cons 1 '()))
(pair? l)
(list? l)
```


### Otro ejemplo: `{1 2 3 4}`

```scheme
(cons 1
      (cons 2
            (cons 3
                  (cons 4 
                        '()))))
```

- La primera pareja cumple las condiciones de ser una lista:

* Su primer elemento es el 1
* Su parte derecha es la lista '(2 3 4)

<img src="./imagenes/lista.png" width="400px"/>


### Lista vacía

La lista vacía es una lista:

```scheme
(list? '())
```

No es un símbolo ni una pareja:

```scheme
(symbol? '())
(pair? '())
```

Función `null?`:

```scheme
(null? '())
```	

### Listas con elementos compuestos

- *Lista de asociación*, listas cuyos elementos son parejas (*clave*, *valor*):

```scheme
(list (cons 'a 1)
      (cons 'b 2)
      (cons 'c 3))
```

¿Cuál sería el diagrama *box and pointer* de la estructura anterior?

<p style="margin-bottom:4cm;"/>

- Expresión equivalente utilizando *conses* es:

```scheme
(cons (cons 'a 1)
      (cons (cons 'b 2)
            (cons (cons 'c 3)
                  '())))
```


### Listas de listas

```scheme
(define lista (list 1 (list 1 2 3) 3))
```

Definición con `quote`:

```scheme
(define lista '(1 (1 2 3) 3))
```

¿Cuál sería el diagrama *box and pointer* de la estructura anterior?

<p style="margin-bottom:4cm;"/>


### Ejemplo inverso  ###

Un último ejemplo. Supongamos el siguiente diagrama caja y puntero:

<img src="imagenes/lista-complicada.png" width="500px"/>


¿Cuál sería la expresión en Scheme (usando llamadas a `list` y `cons`) que lo
construye?

<p style="margin-bottom:4cm;"/>

Solución: 

```scheme
(define p1 (list (cons (cons 1 2)
                       (cons 3 4))
                 (list 5 6 (cons 7
                                 (cons 8 9)))
                 10))
```


### Distintos niveles de abstracción

- Una vez que conocemos la implementación de listas con parejas, no va a a ser necesario casi nunca *bajar* a este nivel de implementación
- Podemos volver a la *abstracción* inicial en la que las funciones `car` y `cdr` trabajan sobre listas:
    - `(car lista)`: devuelve el primer elemento de la lista
    - `(cdr lista)`: devuelve el resto de la lista
    - `(list-ref lista n)`: devuelve la posición `n` de la lista
    - `(cons dato lista)`: devuelve una nueva lista con `dato` en su primera posición y `lista` como su resto

<p style="margin-bottom:4cm;"/>


