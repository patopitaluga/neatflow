# Neat-flow

Manual de hábitos de uso de repositorios y deploys para un equipo de desarrollo pequeño/mediano.

Por: patricio.pitaluga@gmail.com

Este manual propone una serie de hábitos para mantener la calidad y la eficacia en el desarrollo de un proyecto informático. El objetivo no es crear burocracia en los procesos sino generar confianza y predictibilidad y mantener la calidad del código. Está pensado para un equipo de trabajo de escala pequeña/mediana. Puede (y deberá ser) modificado con el consenso del equipo para adaptarse a las necesidades cambiantes del proyecto.

## Ventajas de este esquema

Los releases e issues son identificados y pueden referirse y conversarse fácilmente. El QA completo se hace en un único momento por lo que es integral y no demora el trabajo diario. Si una feature no pasa el proceso de QA se puede re-planear para el siguiente sprint y hacer el release con el resto de los features. No requiere casos complejos de uso de git.

## Desventajas

Desaconseja el deploy continuo. Los releases se planean orgánicamente y dificulta la anticipación a largo plazo. Si se cancela un release hay que volver a hacer pull request de los features no deprecados, pero está el ticket del release cancelado como referencia. Demanda tiempo y compromiso del equipo. Desaconseja hot fixes, si bien los permite.

## Esquema ideal

```
                +
                |        |
                |        |
                +--------+
                |     Release candidate v1.2.1
                |
Release 1.2.0  ++
                | <------+ QA
                |        |
                |        |
                |        | <------+ Peer review
                |        |        |
                |        |        ++ Commit
                |        |        |
                |        |        ++ Commit
          ^     |        |        |
          |     +-----------------+ Branch ticket 102
          |     |        |
          |     |        |
          |     |        | <------+ Peer review
          +     |        |        |
       Tiempo   |        |        ++ Commit
                |        |        |
                +-----------------+ Branch ticket 101
                |        |
                |        |
                +--------+
                |     Release candidate v1.2.0
                +
              Master
```

Nota: no existe el branch dev ni staging oficialmente dentro del plan, pero pueden existir en el repositorio para lanzar los deploys de ser necesarios. La publicación del proyecto en estados "dev", "staging" o "QA" son procesos que se harán en el branch releasecandidate o en los feature branch según la etapa.

## Branchs releasecandidate-x.x.x y features
### Releasecandidate-x.x.x branch

El equipo decide hacer un release que incluya un paquete de features, de ser posible agrupados por algún objetivo general. Se genera el **ticket de release** con la lista de los features (tickets) que incluirá.

Una persona del equipo tendrá la responsabilidad de ser el **release manager**. Abrirá un branch releasecandidate-x.x.x desde el branch master. El branch releasecandidate-x.x.x será el destinatario de los merge request de los features elegidos. El **release manager** será el encargado de constatar que actualizar el **ticket de release** si el plan del release cambia.

El nombre de versión es a consensuar con el equipo. Tipo A.B.C. Se recomienda: cambiar A para cambios sustanciales en el producto, B para nuevas secciones, features o refactoring y C para bug fixing o poca diferencia con el release anteriores.

En el caso más común no habrá dos branchs releasecandidate al mismo tiempo, pero en caso de haberlo, se abrirá el branch releasecandidate-x.x.x de igual manera y se considerará qué features branch
deben incluirse, incluso si pertenecen también a otro release, según sean necesarios y no debería causar conflicto.

El branch master es la fuente para cada nuevo branch porque solo contiene features aprobados y testeados, todos ellos enlazados al **ticket de release** correspondiente. Todos los features en master han pasado por **dos** procesos de aprobación (el QA integral del release y el peer review).

El branch releasecandidate-x.x.x nunca debe tomarse de referencia para generar nuevos branchs porque contiene contenido que puede no haber pasado el QA o que ha sido cancelado. Es una herramienta del **release manager** y sirve para hacer QA sobre él. Puede borrarlo y generarlo en cualquier momento.

-----------
### Generación de feature

El miembro del equipo que tenga asignado un ticket, puede en cualquier momento abrir un branch desde el branch master, el nombre será feature/ticket-12345. Si la tarea no tiene un ticket pero se ha decidido y informado al **release manager** y al equipo, puede llamarlo por cualquier nombre identificable (desaconsejado).

Se trabaja y se hacen los commits necesarios en ese branch. Luego se hace pull request al release candidate actual. Al finalizar abrirá un merge request hacia el branch releasecandidate relacionado con ese ticket.

