---
theme: default
background: "./background2.avif"
class: text-center
highlighter: shiki
lineNumbers: false
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
drawings:
  persist: false
transition: slide-left
title: "Unleashing the Power of AI: Integrating Tiptap, OpenAI, and Vue.js"
mdc: true
---

# Unleashing the Power of AI: Integrating Tiptap, OpenAI, and Vue.js

---

transition: slide-up
layout: image-right
image: https://res.cloudinary.com/vannsl-io/image/upload/v1555928691/IMG_4127.jpg

---

# Hi!

I'm Vanessa Otto

Head of Frontend at <a href="https://www.zavvy.io/" target="_blank" rel="nofollow noopener">Zavvy</a> <br>
Co-Host of <a href="https://workingdraft.de" target="_blank" rel="nofollow noopener">Working Draft</a>, <a href="https://expect-exception.netlify.app/" target="_blank" rel="nofollow noopener">Working Draft</a>, and <a href="https://ausbaufaehig-podcast.de/" target="_blank" rel="nofollow noopener">Ausbaufähig</a> <br><br>
Social: @vannsl / @vanessa_otto<br>
<span class="opacity-60">X, Mastodon, Bluesky <span class="text-xs">and whatever will come in future</span></span>

<p v-click class="absolute bottom-23 left-45 transform -rotate-10">... and I wanted to add AI to our platform</p>

---

# What did you always want to learn about?

- Svelte
- How to cook veggies
- Gardening
- ...

---

# Today

we will talk about

<v-clicks>

- 📝 **Tiptap** - Rich text editor
- 🎨 **OpenAI** - to sprinkle AI on top
- 🧑‍💻 **Vue 3** - to implement it. But you can use whatever you like.

</v-clicks>

---

# What we needed

<a href="https://app.zavvy.io/company_admin/journeys/38835/builder/edit?sectionId=571744#/" target="_blank" rel="nofollow noopener">Zavvy</a> is a people enablement platform. One feature is to create and manage employee journeys used for Onboarding.

---

## layout: default

# Technical Research

<v-clicks>

- Backend compatible output, in JSON or HTML
- "Notion like" design (floating menus)
- Custom "blocks": embeddings, AI widgets, etc.
- Markdown Support, Keyboard Shortcuts, Collaboration
- Developer Experience\* (Docs, Vue 3 Support)

</v-clicks>

<p v-click class="opacity-60">*Strong DX can lead to great UX</p>

---

## transition: slide-up

<div style="overflow-y:scroll; height: 100vh;padding-bottom:400px;">
<img src="/research.png">
</div>

---

## transition: slide-up

### Decision for Tiptap

|               |                                            |
| ------------- | ------------------------------------------ |
| 🤩 **Pro**    | Headless UI                                |
|               | Modern framework, easy to get started with |
|               | Extensible (own plugins)                   |
| 😞 **Contra** | Headless UI means (almost) no UI           |
|               | Was in beta / still early release          |

<!--
|  | Albeit good documentation, no big community yet - and some hiccups after the release. Some examples with Vue 2, some in Vue 3. Although TypeScript support is pretty well, most examples come without. |
-->

---

## transition: slide-up

# Tiptap Framework

|                         |                                                      |
| ----------------------- | ---------------------------------------------------- |
| **Nodes**               | <code>block</code>                                   |
|                         | Paragraph, Bullet list, Code blocks, etc.            |
| **Marks**               | <code>inline</code>                                  |
|                         | bold, italic, code, etc.                             |
| **Extensions**          | First party, community, and your own                 |
| **Commands**            | Programmatically change content and alter selections |
| <code>@tiptap/pm</code> | Tiptap is built on ProseMirror, internals accessible |

---

## transition: slide-up

# Create Editor

```ts {all|4|5|6|7|8-10|8-10|6}
import { Editor } from "@tiptap/vue-3";

const editor = new Editor({
  editable: props.isEditable,
  autofocus: props.shouldAutofocus,
  extensions: initializeExtensions(),
  content: props.content || "",
  onUpdate: () => {
    updateModelValue(editor.getHTML());
  },
});
```

<arrow v-click="[3, 4]" x1="300" y1="350" x2="230" y2="220" color="#564" width="3" arrowSize="1" />

<div v-click="[3, 4]" class="absolute bottom-12 left-45 shadow">

```ts
extensions: initializeExtensions(
  {
    promptConfig: props.promptConfig,
    attachmentConfig: props.attachmentConfig,
  },
  t
),
```

</div>

