<template>
  <div class="markdown-render" v-html="markdownToHtml" v-copy-code />
</template>
<script>
import { marked } from "marked";
import { markedHighlight } from "marked-highlight";
import hljs from "highlight.js";
import * as DOMPurify from "dompurify";

const preprocess = (html) => {
  return html.replace(/[^\S\r\n]+$/gm, "");
};
const postprocess = (html) => {
  return DOMPurify.sanitize(html);
};

DOMPurify.addHook("beforeSanitizeAttributes", (node) => {
  if (node.tagName === "svg") {
    const width = node.getAttribute("width");
    const height = node.getAttribute("height");
    const viewBox = node.getAttribute("viewBox");
    if (!viewBox && width && height) {
      node.setAttribute("viewBox", `0 0 ${width} ${height}`);
    }
  }
});

marked.use(
  { hooks: { preprocess, postprocess } },
  markedHighlight({
    langPrefix: "hljs language-",
    highlight(code, lang) {
      const language = hljs.getLanguage(lang) ? lang : "plaintext";
      return hljs.highlight(code, { language }).value;
    },
  })
);

export default {
  name: "RenderMarkdownBaseComponent",
  props: {
    markdown: {
      type: String,
      required: true,
    },
  },
  computed: {
    markdownToHtml() {
      return marked.parse(this.markdown, {
        headerIds: false,
        mangle: false,
        breaks: true,
      });
    },
  },
};
</script>
<style lang="scss" scoped>
.markdown-render {
  white-space: normal;
  word-break: break-word;
  :deep() {
    hr {
      width: 100%;
    }
    blockquote {
      font-style: italic;
    }
    pre {
      white-space: pre-wrap;
      word-break: break-all;
    }
    code:not(.hljs) {
      color: palette(orange-red-crayola);
      background-color: palette(apricot, light);
      border-radius: 4px;
    }
    a {
      word-break: break-all;
      color: $primary-color;
    }
    h1,
    h2,
    h3,
    h4,
    h5 {
      line-height: 1.4em;
    }
    p,
    strong,
    em,
    h1,
    h2,
    h3,
    h4,
    h5 {
      margin-top: 0;
      margin-bottom: $base-space;
    }
    svg {
      max-width: 100% !important;
      height: auto !important;
    }
    img {
      max-width: 100%;
    }
  }
}
:deep() {
  .hljs {
    position: relative;
    font-family: monospace, serif;
    margin: 0;
    background-color: #333346;
    color: white;
    padding: 2em !important;
    border-radius: $border-radius;
    text-align: left;
    font-weight: 500;
    @include font-size(13px);
  }

  .hljs-keyword,
  .hljs-selector-tag,
  .hljs-literal,
  .hljs-section,
  .hljs-link {
    color: #3ef070;
    font-weight: bold;
  }
  .hljs-deletion,
  .hljs-number,
  .hljs-quote,
  .hljs-selector-class,
  .hljs-selector-id,
  .hljs-string,
  .hljs-template-tag,
  .hljs-type {
    color: #febf96;
  }
  .hljs-string,
  .hljs-title,
  .hljs-name,
  .hljs-type,
  .hljs-attribute,
  .hljs-symbol,
  .hljs-bullet,
  .hljs-addition,
  .hljs-variable,
  .hljs-template-tag,
  .hljs-template-variable {
    color: #a0c7ee;
  }
  .hljs-built_in {
    color: #8fbb62;
  }
  .hljs-tag,
  .hljs-punctuation,
  .hljs-tag .hljs-attr,
  .hljs-tag .hljs-name {
    color: #c0a5a5;
  }
}
</style>
