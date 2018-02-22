# Consejos personales para el desarrollo de sistemas #
[Cristian Daniel Garay Sosa](https://about.me/cdanielpy "https://about.me/cdanielpy")

## Consejo número 1: Que el código sea el documento. ##

![](https://i0.wp.com/www.clker.com/cliparts/v/O/1/t/u/D/edit-find-replace-md.png)

Durante todo el tiempo (al menos el 98% de él) que llevo trabajando como programador casi nunca hubo tiempo para documentar los trabajos realizados. El ritmo acelerado de los requerimientos del negocio, la cantidad de trabajos pendientes, la cantidad de errores a parchar y el tiempo ocupado en localizarlos(...), la falta de estandarización de las metodologías de trabajo y un montón de otras razones no dejan espacio para escribir documentos.


De cualqueir forma, leer un montón de páginas para entender cómo funciona un programa no es lo mío, salvo que sea desde el punto de vista de los usuarios.  

Para paliar esta situación y pensando en otros compañeros adopté la costumbre de comentar prácticamente 8 de cada 10 líneas de código que escribo. Desde algún punto de vista puede ser exagerado comentar un `if` o la declaración misma de variables.

A pesar de que el comentario en el código al compilarlo se pierde, lo que queda son las documentaciones de los métodos. Valerse de las utilerías tipo [Javadoc](https://es.wikipedia.org/wiki/Javadoc), [Pydoc](https://docs.python.org/2/library/pydoc.html), etc. facilitan bastante la tarea de documentación, al menos la esencial.

No es lo mismo toparse con algo como esto:

<img src="https://drive.google.com/uc?export=view&id=1cy0brHvX9j-07lSKUcGy7YIrR7oa0rER" style="width: 500px; max-width: 100%; height: auto" title="Click for the larger version." />

que poder ver algo como esto:


<img src="https://drive.google.com/uc?export=view&id=12Vs6yFq73-GGvqWHxZYVIVmLBqNhdLoH" style="width: 500px; max-width: 100%; height: auto" title="Click for the larger version." />

Si bien, el nombre del método dice algo acerca de lo que hace, y podemos intuir  la función del parámetro requerido, siempre deja lugar a alternativas como por ejemplo que podría ser la fecha en que queremos que el proceso se ejecutase. Es siempre mejor evitemos las ambigüedades.

Otra ventaja derivada de la documentación, exagerada o no, de nuestro código es que, cuando lo tome un compañero con poca experiencia, no requerirá de mucho de nuestro tiempo para ayudarlo a entender el programa, sino que podrá leerlo en lenguaje "natural" y sólo recurriría a nosotros para temas puntuales y con menos frecuencia (salvo casos excepcionales de personas que se equivocaron de carrera y hasta aquí me limito en este comentario...).	
