<template>
  <div>
    <editor-menu-bar :editor="editor" v-slot="{ commands, isActive }">
      <div>
        <div>
          <button :class="{ 'is-active': isActive.bold() }" @click="commands.bold">Bold</button>
          <button
            type="button"
            :class="{ 'is-active': isActive.italic() }"
            @click="commands.italic"
          >Italic</button>
          <button
            type="button"
            :class="{ 'is-active': isActive.strike() }"
            @click="commands.strike"
          >Strike</button>
          <button
            type="button"
            :class="{ 'is-active': isActive.underline() }"
            @click="commands.underline"
          >Underline</button>
          <button
            type="button"
            :class="{ 'is-active': isActive.code() }"
            @click="commands.code"
          >Code</button>
        </div>
        <div>
          <button
            type="button"
            :class="{ 'is-active': isActive.heading({ level: 1 }) }"
            @click="commands.heading({ level: 1})"
          >H1</button>
          <button
            type="button"
            :class="{ 'is-active': isActive.heading({ level: 2 }) }"
            @click="commands.heading({ level: 2 })"
          >H2</button>
          <button
            type="button"
            :class="{ 'is-active': isActive.heading({ level: 3 }) }"
            @click="commands.heading({ level: 3 })"
          >H3</button>
          <button
            type="button"
            :class="{ 'is-active': isActive.heading({ level: 4 }) }"
            @click="commands.heading({ level: 4 })"
          >H4</button>
          <button
            type="button"
            :class="{ 'is-active': isActive.heading({ level: 5 }) }"
            @click="commands.heading({ level: 5 })"
          >H5</button>
          <button
            type="button"
            :class="{ 'is-active': isActive.heading({ level: 6 }) }"
            @click="commands.heading({ level: 6 })"
          >H6</button>
        </div>
        <div>
          <button
            type="button"
            :class="{ 'is-active': isActive.bullet_list() }"
            @click="commands.bullet_list"
          >Bullet List</button>
          <button
            type="button"
            :class="{ 'is-active': isActive.ordered_list() }"
            @click="commands.ordered_list"
          >Ordered List</button>
        </div>
        <div>
          <button
            type="button"
            :class="{ 'is-active': isActive.blockquote() }"
            @click="commands.blockquote"
          >Blockquote</button>
        </div>
        <div>
          <button type="button" @click="commands.horizontal_rule">Horizontal Rule</button>
        </div>
        <div>
          <button
            :class="{ 'is-active': isActive.bold() }"
            @click="commands.createTable({rowsCount: 3, colsCount: 3, withHeaderRow: false })"
          >Create Table</button>
        </div>
        <div>
          <button
            type="button"
            :class="{ 'is-active': isActive.todo_list() }"
            @click="commands.todo_list"
          >Todo List</button>
        </div>
      </div>
    </editor-menu-bar>
    <editor-content :editor="editor" />
    <button type="button" @click="saveToAirtable">Save to Airtable</button>
  </div>
</template>

<script>
</script>

<script>
import swal from "sweetalert2";
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
  // History,
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
  beforeRouteEnter: (to, from, next) => {
    next(vm => {
      if (vm.$store.state.user.user) {
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
  },
  components: {
    EditorContent,
    EditorMenuBar
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
        new TodoList()

        // History
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
      console.log('save to airtable');
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
