# Reflexión aplicada — Laboratorio 1

## 1. Token Único de Verificación

**`cobis-5580-89`**

Formado según el criterio pedido: apellido en minúsculas (`cobis`), últimos 4 dígitos
del DNI (`5580`) y los dos últimos dígitos del legajo CURZA-9389 (`89`).

El repositorio remoto es, entonces, `peylw-2026-practicos-cobis-5580-89`.

## 2. Salida de `git status` justo antes del primer commit

```
En la rama main

No hay commits todavía

Archivos sin seguimiento:
  (usa "git add <archivo>..." para incluirlo a lo que será confirmado)
	index.html

no hay nada agregado al commit pero hay archivos sin seguimiento presentes (usa "git add" para hacerles seguimiento)
```

Tres cosas que dice esa salida y conviene leer con atención:

- **"No hay commits todavía"**: el repositorio existe pero está vacío. El commit que
  viene va a ser el *commit raíz*, el único que no tiene padre.
- **"Archivos sin seguimiento"**: `index.html` existe en el disco, pero Git todavía no
  lo controla. No está siguiendo sus cambios.
- **"no hay nada agregado al commit"**: el área de preparación está vacía. Si commiteara
  en este momento, el commit no incluiría nada.

## 3. Diferencia entre el área de preparación y el directorio de trabajo

En realidad son tres zonas, no dos, y entender la tercera es lo que hace que la
diferencia entre las dos primeras tenga sentido.

**El directorio de trabajo (working directory)** son mis archivos tal como están en el
disco en este momento. Es donde escribo, edito y borro. Git no controla nada acá: se
limita a comparar lo que hay con lo que tiene registrado, y por eso puede decirme qué
cambió.

**El área de preparación (staging area, también llamada *index*)** es la foto propuesta
del próximo commit. Cuando ejecuto `git add`, Git **copia el contenido del archivo en
ese instante** al área de preparación. No guarda una referencia al archivo ni una lista
de nombres: guarda el contenido.

De ahí sale la prueba de que son dos cosas distintas. Si agrego un archivo con
`git add`, después lo edito y recién entonces hago `git commit` sin volver a agregarlo,
lo que queda commiteado es **la versión vieja**, la que estaba cuando hice el `add`. El
directorio de trabajo ya tiene la nueva; el área de preparación conserva la anterior.

**El repositorio** es la tercera zona: los commits ya sellados, inmutables, identificados
por su hash.

El flujo completo es entonces:

```
directorio de trabajo  --git add-->  área de preparación  --git commit-->  repositorio
```

### Para qué sirve que estén separadas

La razón de ser del área de preparación es **desacoplar qué cambié de qué voy a contar
como una unidad de cambio**. Si toqué cinco archivos resolviendo dos problemas
distintos, puedo agregar los tres del primer problema, commitear, y después los otros
dos con su propio mensaje. El historial queda contando dos cosas coherentes en vez de
una mezcla. Con `git add -p` se puede llegar a elegir incluso partes de un mismo
archivo.

No todos los sistemas de control de versiones tienen esta zona intermedia: en Mercurial,
por defecto, un commit toma todo lo modificado. Git eligió agregar el paso extra
justamente para poder componer el commit antes de sellarlo.

Esa separación se ve directamente en la salida de `git status --short`, donde la primera
columna indica en qué zona está cada archivo: `A` significa que está en el área de
preparación y entrará al próximo commit, mientras que `??` significa que está solamente
en el directorio de trabajo y Git lo ignoraría si commiteara ahora.

---

# Reflexión aplicada — Laboratorio 2

## 1. Imagen y texto alternativo

La imagen guardada en `img/` se llama **`rolando-cobis.png`**.

El valor del atributo `alt` asignado en `acercade.html` es:

```
Retrato de Rolando Cobis, estudiante de la Tecnicatura Universitaria en Desarrollo Web del CURZA
```

El `alt` describe **qué se ve y en qué contexto**, no repite el nombre del archivo. Es lo que
lee un lector de pantalla y lo que se muestra si la imagen no carga, así que tiene que
transmitir la misma información que aporta la foto. La descripción visible va aparte, en el
`<figcaption>`: el `alt` **sustituye** a la imagen, el `figcaption` la **acompaña**.

