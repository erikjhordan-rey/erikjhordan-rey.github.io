---
layout: post
cover:  assets/images/2017/12/gradle.png
title: Automatiza, Distribuye y Prueba una Android App (Part 1)
date: 2020-03-16 00:00:00 +0545
categories: blog
author: erik
---

# Automatiza, Distribuye y Prueba una Android App

En el día a día comúnmente los equipos de software (mobile) tenemos la responsabilidad de implementar soluciones enfocadas en el negocio, proveer mejoras de funcionalidad, reparar bugs, cumplir con deadlines, entre otras y al final el objetivo es lograr que un usuario pueda disfrutar de nuestro producto de la mejor forma posible. 

Para que un build llegue a Google Play Console hay un camino largo y en ocasiones complicado, la falta de automatización de procesos, la no cultura de pruebas automatizadas/manuales o procesos complejos con el equipo de QA, son el tipo de problemas más recurrentes a los que me he enfrentado como developer en diversos proyectos.

## Common Problems

**No tienes un buen flujo de release, cuando:**

* El lead del equipo tiene que generar el build manualmente en su máquina.
* Solo un miembro del equipo sabe cómo hacer un release.
* Tienes que conectar un device a tu computadora para que alguien de tu equipo pueda probar esa versión del apk o el bundle
* Envías un apk por slack, drive, dropbox u otra. 
* No puedes hacer de forma fácil hotfix, no tienes un release branch o un tag que te permita solucionarlo de forma fácil sin tener que pasar toda la regresión de la versión.
* Alguien de tu equipo necesita mostrar un avance prematuro y no se puede hacer el release por que se “rompe” en alguna parte.
* Firmas manualmente el apk o bundle y luego arrastras a Google Play Console.
* El equipo de QA no sabe que probar y pregunta cada hora si ya esta listo el build.
* Te preguntan qué incluye este nuevo build
* No es sencillo generar otra versión tras una regresión si se encontraron errores y se repararon.
* Necesitamos liberar mañana “ya como este no hay tiempo”

Quizás alguno de estos puntos te parezca familiar y probablemente te pasen en tu día a día, por tal razón quiero mostrar el cómo solucionarlo aplicando estrategias que ayuden a mejorar la experiencia de hacer un release, quizás te sea de utilidad y logres llevarlo a tu equipo, o te de una idea para crear tu propio [flujo](https://nvie.com/posts/a-successful-git-branching-model/) de [trabajo](https://hackernoon.com/a-branching-and-releasing-strategy-that-fits-github-flow-be1b6c48eca2), recuerda que cada release impacta a todo el equipo no solo a los mobile developers.

## Understanding automation software strategies

#### Continuous Integration

En la práctica significa que los miembros de un equipo tengan la facilidad de integrar su trabajo a un core **(master, develop u otro)** branch, normalmente se hace mediante un pull request para asegurar que la futura integración no contiene errores y si los tiene detectarlos de forma rápida. 
Es aquí donde la compilación automatizada, verificación de code style, code analysis, linters, y la suite de tests automatizados se ejecuta, es la forma que tienes para mantener tu repositorio core fuera de sorpresas.

#### Continuous Delivery

Es una disciplina y forma de hacer software en la cual tiene como objetivo que siempre se pueda llevar build a producción en cualquier momento. Es una práctica compleja dado que requiere de procesos internos y mejorar formas de trabajo dentro del equipo, como bien sabemos para distribuir una aplicación en Google Play requiere de ser firmada antes lo que lo dificulta un poco más la implementación.

En este caso podríamos decir que no hacemos del todo [continuous delivery](https://martinfowler.com/bliki/ContinuousDelivery.html) por el simple hecho de poner un build de forma automatizada y requerir de presionar un botón para hacer el release en Google Play Store o [Firebase App Distribution](https://firebase.google.com/docs/app-distribution) por que el flujo de trabajo que usamos es enfocado más a desarrollar features ;pero tampoco hacemos **continuous development** por que cada cosa que integramos nuevo código no va a producción directamente, va a nuestro ambiente de pruebas.

Así que para mantenerlo simple y sin ponernos un tanto filosóficos automatizar un release de android lo resumiría en la forma en la que mantenemos el producto de software en estado liberable, lo que permite llevar a producción funciones de manera rápida, así como responder ante cualquier falla que pueda ocurrir. 

## Tooling to automate android releases

### Continuous Integration Tool

Existen algunas alternativas en el mercado que te pueden ayudar a implementar CI dependiendo las necesidades de tu proyecto. En mi experiencia he podido usar gran parte de estas herramientas, la complejidad de integración en ocasiones depende mucho de qué plataforma git utilizas [bitbucket](https://bitbucket.org/product/), [gitlab](https://about.gitlab.com/), [github](https://github.com/) u otro ;pero la mayoría de veces la configuración es similar. 

Actualmente utilizamos [Travis CI](https://docs.travis-ci.com/) dado que tenemos alojado el proyecto en github, y es la herramienta que mejor dominamos en el equipo, aún así  puedes mirar otras alternativas:

* [Travis CI](https://docs.travis-ci.com/)
* [Jenkins](https://jenkins.io/doc/)
* [Circle CI](https://circleci.com/)
* [Bitrise](https://www.bitrise.io/)
* [GitLab CI](https://about.gitlab.com/stages-devops-lifecycle/continuous-integration/)
* [Bamboo](https://www.atlassian.com/es/software/bamboo)


### App Distribution

Desde hace unos años he utilizado [Fabric Beta](https://docs.fabric.io/android/beta/overview.html) para distribuir un apk con mi equipo y hacer todo tipo de pruebas antes de llegar a producción en Google Play. En el 2017 [Crashlytics](https://firebase.google.com/docs/crashlytics) fue adquirido por Google con Fabric incluido, al día de hoy Fabric está deprecated y está por cerrar el 31 de Marzo de 2020, así que si no lo sabías sugiero que migres a [Firebase](https://firebase.google.com/?hl=es-419) en los siguientes días.

### Firebase App Distribution

Es la herramienta que nos permite compartir nuestra aplicación con testers externos o internos de una forma simple, ya que basta con compartir un link de invitación para poder convertirte en tester de la aplicación. Si estás familiarizado con Fabric entenderlo será más simple.

Existen 4 formas de distribuir una app en Firebase App Distribution: 

* [Firebase console](https://firebase.google.com/docs/app-distribution/android/distribute-console)
* [Firebase CLI](https://firebase.google.com/docs/app-distribution/android/distribute-cli) 
* [fastlane](https://firebase.google.com/docs/app-distribution/android/distribute-fastlane) 
* [Gradle](https://firebase.google.com/docs/app-distribution/android/distribute-gradle)

Si deseas integrarlo con tu servicio de [continuous integration](https://martinfowler.com/articles/continuousIntegration.html) la opción a utilizar es gradle o fastlane si tu proyecto lo utiliza. En nuestro caso utilizamos la configuración de gradle para integrar con [Travis CI](https://docs.travis-ci.com/).

El objetivo de esta introducción es entender los problemas a los que nos enfrentamos en el día a día como mobile developers, mostrar las estrategias que podemos utilizar para resolverlo y los problemas que nos trae el no tener un flujo de liberación definido. En el siguiente post muestro compartiré algunos tips para que puedas implementarlo en tu proyecto. 