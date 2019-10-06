name: cover
layout: true
class: center, middle, inverse
---
# Desarmando Doppler

---
name: intro
layout: false
## Por qué, Qué y Cómo

???

Vamos a dividir esta presentación en tres secciones.

Desde hace tiempo estamos trabajando en estos cambios, primero de a poco y cada
vez vamos más rápido. En algunas partes de Doppler más y en otras menos.

Nos concentraremos en cambios de arquitectura, y en consejos prácticos de como
encararlos. Esos cambios tienen un transfondo cultural que rozaremos, pero trataremos
de dejar eso para otra exposición.

--

* ¿**Por qué** lo estamos haciendo diferente?

???

Primero, explicaremos las razones que tenemos para modificar nuestra arquitectura.

Esa explicación también nos dará pistas de hacia donde vamos y como vamos a lograrlo.

--

* ¿**Qué** estamos haciendo?

???

Luego, explicaremos como aplican esos cambios en la arquitectura de Doppler. Los
diferentes cambios que queremos hacer y un poco en que orden lo estamos haciendo.

Es la sección menos preparada, pero es posible que surja una buena conversación.

--

* ¿**Cómo** vamos a lograrlo?

???

Finalmente, daremos algunas estratégias o tips para aplicar en el día a día, commit
a commit, feature a feature y en cada sprint para lograrlo de forma eficiente.

---
name: por-que-cover
layout: false
class: center, middle, inverse
## Por qué

¿Por qué lo estamos haciendo diferente?

???

Explicaremos las razones que tenemos para modificar nuestra arquitectura.

Esa explicación también nos dará pistas de hacia donde vamos y como lo lograremos.

