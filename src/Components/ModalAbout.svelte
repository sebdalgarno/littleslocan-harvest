<script>
  import { getContext } from "svelte";
  import About from "./About.svelte";
  import Button from "./Button.svelte";
  import {data, tips, tips2 } from "../about";

  const { open } = getContext("simple-modal");
  //   export let message;

  open(About, {
      tips: tips,
      data: data},
      {styleWindow: {'z-index': 100}}
    );

  const showAbout = () => {
    open(About, {
      tips: tips,
      data: data,
    tips2: tips2},
      {styleWindow: {'z-index': 100}}
    );
  };
</script>


<Button caption={'About'} outline={false} on:open-modal={showAbout} />
