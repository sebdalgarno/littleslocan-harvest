<script>
  export let data;
  export let tips;
</script>

<style>
  .title {
    @apply text-xl pt-4 pb-1;
  }

  .text {
    @apply text-sm text-gray-900;
  } 

  .smalltext {
    @apply text-xs text-gray-900;

  }

</style>

<div class="z-50">
  <p class="title">Tips for exploring the data</p>
  <p class="text">{tips}</p>
  <p class="title">Data</p>
  <p class="smalltext">{data}</p>
  <br>
  <p class="title">Credit</p>
  <p class="smalltext">Data were compiled by Dave Leversee. The visualization was developed by Seb Dalgarno (<a href="https://www.northbeachconsulting.ca" target="_blank">North Beach Consulting</a>). It is a new implementation of John Broadhead's original animation and much of the original design is mimicked. Contact <a href="mailto:seb@northbeachconsulting.ca">Seb Dalgarno</a> for more information.</p>
</div>
