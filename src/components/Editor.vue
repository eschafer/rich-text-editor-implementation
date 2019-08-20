<template>
  <div class="editor">
    <h1>{{new Intl.DateTimeFormat('en-US', { dateStyle: 'full' }).format(new Date())}}</h1>
    <editor-menu-bar :editor="editor" v-slot="{ commands, isActive }">
      <div class="menubar">

        <div class="menubar__group">
          <button
            aria-label="undo"
            class="menubar__button"
            @click="commands.undo"
          >
            <icon name="undo" />
          </button>

          <button
            aria-label="redo"
            class="menubar__button"
            @click="commands.redo"
          >
            <icon name="redo" />
          </button>
        </div>

        <div class="menubar__group">
          <button
            aria-label="bold"
            class="menubar__button"
            :class="{ 'is-active': isActive.bold() }"
            @click="commands.bold"
          >
            <icon name="bold" />
          </button>

          <button
            aria-label="italic"
            class="menubar__button"
            :class="{ 'is-active': isActive.italic() }"
            @click="commands.italic"
          >
            <icon name="italic" />
          </button>

          <button
            aria-label="strikethrough"
            class="menubar__button"
            :class="{ 'is-active': isActive.strike() }"
            @click="commands.strike"
          >
            <icon name="strike" />
          </button>

          <button
            aria-label="underline"
            class="menubar__button"
            :class="{ 'is-active': isActive.underline() }"
            @click="commands.underline"
          >
            <icon name="underline" />
          </button>
        </div>

        <div class="menubar__group">
          <button
            aria-label="inline code"
            class="menubar__button"
            :class="{ 'is-active': isActive.code() }"
            @click="commands.code"
          >
            <icon name="code" />
          </button>
        </div>

        <div class="menubar__group">
          <!-- <button
            class="menubar__button"
            :class="{ 'is-active': isActive.paragraph() }"
            @click="commands.paragraph"
          >
            <icon name="paragraph" />
          </button> -->

          <button
            aria-label="H1"
            class="menubar__button"
            :class="{ 'is-active': isActive.heading({ level: 1 }) }"
            @click="commands.heading({ level: 1 })"
          >
            H1
          </button>

          <button
            aria-label="H2"
            class="menubar__button"
            :class="{ 'is-active': isActive.heading({ level: 2 }) }"
            @click="commands.heading({ level: 2 })"
          >
            H2
          </button>

          <button
            aria-label="H3"
            class="menubar__button"
            :class="{ 'is-active': isActive.heading({ level: 3 }) }"
            @click="commands.heading({ level: 3 })"
          >
            H3
          </button>
        </div>

        <div class="menubar__group">
          <button
            aria-label="bulleted list"
            class="menubar__button"
            :class="{ 'is-active': isActive.bullet_list() }"
            @click="commands.bullet_list"
          >
            <icon name="ul" />
          </button>

          <button
            aria-label="numbered list"
            class="menubar__button"
            :class="{ 'is-active': isActive.ordered_list() }"
            @click="commands.ordered_list"
          >
            <icon name="ol" />
          </button>
        </div>

        <div class="menubar__group">
          <button
            aria-label="block quote"
            class="menubar__button"
            :class="{ 'is-active': isActive.blockquote() }"
            @click="commands.blockquote"
          >
            <icon name="quote" />
          </button>
        </div>

        <div class="menubar__group">
          <button
            aria-label="multiline code"
            class="menubar__button"
            :class="{ 'is-active': isActive.code_block() }"
            @click="commands.code_block"
          >
            <icon name="code" />
          </button>
        </div>

        <!-- <button
          class="menubar__button"
          @click="commands.horizontal_rule"
        >
          <icon name="hr" />
        </button> -->

        

      </div>
    </editor-menu-bar>
    <editor-content :editor="editor" class="editor__content" />
    <!-- <button type="button" @click="saveToAirtable">Save to Airtable</button> -->
  </div>
</template>

<script>
import Icon from './Icon'
// import swal from "sweetalert2";
import { Editor, EditorContent, EditorMenuBar } from "tiptap";
import {
  Blockquote,
  Bold,
  BulletList,
  Code,
  CodeBlock,
  CodeBlockHighlight,
  HardBreak,
  Heading,
  History,
  HorizontalRule,
  Italic,
  Link,
  ListItem,
  OrderedList,
  // Placeholder,
  Table,
  TableHeader,
  TableCell,
  TableRow,
  TodoItem,
  TodoList,
  Strike,
  Underline
} from "tiptap-extensions";

