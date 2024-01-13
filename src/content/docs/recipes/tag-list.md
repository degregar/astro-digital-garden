---
title: Tag list
tags: [page]
---

## Implementation in Starlight

```astro
//src/components/Tags.astro
---
import { getCollection } from "astro:content";

const docs = await getCollection("docs");
type Docs = typeof docs;
const docsByTags = new Map<string, Docs>();
docs.forEach((doc) => {
  if (doc.data.tags) {
    doc.data.tags.forEach((tag: string) => {
      docsByTags.set(tag, docsByTags.get(tag) || []);
      docsByTags.get(tag)?.push(doc);
    });
  }
});
const comparator = new Intl.Collator("en");
const tagsSorted = [...docsByTags.keys()].sort(comparator.compare);
---

<ul>
  {
    tagsSorted.map((tag) => (
      <li>
        {tag} ({docsByTags.get(tag)?.length})
        <ul>
          {docsByTags.get(tag)?.map((doc) => (
            <li>
              <a href={`/${doc.slug}`}>{doc.data.title}</a>
            </li>
          ))}
        </ul>
      </li>
    ))
  }
</ul>
```

```mdx
//src/content/docs/tags.mdx
---
title: Tag list
tableOfContents: false
prev: false
next: false
---

import Tags from "../../components/Tags.astro";

<Tags />
```

```ts
//src/content/config.ts
import { z, defineCollection } from "astro:content";
import { docsSchema, i18nSchema } from "@astrojs/starlight/schema";

export const collections = {
  docs: defineCollection({
    schema: docsSchema({
      extend: z.object({
        tags: z.array(z.string()).optional(),
      }),
    }),
  }),
  i18n: defineCollection({ type: "data", schema: i18nSchema() }),
};
```

## Further improvements

- add tags to pages. Maybe in sidebar?
- add many column design. Similar to https://stereobooster.com/tags/
- page for each tag. I think this is impossible in Starlight