<div v-click="[5, 7]">
<div class="absolute bottom-23 left-20 shadow">

```ts
const updateModelValue = debounce((value: string) => {
  const cleanedHtml = cleanHtml(value);
  const isEmpty = isHtmlEmpty(cleanedHtml);
  emit("update-content", isEmpty ? null : cleanedHtml);
}, 650);
```

</div>
<div v-click=[6,7] class="absolute bottom-23 right-0 shadow">

```ts
function cleanHtml(xmlString: string) {
  const documentFragment = document.createElement("template");
  documentFragment.innerHTML = xmlString;
  documentFragment.content
    .querySelectorAll("[zvy-js-client-only='true']")
    .forEach((el) => el.remove());

  return documentFragment.innerHTML;
}
```

</div>

</div>

<arrow v-click="[5, 7]" x1="300" y1="350" x2="230" y2="270" color="#564" width="3" arrowSize="1" />
<arrow v-click="[6, 7]" x1="520" y1="300" x2="300" y2="380" color="#564" width="3" arrowSize="1" />

---

## transition: slide-up

# Initialize Extensions

```ts {all|2|2-6|10-13|14-15|16-17|7-9,18-20}
[
  StarterKit.configure({
    heading: {
      levels: [1, 2, 3],
    },
  }),
  Commands.configure({
    suggestion: suggestions(), // Floating menu
  }),
  Placeholder.configure({
    emptyEditorClass: "v-editor-empty",
    placeholder: t("modules.rich_text_editor.placeholder"),
  }),
  Link.configure(),
  Underline.configure(),
  ActionTextAttachmentExtension.configure(),
  ZavvyAiExtension.configure(),
  BubbleMenu.configure({
    element: document.querySelector(".menu"),
  }),
];
```

<arrow  v-click="[1,2]" x1="530" y1="130" x2="250" y2="130" color="#564" width="3" arrowSize="1" />
<div v-click="[1,2]"  class="absolute top-20 right-20 shadow bg-white rounded p-4 grid grid-cols-2 gap-8">

<div>

## Nodes

- Blockquote
- BulletList
- CodeBlock
- Document
- HardBreak
- Heading
- HorizontalRule
- ListItem
- OrderedList
- Paragraph
- Text

</div>
<div>

## Marks

- Bold
- Code
- Italic
- Strike

## Extensions

- Dropcursor
- Gapcursor
- History

</div>
</div>

<arrow  v-click="[2, 3]" x1="380" y1="130" x2="250" y2="160" color="#564" width="3" arrowSize="1" />
<video v-click="[2, 3]" class="absolute top-20 right-20" width="500" autoplay controls>
  <!-- <source src="/headline.mov" type="video/mp4"> -->
</video>

<img v-click="[3, 4]" class="absolute top-60 right-20" src="/placeholder.png" width="300">

<div v-click="[4,5]" class="absolute top-50 right-20 shadow bg-white rounded p-4">

```ts
Link.configure({
  autolink: true, // default
  linkOnPaste: true, // default
  protocols: ["mailto"], // additional protocols - default: []
  openOnClick: true, // default
  HTMLAttributes: {
    rel: "noopener noreferrer",
    target: "_blank",
  },
}),
```

</div>

---

## transition: slide-up

# Bubble menu

<div v-click="[1,5]">

```html {all|all|3-5|7-12}
<TipTapBubbleMenu
  class="tw-flex tw-rounded tw-bg-background tw-p-1 tw-shadow-zv"
  :tippy-options="{ duration: 100 }"
  :editor="editor"
  :should-show="shouldShowHandler"
>
  <BubbleMenuButton
    :is-active="editor.isActive('bold')"
    icon="formatBoldOutline"
    label="Bold"
    @click="editor.chain().focus().toggleBold().run()"
  />
  <!-- ... -->
</TipTapBubbleMenu>
```

</div>

<img src="/bubble_menu_1.png" style="width:300px" class="absolute shadow top-10 right-20">
<img src="/bubble_menu_2.png" style="width:300px" class="absolute shadow bottom-10 right-20">

---

transition: slide-up
layout: image-right
image: "./floating_menu_1.png"

---

# Floating Menu

```ts {all|8-15}
const suggestions = [
  // ...
  {
    title: t("modules.rich_text_editor.suggestions.h1"),
    slug: "h1",
    icon: "formatH1Outline",
    command: ({ editor, range }) => {
      editor
        .chain()
        .focus()
        .deleteRange(range)
        .setNode("heading", { level: 1 })
        .run();
    },
  },
  // ...
];
```

---