export default {
  name: "Editor",
  /* beforeRouteEnter: (to, from, next) => {
    next(vm => {
      if (vm.$store.state.user.user || true) {
        return next();
      } else {
        swal.fire({
          title: "Error!",
          text: "Please log in or sign up!",
          type: "error",
          allowOutsideClick: false,
          confirmButtonText: "All Right"
        });
        return next({ name: "Home" });
      }
    });
  }, */
  components: {
    EditorContent,
    EditorMenuBar,
    Icon
  },
  data() {
    const savedJson = `{
  "type": "doc",
  "content": [
    {
      "type": "paragraph",
      "content": [{ "type": "text", "text": "paragraph" }]
    },
    {
      "type": "blockquote",
      "content": [
        {
          "type": "paragraph",
          "content": [{ "type": "text", "text": "Blockquote" }]
        }
      ]
    },
    {
      "type": "paragraph",
      "content": [
        { "type": "text", "marks": [{ "type": "bold" }], "text": "Bold" }
      ]
    },
    {
      "type": "bullet_list",
      "content": [
        {
          "type": "list_item",
          "content": [
            {
              "type": "paragraph",
              "content": [{ "type": "text", "text": "BulletList" }]
            }
          ]
        }
      ]
    },
    {
      "type": "paragraph",
      "content": [
        { "type": "text", "marks": [{ "type": "code" }], "text": "Code" }
      ]
    },
    {
      "type": "code_block",
      "content": [{ "type": "text", "text": "CodeBlock" }]
    },
    {
      "type": "code_block",
      "content": [{ "type": "text", "text": "CodeBlockHighlight??" }]
    },
    { "type": "paragraph", "content": [{ "type": "hard_break" }] },
    {
      "type": "heading",
      "attrs": { "level": 1 },
      "content": [{ "type": "text", "text": "Heading 1" }]
    },
    { "type": "horizontal_rule" },
    {
      "type": "paragraph",
      "content": [
        { "type": "text", "marks": [{ "type": "italic" }], "text": "Italic" },
        { "type": "text", "text": " " },
        {
          "type": "text",
          "marks": [{ "type": "link", "attrs": { "href": "#" } }],
          "text": "Link"
        }
      ]
    },
    {
      "type": "ordered_list",
      "attrs": { "order": 1 },
      "content": [
        {
          "type": "list_item",
          "content": [
            {
              "type": "paragraph",
              "content": [{ "type": "text", "text": "OrderedList" }]
            }
          ]
        }
      ]
    },
    { "type": "paragraph" },
    {
      "type": "table",
      "content": [
        {
          "type": "table_row",
          "content": [
            {
              "type": "table_header",
              "attrs": {
                "colspan": 1,
                "rowspan": 1,
                "colwidth": null,
                "background": null
              },
              "content": [
                {
                  "type": "paragraph",
                  "content": [{ "type": "text", "text": "TableHeader" }]
                }
              ]
            }
          ]
        },
        {
          "type": "table_row",
          "content": [
            {
              "type": "table_cell",
              "attrs": {
                "colspan": 1,
                "rowspan": 1,
                "colwidth": null,
                "background": null
              },
              "content": [
                {
                  "type": "paragraph",
                  "content": [{ "type": "text", "text": "TableCell" }]
                }
              ]
            }
          ]
        }
      ]
    },
    {
      "type": "paragraph",
      "content": [
        { "type": "text", "marks": [{ "type": "strike" }], "text": "Strike" },
        { "type": "text", "text": " " },
        {
          "type": "text",
          "marks": [{ "type": "underline" }],
          "text": "Underline"
        }
      ]
    }
  ]
}
`;

    // Create an `Editor` instance with some default content. The editor is
    // then passed to the `EditorContent` component as a `prop`
    const editor = new Editor({
      content: JSON.parse(savedJson),
      extensions: [
        new Bold(),
        new Italic(),
        new Strike(),
        new Underline(),
        new Code(),
        // paragraph?

        new Heading(),

        new BulletList(),
        new OrderedList(),
        new ListItem(),

        new Blockquote(),

        new CodeBlock(),
        new CodeBlockHighlight(),

        new HorizontalRule(),

        new HardBreak(),

        new Link(),

        new Table(),
        new TableCell(),
        new TableHeader(),
        new TableRow(),

        new TodoItem(),
        new TodoList(),

        new History()
        // Placeholder
      ],
      onUpdate: ({ getJSON }) => {
        this.json = getJSON();
        // console.log(JSON.stringify(this.json));
      }
    });

    return {
      editor
    };
  },
  methods: {
    clearContent() {
      this.editor.clearContent(true);
      this.editor.focus();
    },
    setContent() {
      // you can pass a json document
      this.editor.setContent(
        {
          type: "doc",
          content: [
            {
              type: "paragraph",
              content: [
                {
                  type: "text",
                  text: "This is some inserted text. 👋"
                }
              ]
            }
          ]
        },
        true
      );
      // HTML string is also supported
      // this.editor.setContent('<p>This is some inserted text. 👋</p>')
      this.editor.focus();
    },
    saveToAirtable() {
      // console.log('save to airtable');
      const data = {
        "Name": "vue test", 
        "Notes": JSON.stringify(this.json),
      }
      fetch("../../.netlify/functions/airtable", {
      method: "POST",
      body: JSON.stringify(data)
      })
      .then(() => alert("Form Sent!"))
      .catch(error => alert(error))
    }
  },
  beforeDestroy() {
    // Always destroy your editor instance when it's no longer needed
    this.editor.destroy();
  }
};
</script>