### Aprobación del merge request del feature branch

El desarrollador puede servir el contenido de su feature branch en un entorno local o remoto para poder mostrar su trabajo al equipo. Debe parecerse al entorno productivo en lo posible. Debe tomar medidas de seguridad que limiten el acceso a los bots de los buscadores y a ajenos al proyecto. Debe indicarse mediante console.log, un mensaje en pantalla o un archivo json en la carpeta pública el número del ticket que está demeando, esto puede generarse automáticamente.

Lo puede aprobar el **release manager**, un par o la misma persona que trabajó en el feature, según el nivel de seguridad configurada en el repositorio. De ser posible requerir uno o dos reviews, dependiendo del tamaño del equipo. Realizar algunos de estos hábitos para evitar la “ceguera de desarrollador”.

#### **_Ideal_**: Peer review del código + Rubber ducking + testeo de par
* **Uso**: habitual
* **Tiempo aproximado**: 10/30 minutos
* **Responsabilidad ante error**: del proceso. Sugerir cambios a este manual ante falla.

#### **_Alternativa_**: Rubber ducking con alguien del equipo
* **Uso**: Si no hay otro programador disponible
* **Tiempo aproximado**: 20 minutos
* **Responsabilidad ante error**: del equipo. Mejorar la disponibilidad y atención al proceso.

#### **_Desaconsejado_**: Rubber ducking con Duckie (solo)
* **Uso**: Para tickets menores como typo's u omisiones
* **Tiempo aproximado**: 20 minutos
* **Responsabilidad ante error**: personal y del proceso. Ante falla: no hacerlo habitualmente y requerir asistencia al equipo.