## 2. Por qué etiquetas semánticas y no `<div>`

Un `<div>` no significa nada: es una caja genérica que sirve para agrupar y darle estilos. Un
`<nav>` o un `<main>`, en cambio, **le dicen a quien lee el documento qué papel cumple ese
bloque**, y eso tiene tres consecuencias concretas:

- **Accesibilidad.** Un lector de pantalla puede ofrecer "saltar al contenido principal"
  porque existe un `<main>`, o listar las regiones de navegación porque existen los `<nav>`.
  Si todo son `<div>` indistinguibles, esa navegación por regiones desaparece y el usuario
  tiene que recorrer la página entera desde arriba.
- **Posicionamiento en buscadores.** El buscador infiere la estructura del documento para
  decidir qué es contenido y qué es andamiaje. Un menú dentro de un `<nav>` se interpreta como
  navegación; el mismo menú dentro de un `<div>` compite como si fuera contenido.
- **Mantenimiento.** `<footer>` se entiende sin abrir la hoja de estilos.
  `<div class="fot">` obliga a ir a buscar qué era.

El criterio de fondo: la etiqueta describe **qué es** el contenido y el CSS decide **cómo se
ve**. Cuando se usa `<div>` para todo, la única fuente de significado pasa a ser el nombre de
la clase, que es una convención privada del autor y que nadie más —ni el navegador, ni el
lector de pantalla, ni el buscador— puede interpretar.

## 3. Cómo verifiqué las rutas de navegación

En **local**, abriendo `index.html` en el navegador y recorriendo el ciclo completo en los dos
sentidos: Inicio → Acerca de → Inicio. Que las dos páginas usen rutas **relativas**
(`index.html`, `acercade.html`, `img/rolando-cobis.png`) y no absolutas es lo que permite que
ese mismo recorrido funcione sin cambios en los dos entornos: una ruta absoluta que empezara
con `/` apuntaría a la raíz del dominio, y en GitHub Pages el sitio no vive en la raíz sino
bajo `/peylw-2026-practicos-cobis-5580-89/`.

Tras el despliegue en **GitHub Pages** la verificación no se hizo a ojo, sino pidiendo el
código de estado HTTP de cada recurso, que es el dato que dice si el servidor lo encontró:

```
curl -o /dev/null -w "%{http_code}" <URL>/
curl -o /dev/null -w "%{http_code}" <URL>/acercade.html
curl -o /dev/null -w "%{http_code}" <URL>/styles.css
curl -o /dev/null -w "%{http_code}" <URL>/img/rolando-cobis.png
```

Los cuatro responden **200**. La diferencia con mirarlo en el navegador es que el `200` es
evidencia del servidor: una hoja de estilos que no cargó o una imagen rota pueden pasar
desapercibidas a simple vista, pero devuelven **404** igual.

---

# Reflexión aplicada — Laboratorio 3

## 1. Campo del código postal

```html
<label for="cp">Código postal:</label>
<input type="text" id="cp" name="cp" pattern="^[A-Z]\d{4}[A-Z]{3}$"
       title="Formato: una letra mayúscula, cuatro dígitos y tres letras mayúsculas (ej: R8500AAF)">
```

## 2. La etiqueta `<label>` y el atributo `for`

El `<label>` es el texto que dice qué va en cada campo. Además, si hacés clic en el texto se activa el
campo, y los lectores de pantalla lo leen en voz alta. Para unirlos, el `for` del label tiene que ser
igual al `id` del input:

```html
<label for="email">Correo electrónico:</label>
<input type="email" id="email" name="email" required>
```

## 3. Radios con distinto `name` y con el mismo `name`

Si los radios tienen el mismo `name`, forman un grupo y solo se puede elegir uno: al marcar otro, el
anterior se desmarca. Si cada uno tiene un `name` distinto, no se conocen entre sí y se pueden marcar
todos a la vez, que no es lo que se busca.