---
name: por-que-accelerate
layout: false
## ¿Que es lo que funciona?

 — Ver [Doppler IT 2018](https://andresmoschini.github.io/doppler-it-2018/#18)

.image-scaled[![Overall Research Program](overall-research-program.png)]

???

No se si recuerdan la charla que habíamos tenído a principio de año acerca los
cambios que estábamos haciendo en el equipo de tecnología.

Muchos de esos cambios son culturales, algunos vamos a repasarlos en la última
parte de la charla, pero otros afectan bastante directamente a la arquitectura.

* Loosely Coupled Architecture
* Test Automation
* Deployment Automation
* Empowered Teams
* Continuous Delivery

---
name: por-que-scale-1
layout: false
##  Scale out >> Scale up

.image-scaled[![Scale up / Scale out](scale.png)]

???

En lugar de tener fierros más grandes, tener más fierros.

---
name: por-que-scale-2
layout: false
## Federated Architecture >> Monolithic Architecture

.image-scaled[![Federated Architecture / Monolithic Architecture](scale2.png)]

???

Pero no solo muchos fierros, las dependencias en un sistema complejo son un problema. Entonces,
es bueno tener muchos sistemas más pequeños tan desacoplados como podamos.

---
name: por-que-big-smash
layout: false

.image-scaled[![Big Smash](big-smash.png)]

???

Smash ~= Aplastar

---
name: por-que-small-pokes
layout: false

.image-scaled[![Small Pokes](small-pokes.png)]

???

Poke ~= Empujar
---
name: por-que-conway-law
layout: false
## Conway's Law

> _Any organization that designs a system (defined broadly) will produce a design
> whose structure is a copy of the organization's communication structure._
>
> — Melvin E. Conway, "How Do Committees Invent?"

???

Esta es una de las razones por las que queremos equipos independientes y
multidisciplinarios, para lograr sistemas completos, con buena calidad desde el
comienzo y desacoplados.

---
name: por-que-técnicas-exitosas
layout: false
## ¿Que hacen los que están en la pomada?

.comparative-table-with-arrow[
 head | → | head
------------- | -- | -------------
 Monolithic Architecture | → | Federated Architecture
 Database as Integrator | → | API's as Connectors
 Stateful Protocols | → | Stateless Protocols
 Consecutive Execution | → | Concurrent Execution
 Sync Communication | → | Async Communication
 Procedural | → | Event Driven
 Defect Free / Fragile | → | Fault Tolerant / Antifragile
 Server configuration documentation | → | Infrastructure as Code
 Teams by expertize area | → | Cross‐functional teams (Product, QA and Ops)
 Manual releases | → | Automated build, testing, dbmigration, and deployment
 Big features | → | Incremental development on the trunk w/CI
 Regression Test before release | → | Always production ready, or everything stops until it is
 Large Releases | → | Deploy constantly, release by switch
]

???

* Monolithic Architecture → Federated Architecture: sistemas desacoplados
* Database as Integrator → API's as Connectors: integrar en la DB causa acoplamiento, por ahora es lo que tenemos
* Stateful Protocols → Stateless Protocols: por ejemplo, evitar cookies, evitar mantener estado en el server
* Consecutive Execution → Concurrent Execution
* Sync Communication → Async Communication: hay algo de esto en Relay, pero poco, por ejemplo al crear un mensaje, mucho del trabajo es sincrónico, pero los hooks son asincrónicos.
* Procedural → Event Driven
* Defect Free / Fragile → Fault Tolerant / Antifragile

---

name: por-que-checkpoint
layout: false
## Por qué

¿Preguntas?

* ¿Qué es lo que funciona?
* Scale out >> Scale up
* Federated Architecture >> Monolithic Architecture
* SMASH / poke, poke, poke, ...
* Conway's Law
* ¿Que hacen los que están en la pomada?
* _¿Otras?_

---
name: que-cover
layout: false
class: center, middle, inverse
## Qué

¿Qué estamos haciendo?

???

Que estamos haciendo con Doppler y con la arquitectura actual. Los diferentes
cambios que queremos hacer y un poco en que orden los haremos.

No vamos a ir tan lejos, hay mucho para cambiar y algunas cosas aún no las vamos
a atacar directamente todavía, por ejemplo la "DB como Integrador", aunque ahora
vamos a estar más abiertos a distribuir los datos.

Se muestra una parte de Doppler, por ejemplo no se ve Relay o el DMS/MTA. pero
igualmente podemos charlar sobre ellos y sobre como encajan aquí.

---
name: que-actual
layout: false
## Arquitectura actual

.image-scaled[![Current Architecture](architecture-current.png)]

???

Acá podemos hablar de [JWT](https://jwt.io/) y como está funcionando.

---
name: que-option1
layout: false
## Arquitectura opción 1

.image-scaled[![Architecture, Option 1](architecture-option1.png)]

---
name: que-option2
layout: false
## Arquitectura opción 2

.image-scaled[![Architecture, Option 2](architecture-option2.png)]

???

Pensar en dar una plataforma que puedan usar directamente los clientes, más allá
de nuestra UI.

Ejemplo de los nuevos formularios por dominio, la página de formularios podría
estar desacoplada de Doppler, ser una SPA, y manejar la conexión por API. Incluso
podría tener un backend independiente y en background ir subiendo los cambios a
Doppler.

En cada oportunidad que tengamos tenemos que generar esos servicios independientes.

---

name: que-checkpoint
layout: false
## Qué

¿Preguntas?

* Arquitectura actual
* Nuevos servicios
* JWT
* Cookies
* Autenticación
* API first
* _¿Otras?_

---
name: como-cover
layout: false
class: center, middle, inverse
## Cómo

¿Cómo vamos a lograrlo?

???

Cómo podemos avanzar para poder llevar adelante estos cambios necesarios.

Daremos algunas estratégias o tips para aplicar en el día a día, commit a commit,
feature a feature y en cada sprint para lograrlo de forma eficiente.

Algunas serán más abstractas y otras más concretas, solo daremos algunas sueltas.

---
name: como-antifragile
layout: false

# Antifragile

.image-scaled[![Fragile / Robust / Antifragile](fragile-robust-antifragile.png)]

???

Queremos tener equipos pequeños multidisciplinarios que generen aplicaciones
pequeñas. Pero, no es cuestión de tener equipos chicos y listo, queremos equipos
flexibles, que logren trabajar cada vez mejor y aprovechen los conflictos para
ser mejores.

Por eso, no alcanza con que trabajemos como una maquinita bien aceitada, somos
personas, tenemos que comunicarnos efectivamente, no temer a la redundancia, por
ejemplo del pair programming o leer PRs al punto de entender todo lo que pasa.

Otro ejemplo es la calidad: una opción es tener una persona que hace el código y
otra que lo prueba cuando está terminado, e ir haciendo ping-pong. La alternativa
es que se hagan pasos chicos, que más de uno pueda seguir como cambia el
comportamiento del sistema, que rápidamente se pueda testear, que el experto de
calidad pueda mentorear a los demás, que se puedan hacer diferentes tipos de tests
y que el experto de calidad pueda leer los unit test, etc.

Ejemplo de _edición de tarjeta de crédito_. Detectamos _tarde_ un problema de
usabilidad, no importa, hablémoslo, no tengamos miedo de perder tiempo, podemos
aplicar el cambio o no, pero la conversación nos ayuda a compartir conocimiento
y entender mejor el contexto.

Otros ejemplos posibles, 10X developer, The Zone.

---
name: como-pasitos-0
layout: false

# Pequeños pasos incrementales (o no tanto)

Queremos construir esta feature:

.center[![La feature que queremos](pasitos-completo.jpg)]

Podemos tomarnos 4 sprints y hacerlo completo, incluso, todo en un mismo _Pull Request_.

--

Pero

* No entregamos valor hasta el final.

--

* Si algo pasa y tenemos que dar prioridad a otra cosa, es muy probable que se 
pierda el esfuerzo.

--

* Si la feature estaba mal definida y no resuelve el problema deseado, recién lo
descubrimos al final.

--

* ¿Quien va a poder encontrar un bug en ese PR?

--

* ¿Se va a poder probar toda la feature?

--

* ¿Como sabemos que no hay huecos? _

---
name: como-pasitos-1
layout: false

# Pequeños pasos incrementales (o no tanto)

.center[![Parte por parte](pasitos1.jpg)]

--

Pero

* No entregamos valor hasta el final.

--

* Si algo pasa y tenemos que dar prioridad a otra cosa, es muy probable que se
pierda el esfuerzo. ¿Que hacemos con esa rueda?

--

* Si la feature estaba mal definida y no resuelve el problema deseado, recién lo
descubrimos al final.

--

* Se puede probar cada paso, pero no en contexto, ¿que ocurre si en el análisis
o diseño olvidamos algo? _

---
name: como-pasitos-2
layout: false

# Pequeños pasos incrementales (o no tanto)

.center[![Mejorando el auto](pasitos2.jpg)]

--

Pero

* El primer paso es muy grande, hasta llegar a él, tenemos los problemas anteriores.

--

* Tenemos pasos funcionales pero poco elegantes, ya que todo está pensado para
que el paso final sea el ideal.

--

* Si algo pasa y tenemos que dar prioridad a otra cosa, es muy probable que
terminemos con esa camioneta fea. _

---
name: como-pasitos-3
layout: false

# Pequeños pasos incrementales (o no tanto)

.center[![Cada paso un vehículo](pasitos3.jpg)]

--

Pero

* No sabemos re-usar el monopatín para hacer la bicicleta.

???

Si hacemos el software como máquinas, hacer el monopatín no nos sirve para hacer
la bicicleta.

Pero si hacemos el software como profesionales, aprendemos, generamos estructuras
para hacer más fácil estructuras más complejas.

--

* Tardamos más.

--

* ¿Tardamos más? _

???

Tal vez descubrimos que lo que necesitábamos era la bicicleta, entonces tardamos
menos.

Las otras alternativas no garantizan que lleguemos a lo que necesitábamos, o que
no aparecieran escenarios inesperados.

---
name: como-naming
layout: false

# Nombres (y algo más)

> _There are only two hard things in Computer Science: cache invalidation and naming things._
>
> — Phil Karlton

???

Seguro escucharon esa frase, yo trato de citar ese chiste cada tanto.

--

> _There's two hard problems in computer science: we only have one joke and it's not funny._
>
> — Phillip Bowden

???

Jeje. Pero ahora enserio, los nombres son importantes, pero no solo por el nombre, sino
porque tienen que ver como como pensamos nuestro código. Ejemplo:

* `HasRepotationLimit` vs `ForceStoreMessage`
* `REACT_APP_ROUTER = hash | browser` vs `REACT_APP_CDN_BUILD = true | false`

--

.center.image-200px[![Domain Driven Design](domain-driven-design.png)]

???

¿Cuantos leyeron ese libro? Yo tampoco, pero leí varios artículos relacionados.

Ejemplo del modelo de DB de SMS (YAGNI, nombres, estar en sintonía con el cliente,
no solo tiene que funcionar)

Importa en todas las capas del sistema, sobre todo cuando varios de nosotros
trabajamos sobre la misma base de código, pero principalmente tenemos que usarlo
en las APIs.

---
name: como-acronims
layout: false

## YAGNI

_You Aren't Gonna Need It._

???

Cada cosa que hacemos, nos ata. Hagamos solo lo necesario, pensemos en el futuro,
pero no dejemos cosas preparadas para después, más bien, tratamos de que lo que
hacemos tenga sentido en un modelo, si es así, podrá evolucionar bien.

--

## SOLID

???

Tenemos que estudiar estos pricipios y aplicarlos siempre que podamos, o al
menos tenerlos en cuenta cuando decidamos romperlos. 

Solo voy a enumerar los principios, no voy a dar un curso sobre ellos, tampoco
soy un experto.

--

* **`S`** - Single responsibility principle

???

> _Una clase debe tener solo una razón para cambiar._
> 
> — Uncle Bob

También deberíamos tener en cuenta la inversa. En lo posible, si la lógica cambia
deberíamos modificar solo una clase.

--

* **`O`** - Open/closed principle

???

Las entidades abiertas para extensión, pero cerradas para modificación.

Hay momentos en que vale la pena refactorizar e introducir breking changes, pero
por defecto, tenemos que mantener compatibilidad hacia atrás, tanto en API's como
en objetos.

--

* **`L`** - Liskov substitution principle

???

Tenemos que tenerlo en cuenta en diseños donde hay diferentes tipos que se comportan
como uno, evitar `if` basados en tipos, pero lo mismo clases que representan muchos
conceptos a la vez. En algún punto se relaciona con el SRP: si rompemos este principio
cuando algo cambie en la lógica del negocio tendremos que cambiar el código en
múltiples lugares.

--

* **`I`** - Interface segregation principle

???

Los clientes de un programa sólo deberían conocer de éste aquellos métodos que realmente usan, y no aquellos que no necesitan usar.

Cuanto más expongamos, más nos atamos.

--

* **`D`** - Dependency inversion principle

???

Los módulos de alto nivel no deben depender en módulos de bajo nivel. Ambos deben depender de abstracciones.

Eso no solo nos permite intercambiar las implementaciones y ayuda al implementar tests, sino que también
nos ayuda a definir más claramente los límites de cada clase y lograr un sistema menos acoplado.

---
name: como-más
layout: false

# Saquemos ventaja del compilador

.left-column[
```csharp
var value = 0;
if (condition)
{
    value = 100;
}
else
{
    value = 200;
}
operation(value);
```

]
.right-column[
```csharp
int value;
if (condition)
{
    value = 100;
}
else
{
    value = 200;
}
operation(value);
```
]

???

¿Cual código es mejor?

Supongamos que ya no tiene sentido el segundo caso.

--

.left-column[
```csharp
var value = 0;
if (condition)
{
    value = 100;
}


operation(value);
```

]
.right-column[
```csharp
int value;
if (condition)
{
    value = 100;
}

// Error: `value` could be not defined
operation(value);
```
]

???

Si queremos que el valor por defecto sea cero, debemos hacerlo explicitamente.

--

.left-column[
```csharp
var value = condition ? 200 : 0;
operation(value);
```
]

???

Mejor todavía, usemos el operador ternario y evitemos la _mutabilidad_.

Ese es un ejemplo cualquiera, hay muchas formas de aprovechar al compilador, _campos readonly_, no usar 
nullables cuando no corresponde, etc. Las restricciones del compilador están para ayudarnos!

En JavaScript podemos usar _const_ para evitar ciertos tipos de mutabilidad, en c# no tenemos eso, pero
también deberíamos evitarlo.

Lo mismo con Linq u otras formas de programación declarativa.

---

# Mucho más

* Cada cambio incluido en un commit tiene que tener una razón, no tiene que haber ni una línea de más, ni una de menos.
* Tests, tests y más tests y cuanto más livianos mejor.
* Automatizar ejecución de tests y otras restricciones, por ejemplo de estilo de código, en cada PR.
* Code Review, no solo para calidad, sino para transferencia de conocimiento.
* Dedicar tiempo al otro, hablemos, ayudémonos, pensemos, hagamos pair programming.
* Falacia de la _Deuda Técnica_.
* Otras?

???

La idea es poder asegurar la calidad y la coordinación desde el principio.

---
name: como-equipo
layout: false

# Equipo >> grupo de trabajo

.center[![Team performance curve](team-performance.jpg)]

???

Para finalizar.

Dediquemos tiempo a mejorar como equipo, tiene un costo, pero el beneficio es muy grande.
---

name: como-checkpoint
layout: false
## Cómo

¿Preguntas?

* Antifragile
* Pequeños pasos
* Nombres
* DDD
* YAGNI
* SOLID
* Aprovechar el compilador
* Commits minimalistas
* Tests automatizados
* Continuous integration
* Code Review
* Dedicar tiempo al otro
* Falacia de la Deuda Técnica.
* Equipo >> grupo de trabajo
* _¿Otras?_


---

name: gracias
layout: false
class: center, middle, inverse

¡Gracias! 😊

---

## Referencias

* Diapositivas [Doppler IT 2018](https://andresmoschini.github.io/doppler-it-2018/)

* Muchas de las imágenes son _tomadas_ de la presentación [The Future of Software Engineering](https://gotocon.com/berlin-2016/presentations/show_talk.jsp?oid=7916) de Mary Poppendieck en GOTO Berlin 2016.

* Diagrama de _Overall Research Program_ del libro [Accelerate](https://makingsense.slack.com/files/U04U7GL8D/FFK3H0280/accelerate_-_forsgren_phd.mobi) 2018 Forsgren PhD. by Nicole Forsgren, Jez Humble, and Gene Kim.

* Melvin E. Conway, "How Do Committees Invent?" ([Ley de Conway](http://www.melconway.com/Home/Conways_Law.html))

