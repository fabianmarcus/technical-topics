# Vue.js

Eine Einarbeitung für ein mögliches Projekt.

## Allgemeines

[Vue.js](https://vuejs.org/) ist ein weiteres JavaScript-Framework. Mit [React.js](https://reactjs.org/) und [Angular](https://angular.io/) gehört es wahrscheinlich zu den drei bekanntesten Frontend-Frameworks. React und Vue.js sind sich sehr ähnlich. Die Struktur eines Moduls ist etwas anders, die Konzepte sind aber fast gleich. Wer bereits Erfahrung mit React hat (und Angular - wegen der Template-Syntax), wird sich in Vue.js sehr schnell zurechtfinden.

Hier geht es eher um adaptives Lernen. Um möglichst schnell in Vue.js einzusteigen, notiere ich Unterschiede und Gemeinsamkeiten der beiden Frameworks.

### Single-File Components

Der typische Aufbau eines Vue.js-Moduls mit der Endung `.vue` sieht wie folgt aus:

```ts
<script setup>
import { ref } from 'vue'

const title = ref('Hello Vue.js')
const description = ref('Dies ist eine Beschreibung.')
</script>

<template>
  <div>
    <h1>{{ title }}</h1>
    <p v-if="description">{{ description }}</p>
  </div>
</template>

<style scoped>
h1 {
  /* styles... */
}
</style>
```

Durch das `<script setup>`-Tag lässt sich im Skript-Teil ziemlich frei ["funktionsorientiert"](https://vuejs.org/guide/extras/composition-api-faq.html#what-is-composition-api) entwickeln, ohne auf eine bestimmte Struktur achten zu müssen.

Mehr zu dem Thema gibt es in der offiziellen Vue.js-Doku: [Single File Components](https://vuejs.org/guide/scaling-up/sfc.html).

## Parallelen zu React

### Modulstruktur

In React exportiert ein Modul (für gewöhnlich) genau eine Komponenten-Funktion. In einer solchen Komponenten-Funktion ist sowohl die Logik als auch das Markup enthalten. Das Markup wird von der Funktion als JSX zurückgegeben. Styles können auf unterschiedliche Weise eingebunden werden; ich importiere meistens CSS-Dateien und schreibe in diesen dann natives CSS.

In Vue.js besteht ein Modul aus drei getrennten Teilen: Script, Template und Style. Der Script-Teil enthält die Logik, der Template-Teil das Markup und der Style-Teil die CSS-Styles. Das Markup wird in Vue.js nicht als JSX geschrieben, sondern als HTML mit eigener Template-Syntax (`v-if`, `v-for`, usw.). Der Styles-Bereich kann von Haus aus gescoped werden, sodass die Styles nur für die jeweilige Komponente gelten.

Die letztlich generierte Ausgabe einer Komponente wird in Vue.js also über HTML-Attribute der Template Engine gesteuert, während in React weiterhin reines JavaScript die Ausgabe kontrolliert.

### State - Lokal

Auch in Vue.js gibt es reaktive State-Variablen. In React werden diese über `useState` erzeugt, in Vue.js über `ref` (siehe Beispiel oben). Der Zugriff auf den Wert einer State-Variable erfolgt in React direkt über die Variable, geändert wird sie über eine extra `set`-Funktion. In Vue.js erzeugt `ref` ein Objekt mit einer `.value`-Eigenschaft. Über diese `.value`-Eigenschaft wird der Wert gelesen und per Zuweisung geschrieben - es ist also keine extra Funktion zum Ändern des Werts nötig. Wird `value` geändert, wird die Komponente automatisch neu gerendert.

### State - Global

Wie auch in React gibt es in Vue.js die Möglichkeit, Werte global für alle Komponenten verfügbar zu machen. Die Prinzipien sind auch hier ziemlich gleich:

- Einerseits gibt es externe Bibliotheken. In React ist das z.B. `zustand` oder `redux`. In Vue.js ist das z.B. `pinia` oder `vuex`. `Nuxt.js` bringt von Haus aus `pinia` mit.
- Andererseits gibt es eine nativ integrierte Möglichkeit, wenn auch nicht so mächtig: In React ist das der `Context`, in Vue.js ist das `provide` und `inject`.

Mit `provide` kann eine Komponente Werte bereitstellen, die von allen untergeordneten Komponenten über `inject` abgerufen werden können. Die Lösung in Vue.js scheint meines Erachtens etwas leichter zu sein, da kein extra Provider-Wrapper nötig ist, sondern die Werte einfach bereitsgestellt und woanders wieder abgefragt werden können.

Beispiel:

Den Benutzer über `provide` für alle Kind-Komponenten bereitstellen:

```ts
/* ParentComponent.vue */

<script setup lang="ts">
import { provide, ref } from "vue";

const user = ref({ name: "Mara", role: "Admin" });

// Key + Wert nach unten bereitstellen
provide("currentUser", user);
</script>

<template>
  <section>
    <h2>Parent</h2>
    <ChildComponent />
  </section>
</template>
```

Den Benutzer über `inject` abrufen:

```ts
/* ChildComponent.vue */

<script setup lang="ts">
import { inject, type Ref } from "vue";

type User = { name: string; role: string };

// Fallback ist optional
const user = inject<Ref<User>>("currentUser");

if (!user) {
  throw new Error("currentUser wurde nicht bereitgestellt.");
}
</script>

<template>
  <article>
    <h3>Child</h3>
    <p>Name: {{ user.name }}</p>
    <p>Rolle: {{ user.role }}</p>
  </article>
</template>
```

### Watchers

Was in React über `useEffect` gesteuert wird, wird in Vue.js über Watchers (`watch` und `watchEffect`) gesteuert. Mit Watchern ist es möglich, auf Änderungen von State-Variablen zu reagieren.

In folgendem Beispiel wird jedes Mal geloggt, wenn sich der Wert von `count` ändert:

```ts
import { ref, watch } from 'vue'

const count = ref(0)

watch(count, (newValue, oldValue) => {
  console.log(`Count changed from ${oldValue} to ${newValue}`)
})
```

Mit `watchEffect` kann auch auf mehrere Variablen reagiert werden, ohne diese explizit angeben zu müssen:

```ts
import { ref, watchEffect } from 'vue'

const count = ref(0)
const name = ref('Vue.js')

watchEffect(() => {
  console.log(`Count is now ${count.value}`)
  console.log(`Name is now ${name.value}`)
})
```

Bei jeder Änderung von `count` oder `name` wird die Funktion erneut ausgeführt. Ein Unterschied zwischen React und Vue ist, dass in `watchEffect` die Abhängigkeiten (also die zu beobachten reaktiven Variablen) automatisch erkannt werden; in React müssen sie explizit angegeben werden, was oft zu Performance Issues und endlosen Rerenderings führt. Vue.js kümmert sich da in `watchEffect` selbst drum.

### Automatisch vererbte Attribute

Praktisch sind auch "Fallthrough Attributes". Ruft eine Komponente eine andere Komponente auf, werden alle HTML-Attribute der Eltern-Komponente (z.B. `class`, `style`, `id`, `v-on`, usw.) automatisch an das Root-Element der Kind-Komponente weitergereicht, auch wenn sie nicht explizit in der Kind-Komponente definiert sind. Dadurch muss in der Kind-Komponente nicht extra die Übergabe dieses Props berücksichtigt werden, was sie einfacher und schlanker macht.

Mehr dazu hier: [Fallthrough Attributes](https://vuejs.org/guide/components/attrs.html).

### Composables

In React gibt es Custom Hooks, in Vue.js gibt es Composables. Composables sind Funktionen, die eine bestimmte State basierte Funktionalität kapseln und wiederverwendbar machen. Sie können in mehreren Komponenten verwendet werden, um Logik zu teilen. Das Prinzip ist das gleiche wie bei Custom Hooks in React, nur dass die Syntax etwas anders ist. Auch die Benennung ist ähnlich: Composables und Custom Hooks beginnen mit `use`, z.B. `useCounter` oder `useFetch`.

## Composition API

Alle oben genannten reaktiven Funktionen innerhalb des `<script setup>`-Tags werden in Vue unter dem Begriff [Composition API](https://vuejs.org/guide/extras/composition-api-faq.html) zusammengefasst. Dazu gehören Funktionen wie `ref`, `reactive`, `computed`, `watch` und `watchEffect`. Sie wurde in Vue 3 eingeführt und hat die [Options API](https://vuejs.org/guide/typescript/options-api) (mit `data`, `methods`, `computed`, usw.) abgelöst.

Die [Options API](https://vuejs.org/guide/typescript/options-api) ist weiterhin verfügbar, wird aber nicht mehr weiterentwickelt. Sie funktioniert objektorientiert und arbeitet mit `this`, während die [Composition API](https://vuejs.org/guide/extras/composition-api-faq.html) funktional konzipiert ist. Das erleichtert Wiederverwendbarkeit und Testbarkeit.

## Nuxt.js

Nuxt.js ist das Framework für Vue.js, wie Next.js das Framework für React ist - mit anderen Buchstaben und Farben. Es macht aus einer Frontend-Bibliothek ein vollwertiges Framework.

### Routing

Der Router sucht für gewöhnlich im `pages`-Verzeichnis nach Dateien.

- Einfacher Dateiname = einfache Route: `pages/blog.vue` → `/blog`
- Unterverzeichnis = verschachtelte Route: `pages/blog/artikel-123.vue` → `/blog/artikel-123`
- Klammern im Dateinamen = dynamische Route: `pages/blog/[artikelId].vue` → `/blog/123` (dynamisch)
- Drei Punkte und Klammern = verschachtelte dynamische Route: `pages/blog/[...artikelId].vue` → `/blog/123/456` (dynamisch)

### Rendering

Nuxt bringt von Haus aus Unterstützung für verschiedene Rendering-Strategien mit, die sonst mit Vue aufwendig zusätzlich konfiguriert werden müssten:

- **Server-Side Rendering (SSR)**: Die Seite wird auf dem Server gerendert und als fertiges HTML an den Client geschickt. Dies ist gut für SEO und die initiale Ladezeit.
- **Static Site Generation (SSG)**: Die Seiten werden beim Build einmalig generiert und als statische HTML-Dateien bereitgestellt. Dies ist gut für Performance und Hosting auf CDNs.
- **Client-Side Rendering (CSR)**: Die Seite wird komplett im Browser gerendert. Dies ist gut für interaktive Anwendungen, die keine SEO benötigen.
- **Incremental Static Regeneration (ISR)**: Eine Mischung aus SSG und CSR, bei der Seiten statisch generiert werden, aber bei Bedarf aktualisiert werden können.

Die jeweilige Rendering-Strategie kann in der `nuxt.config.js` Datei oder im Routing pro Seite konfiguriert werden.