<style>
.editor {
  display: flex;
  flex-direction: column;
  max-width: 50rem;
  margin: 0 auto;
}
.editor__content {
  min-height: 80vh;
  background: white;
  overflow-wrap: break-word;
  word-wrap: break-word;
  word-break: break-word;
}
.editor__content > div[contenteditable="true"] {
  min-height: 100%;
  margin-bottom: 80px;
}
.editor__content > div[contenteditable="true"]:focus {
  /* outline: 2px solid #204468; */
  outline: none;
}
.editor__content * {
  caret-color: currentColor;
}
.editor__content pre {
  padding: 0.7rem 1rem;
  border-radius: 5px;
  background: #000;
  color: #fff;
  font-size: 0.8rem;
  overflow-x: auto;
}
.editor__content pre code {
  display: block;
}
.editor__content p code {
  font-family: "Roboto Mono", monospace;
  padding: 0.2rem 0.4rem;
  border-radius: 2px;
  background: rgba(0, 0, 0, 0.1);
}
.editor__content ul, .editor__content ol {
  padding-left: 1rem;
}
.editor__content li > p, .editor__content li > ol, .editor__content li > ul {
  margin: 0;
}
.editor__content a {
  color: inherit;
}
.editor__content blockquote {
  border-left: 3px solid rgba(0, 0, 0, 0.1);
  color: rgba(0, 0, 0, 0.8);
  padding-left: 0.8rem;
  font-style: italic;
}
.editor__content blockquote p {
  margin: 0;
}
.editor__content img {
  max-width: 100%;
  border-radius: 3px;
}
.editor__content table {
  border-collapse: collapse;
  table-layout: fixed;
  width: 100%;
  margin: 0;
  overflow: hidden;
}
.editor__content table td, .editor__content table th {
  min-width: 1em;
  border: 2px solid #ddd;
  padding: 3px 5px;
  vertical-align: top;
  box-sizing: border-box;
  position: relative;
}
.editor__content table td > *, .editor__content table th > * {
  margin-bottom: 0;
}
.editor__content table th {
  font-weight: bold;
  text-align: left;
}
.editor__content table .selectedCell:after {
  z-index: 2;
  position: absolute;
  content: "";
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  background: rgba(200, 200, 255, 0.4);
  pointer-events: none;
}
.editor__content table .column-resize-handle {
  position: absolute;
  right: -2px;
  top: 0;
  bottom: 0;
  width: 4px;
  z-index: 20;
  background-color: #adf;
  pointer-events: none;
}
.editor__content .tableWrapper {
  margin: 1em 0;
  overflow-x: auto;
}
.editor__content .resize-cursor {
  cursor: ew-resize;
  cursor: col-resize;
}

.menubar {
  display: flex;
  position: sticky;
  top: 0;
  background: #ffffff;
  z-index: 1;
  border-bottom: 1px solid rgba(0, 0, 0, 0.5);
  margin: 0;
  height: 40px;
  transition: visibility 0.2s 0.4s, opacity 0.2s 0.4s;
}
.menubar.is-hidden {
  visibility: hidden;
  opacity: 0;
}
.menubar.is-focused {
  visibility: visible;
  opacity: 1;
  transition: visibility 0.2s, opacity 0.2s;
}
.menubar__group {
  border-right: 1px solid #ddd;
  margin: 4px 0;
  display: flex;
}
.menubar__button {
  font-weight: bold;
  display: inline-flex;
  background: transparent;
  border: 0;
  color: #000;
  padding: 0.2rem 0.5rem;
  margin: 0 0.2rem;
  border-radius: 3px;
  cursor: pointer;
}
.menubar__button:hover {
  background-color: rgba(0, 0, 0, 0.05);
}
.menubar__button.is-active {
  background-color: rgba(0, 0, 0, 0.1);
}
span.menubar__button {
  font-size: 13.3333px;
}
</style>