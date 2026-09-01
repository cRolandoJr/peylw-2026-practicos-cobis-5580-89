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
