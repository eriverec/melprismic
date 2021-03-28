<template>
  <nuxt-link :to="link">
    <div class="blog-post">
      <h2>{{ $prismic.asText(vestido.data.titulo) }}</h2>
      <p class="post-post-meta">
        <span class="created-at">{{ formattedDate }}</span>
      </p>
      <p>{{ getFirstParagraph(vestido) }}</p>
    </div>
  </nuxt-link>
</template>

<script>
import LinkResolver from "~/plugins/link-resolver.js";

export default {
  props: ["vestido"],
  data: function () {
    return {
      link: "",
      formattedDate: "",
    };
  },
  name: "vestido-widget",
  methods: {
    // Function to get the first paragraph of text in a blog post and limit the displayed text at 300 characters
    getFirstParagraph(vestido) {
      const textLimit = 300;
      const slices = vestido.data.body;
      let firstParagraph = "";
      let haveFirstParagraph = false;

      slices.map(function (slice) {
        if (!haveFirstParagraph && slice.slice_type == "text") {
          slice.primary.text.forEach(function (block) {
            if (block.type == "paragraph" && !haveFirstParagraph) {
              firstParagraph += block.text;
              haveFirstParagraph = true;
            }
          });
        }
      });

      const limitedText = firstParagraph.substr(0, textLimit);

      if (firstParagraph.length > textLimit) {
        return limitedText.substr(0, limitedText.lastIndexOf(" ")) + "...";
      } else {
        return firstParagraph;
      }
    },
  },
  created() {
    (this.link = LinkResolver(this.vestido)),
      (this.formattedDate = Intl.DateTimeFormat("en-US", {
        year: "numeric",
        month: "short",
        day: "2-digit",
      }).format(new Date(this.vestido.data.date)));
  },
};
</script>

<style lang="sass" scoped>
.blog-post
  color: #353535

h2
  margin: 0
</style>