![Duckie](http://i.imgur.com/141k7uPm.jpg) ⬅ Duckie

```
Rubber ducking 🦆

Hacer una demo mostrando qué tarea se hizo, qué problemas soluciona, cuál es el funcionamiento esperado y cómo está funcionando, en distintos dispositivos y casos de uso.

La explicación misma generará ideas para identificar posibles problemas y soluciones.
```

### Fix de errores surgidos en la aprobación del feature

Se realizan sobre el mismo branch del feature y luego se solicita nueva aprobación.

```
            |        |
            |        |
            |        | <------+ Peer review
            |        |        |
            |        |        |
            |        |        ++ Commit corrección
            |        |        |
            |        |        ++ Peer review
            |        |        |
            |        |        ++ Commit
            |        |        |
            |        |        ++ Commit
            |        |        |
            +-----------------+ Branch ticket 101
            |        |
            |        |
            +--------+
            |      Release candidate v1.2.0
            +
          Master
```

### Requerir otro feature branch en una feature

Es desaconsejado. Solo para casos aislados de ser necesario. Si una feature es requerida y no ha ingresado aún a master, puede requerirse desde ese mismo feature branch. Si se hace, incluir las características a aprobar de ambas features en el rubber ducking y QA y avisar al **release manager** que deje constancia en el **ticket de release**. En el ejemplo, el ticket 102 requiere características del 101 y el 101 no se había agregado al relese candidate. Tener en cuenta que no podrá cancelarse un feature y aprobarse el que lo incluye.

```
                |        |
                |        |
                |        | <------+ Peer review 102 y 101
                |        |        |
                |        |        ++ Commit
                |        |        |         Si necesita 101
                |        |        | <-------------------+
                |        |        |                     |
                +-----------------+ Branch ticket 102   |
                |        |                              |
                |        |                              |
                |        |                              |
                |        |        |                     |
                |        |        +---------------------+
                |        |        |
                |        |        ++ Commit
                |        |        |
                +-----------------+ Branch ticket 101
                |        |
                |        |
                +--------+
                |      Release candidate v1.2.0
                +
              Master
```

### QA

El deploy a un entorno de staging debe realizarse automáticamente con un comando o pipeline del repositorio. Este generará un console.log, archivo en la carpeta púbica o aviso en pantalla que indique la versión del release para que pueda identificarse fácilmente. Debe ser igual al entorno productivo. Deben haber sido tomadas medidas de seguridad que limiten el acceso a los bots de los buscadores y a personas ajenas al proyecto. Si el proyecto aplica cache debe refrescarse automáticamente con cada deploy en el entorno staging y productivo.

Se avisa al equipo o al responsable de QA que está disponible el release candidate online.

Se utilizarán herramientas de testeos para comprobar que el comportamiento es el esperado de cada feature. Se testearán endpoints y regresion tests de css, múltiples dispositivos y testings de seguridad, stress y penetration.

Se deberá probar exhaustivamente las características de los features nuevos y también el funcionamiento de las anteriores e integralmente del sitio.

Si una feature no alcanza los objetivos esperados, vuelve a desarrollo y se notifica a la persona del equipo que ha hecho la tarea. Ella hará las correcciones y commits en el mismo branch y volverá a pedir el pull request al releasecandidate. Si son menores, no es necesario hacer peer review nuevamente. Hacerlo en el caso de que los cambios para el fix sean muchos.

### Fix de problemas encontrados en QA

```
          +
          | <------+
          |        ++ QA
          |        |
          |        | <----------------------+ Peer review
          |        |                        |
          |        |                        ++ Commit corrección
          |        |                        |
          |        ++ QA encuentra fallas   |
          |        |                        |
          |        |        -----------------
          |        | <------+ Peer review
          |        |        |
          |        |        ++ Commit
          |        |        |
          |        |        ++ Commit
          |        |        |
          +-----------------+ Branch ticket 101
          |        |
          |        |
          +--------+
          |     Release candidate v1.2.0
          +
        Master
```

### Aprobación del release candidate y merge a master

**Si algún criterio quedó sin resolver se conversa con el equipo los motivos por los cuales no puede cumplir los requisitos y se planea una nueva task para hacerlo si es necesario.**

Aprobado el QA, el **release manager** hará el pull request del branch releasecandidate-x.x.x al branch master, actualizará el estado del **ticket de release**.

### Deploy a producción

Puede realizarse inmediatamente aprobado el merge a master o posteriormente según el plan del **release manager**.

El deploy a producción debe realizarse automáticamente con un comando o pipeline del repositorio.

Al finalizar el deploy actualizar el **ticket del release** con la fecha en la que fue deployado y anunciar al equipo con felicitaciones, confeti y brindis.

----

### Cancelar el release (quitar features)

Si en la etapa de QA se encuentra que una feature no alcanza el objetivo y no se pueden resolver los problemas o se requiere con urgencia deployar otras features del release, se puede cancelar el release y generar uno nuevo.

Se abrirá un nuevo branch releasecandidate con una nueva numeración de release. Se solicitará pull request de las features requeridas exceptuando la que no pudo resolverse. El release llevará un nuevo número de versión para poder referirse al release candidate calcelado de ser necesario.

```
        +        +
        |        |
        |        | <------------------------------+
        |        |                                |
        |        |                                |
        +--------+                                |
        |      Release candidate v1.2.1           |
        |                                         |
        |        + QA falla                       |
        |        |    se decide postergar         |
        |        |                                |
        |        | <------+ Peer review           |
        |        |        |                       |
        |        |        ++ Commit               |
        |        |        |                       |
        +-----------------+ Branch ticket 102     |
        |        |                                |
        |        |        +-----------------------+
        |        |        |
        |        | <------+ Peer review
        |        |        |
        |        |        ++ Commit
        |        |        |
        +-----------------+ Branch ticket 101
        |        |
        +--------+
        |     Release candidate v1.2.0
        +
      Master
```

---------

## Hot fix

Desaconsejado. Si se encuentra un error en producción y debe resolverse rápidamente puede abrirse un branch hotfix como /hotfixes/hotfix-123 o la descripción, a criterio.

El branch del hotfix debería tener dos pull request. Uno al branch master y otro al branch releasecandidate activo. Al aprobar uno, aprobar el otro. Notificar al **release manager** para que actualice el **ticket del release** activo.

**¿Qué problemas pueden resolverse con un hotfix?**
Typos y contenido de demo como lorem ipsum o placeholders images. Información equivocada o no relevante que pueda cambiarse rápidamente.

**¿Qué problemas no deberían resolverse con un hotfix?**
Nuevos features. Problemas no urgentes. Pequeños bugs no notables.

```
  |        |
  |        |
  |        |
  |        |
  |        | <-----+
  |        |       | Merge a master
  | <--------------+    y al releasecandidate activo
  |        |       |
  |        |       |
  |        |       ++ Commit
  |        |       |
  +----------------+
  |        |   Branch hotfix 231
  |        |
  +--------+
  |     Release candidate v1.2.0
  +
Master
```

-------

## Revisión de proceso de git flow

Hacer una reunión trimestral con el equipo para revisión de este proceso (y este documento) para encontrar fallas, fortalezas y sugerir mejoras.
