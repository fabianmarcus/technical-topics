# Nuxt.js

Nuxt.js ist das Framework für Vue.js, wie Next.js das Framework für React ist - mit anderen Buchstaben und Farben. Es macht aus einer Frontend-Bibliothek ein vollwertiges Framework.

## Dynamisches Routing

Wie auch Next.js unterstützt Nuxt dynamisches Routing über die Dateinamen im `pages`-Verzeichnis. Dateien, die in eckigen Klammern stehen, werden als dynamische Parameter interpretiert.

Beispiel:

```text
pages/
├── index.vue              -> /
├── about.vue              -> /about
├── blog/
│   ├── index.vue          -> /blog
│   └── [id].vue           -> /blog/:id (dynamisch)
|       └── overview.vue   -> /blog/:id/overview.vue (verschachtelt, dynamisch)
│   └── [...slug].vue      -> /blog/:slug* (verschachtelt, dynamisch)
```

### Slug

Der *"slug"* innerhalb der eckigen Klammern kann beliebig benannt werden. Sein Name wird dann als Parameter Teil der URL.

Innerhalb der Komponente kann der Parameter über die `useRoute`-Funktion aus dem `vue-router`-Paket abgerufen werden:

```ts
<script setup lang="ts">
import { useRoute } from 'vue-router'

const route = useRoute()
console.log(route.params.id) // Zugriff auf den Parameter "id" => 123
</script>
```

Mit vier eckigen Klammern (also zwei Paaren) kann ein Slug auch als optional definiert werden.

```text
pages/
└── blog/
    └── [[slug]].vue -> /blog und /blog/irgendwas
```

### Drei Punkte

Fügt man davor noch drei Punkte hinzu, kann der Parameter auch verschachtelt sein. Das bedeutet, dass er mehrere Segmente in der URL abbilden kann.

Das heißt, aus der Datei `pages/blog/[...slug].vue` kann man sowohl `/blog/123` als auch `/blog/123/456` oder `/blog/123/456/789` aufrufen. Der Parameter `slug` enthält dann ein Array mit den Werten der URL-Segmente.

Innerhalb der Komponente kann man den Parameter wie folgt abrufen:

```ts
<script setup lang="ts">
import { useRoute } from 'vue-router'

const route = useRoute()
console.log(route.params.slug) // Zugriff auf den Parameter "slug" (Array) => [123, 456, 789]
</script>
```

Da es hier mehrere Segmente sein können, ist der Parameter `slug` ein Array. Man kann also auf die einzelnen Segmente zugreifen, z.B. `route.params.slug[0]` für das erste Segment, `route.params.slug[1]` für das zweite Segment usw.
