<script>
  export let data;
  export let tips;
  export let tips2;
</script>

<style>
  .title {
    @apply text-xl pt-4 pb-1;
  }

  .subtitle {
    @apply text-base pt-4 pb-1;
  }

  .text {
    @apply text-sm text-gray-900;
  } 

  .smalltext {
    @apply text-xs text-gray-900;

  }

</style>

<div class="z-50">
  <p class="title">Welcome!</p>
  <p class="title">Tips for exploring the data</p>
  <p class="text">{tips}</p>
  <br>
  <p class="text">{tips2}</p>
  <p class="subtitle">Data sources</p>
  <p class="smalltext">{data}</p>
  <p class="subtitle">Credit</p>
  <p class="smalltext">This project was organized by concerned residents of the Little Slocan Watershed: Herb Hammond, Jennifer Yeow, and Kate Mizenka. Funding for this project provided by Sierra Club BC. Data were compiled by Dave Leversee. The visualization was developed by Seb Dalgarno (<a href="https://www.northbeachconsulting.ca" target="_blank">North Beach Consulting</a>). The idea and design are inspired by John Broadhead's animation of Haida Gwaii logging history. Contact <a href="mailto:seb@northbeachconsulting.ca">Seb Dalgarno</a> for more information.</p>
</div>
