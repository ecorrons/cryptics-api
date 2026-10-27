# API de Cripticos

Catálogo de cripticos en español de solo lectura para el TFI de TTPS opcion Ruby, publicado como JSON estático.

## Endpoints

| Ruta | Contenido |
| --- | --- |
| `/v1/cryptics.json` | El catálogo completo. |
| `/v1/version.json` | Solo la versión vigente del catálogo. |
| `/v1/cryptics_with_errors.json` | Un catálogo de prueba con errores a propósito. |

Hay tres formas de usar la API. Las rutas son las mismas en las tres: solo cambia la URL base, que se configura desde el backend de administración. En todos los casos la sincronización se hace por HTTP; no vale leer el archivo directamente del disco.

### Opción 1: la API de la cátedra

```
https://ecorrons.github.io/cryptics-api/v1/cryptics.json
```

Es la opción más simple. El catálogo lo mantiene la cátedra y puede sumar cripticos durante la cursada.

### Opción 2: una copia propia en GitHub

Sirve para agregar cripticos propios.

1. Hacer un fork de este repositorio desde el botón **Fork**.
2. En el fork, ir a la pestaña **Actions** y habilitar los workflows (en los forks vienen desactivados).
3. En **Settings → Pages**, elegir **GitHub Actions** como fuente.
4. Hacer cualquier commit en `main`, o correr a mano el workflow **Publicar** desde **Actions**.

La API queda en `https://<usuario>.github.io/cryptics-api/v1/cryptics.json`. Para traer los cripticos nuevos de la cátedra, usar **Sync fork** en la página del fork.

### Opción 3: un servidor local

Sirve para desarrollar sin conexión. Desde la carpeta de una copia del repositorio (el de la cátedra o un fork propio):

```sh
gem install webrick
ruby -run -e httpd . -p 4567
```

La API queda en `http://localhost:4567/v1/cryptics.json`. Conviene no usar el puerto 3000, que es el que usa por defecto Rails. Los cambios en los archivos se ven al instante, pero nada verifica que el JSON sea válido antes de servirlo.

## Cómo agregar cripticos

1. Editar `v1/cryptics.json`. Cada críptico nuevo lleva un `id` nuevo y NUNCA se reutiliza uno viejo. En una copia propia, usar un prefijo propio (por ejemplo, `g3-001`) para no chocar con los ids de la cátedra.
2. Cambiar el campo `version` en `v1/cryptics.json` y poner el mismo valor en `v1/version.json`. Actualizar `cantidad`.
3. Hacer commit y push a `main`. El workflow verifica que los JSON sean válidos y que las dos versiones coincidan, y recién entonces publica. Si algo falla, la versión anterior sigue publicada.

El workflow solo verifica que el JSON sea válido, no que los cripticos estén bien armados. Un críptico con errores se publica igual, y es el sistema de cada grupo el que debe detectarlo y rechazarlo al sincronizar, según las reglas del enunciado.

Para sacar un críptico, se lo borra del catálogo. Su `id` no se vuelve a usar nunca, y es el sistema de cada grupo el que decide qué hacer con los cripticos que ya no vienen en el catálogo.
