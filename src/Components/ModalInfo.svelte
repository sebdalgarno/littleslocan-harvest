<script>
  import { getContext } from "svelte";
  import MoreInfo from "./MoreInfo.svelte";
  import Button from "./Button.svelte";

  const { open } = getContext("simple-modal");
  //   export let message;

  open(MoreInfo, {},
      {styleWindow: {'z-index': 100}}
    );

  const showMoreInfo = () => {
    open(MoreInfo, {},
      {styleWindow: {'z-index': 100}}
    );
  };
</script>


<Button caption={'More Info'} outline={false} on:open-modal={showMoreInfo} />