transition: slide-up
layout: image-right
image: "./floating_menu.png"

---

# Floating Menu

```ts {all|11}
const items = [
  // ...
  {
    title: t("modules.rich_text_editor.suggestions.ask_zavvy_ai"),
    slug: "zavvy_ai_topic_completion",
    icon: "autoAwesomeOutline",
    command: ({ editor, range }) => {
      editor
        .chain()
        .deleteRange(range)
        .setZavvyAiPromptActionCompletion()
        .run();
    },
  },
  // ...
];
```

---

# AI Extension

<div class="overflow-scroll h-100">

```ts
const ZavvyAiExtension = Node.create<ZavvyAiOptions>({
  // ...
  name: "zavvy-ai"

  addAttributes() {
    return {
      "zvy-js-client-only": {
        default: "true",
      },
    };
  },

  addCommands() {
    return {
      setZavvyAiPromptActionCompletion:
        () =>
        ({ commands }) =>
          commands.insertContent({
            type: this.name,
            attrs: {
              prompt: "completion",
            },
          }),
    };
  },

  // ...
});
```

</div>

---

# NodeViewWrapper: ZavvyAi.vue

```html
<script setup lang="ts">
  import { nodeViewProps, NodeViewWrapper } from "@tiptap/vue-3";

  const props = defineProps(nodeViewProps);

  constole.log(props.node.attrs.prompt); // "completion"
</script>

<template>
  <NodeViewWrapper> Hello world! </NodeViewWrapper>
</template>
```

---

# Input Field

```html
<script setup lang="ts">
  import { ref } from "vue";

  const inputValue = ref("");
  const responseText = ref("");

  watch(inputValue, (value) => {
    // make call to open AI with prompt
    responseText.value = "Dummy Response";
  }
</script>

<template>
  <NodeViewWrapper>
    <input v-model="inputValue" />
    <div v-if="responseText">{{ responseText }}</div>
  </NodeViewWrapper>
</template>
```

---

---

# // make call to open AI with prompt - and then what?

- Frontend sends the prompt to Backend
- Backend sends the prompt to OpenAI
  - either directly
  - or using a service (e.g. <a href="https://azure.microsoft.com/en-us/solutions/ai" target="_blank" rel="noopener">Azure AI</a>)
- OpenAI sends the response to Backend, and Backend back to Frontend

---

# Insert and delete

<div class="overflow-scroll h-100">

```html
<script setup lang="ts">
  const props = defineProps(nodeViewProps);

  function insertAndDeleteNode() {
    // inserts AI content directly before this node
    props.editor
      .chain()
      .focus()
      .insertContentAt(
        props.editor.state.selection.$anchor.pos,
        responseText.value
      )
      .run();

    props.deleteNode();
    props.editor.commands.focus();
  }
</script>

<template>
  <NodeViewWrapper>
    <button type="button" @click="insertAndDeleteNode">Insert!</button>
  </NodeViewWrapper>
</template>
```

</div>

---

## transition: slide-left

# OpenAI

## Completion (Legacy)

Receives a simple prompt:

```
Translate the following English text to French: "{text}"
```

<p></p>

<div v-click>

## Chat Completion

Receives an array of messages:

```
[{"role": "user", "content": 'Translate the following English text to French: "{text}"'}]
```

</div>

<div v-click>

=> Nowadays, use the `Chat Completion` API which uses the newer models.

</div>

---

## transition: slide-up

# OpenAI Models

|                            | **Model families**                                               | **API Endpoint**                           |
| -------------------------- | ---------------------------------------------------------------- | ------------------------------------------ |
| Newer models (2023–)       | gpt-4, gpt-3.5-turbo                                             | https://api.openai.com/v1/chat/completions |
| Updated base models (2023) | babbage-002, davinci-002                                         | https://api.openai.com/v1/completions      |
| Legacy models (2020–2022)  | text-davinci-003, text-davinci-002, davinci, curie, babbage, ada | https://api.openai.com/v1/completions      |

---

## transition: slide-left

# Prompt injection

> Prompt injection is the process of hijacking a language model's output.

<p></p>

<div v-click>

```
Translate the following English text to French: "{text}"
```

</div>

---

## transition: slide-up

# Prompt injection

<img src="/prompt_injection.png" class="h-80">

<a href="https://gandalf.lakera.ai/" target="_blank" rel="noopener noreferrer" class="text-center">Gandalf Game</a>

---

## layout: end

Thanks

Twixxter: @vannsl

Mastodon: @vanessa_otto@hachyderm.io
