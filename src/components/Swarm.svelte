<script>
  import { scalePow } from "d3-scale";
  import { format } from "d3-format";
  import { LayerCake, Html, Svg } from "layercake";
  import Nodes from "./Swarm.Nodes.svelte";
  import Axis from "./Swarm.Axis.svelte";
  import Labels from "./Swarm.Labels.svelte";
  import bands from "../data/bands-swarm.csv";
  import members from "../data/members-swarm.csv";

  export let downloadData;
  export let orientation = "horizontal";

  const xScale = scalePow().exponent(0.5);

  const getBandAmount = (name) => {
    const match = bands.find((d) => d.name === name);
    return +match.followers;
  };

  const getDelta = (d) => {
    const comp = getBandAmount(d.band);
    return +d.followers - comp;
  };

  const data = members.map((d, i) => ({
    ...d,
    id: i,
    followers: +d.followers,
    delta: getDelta(d),
  }));

  downloadData = data.map((d) => ({
    name: d.spotifyName,
    band: d.band,
    followers: format(".3s")(d.followers),
    band_followers: format(".3s")(getBandAmount(d.band)),
    follower_difference: format(".3s")(d.delta),
  }));

  $: mobile = orientation === "vertical";
  $: x = mobile ? 2 : 2.5;
  $: aspectRatio = mobile ? 1 / x : x / 1;
  $: low = 0;
  $: high = 100 * x;
  $: xRange = [mobile ? high : low, mobile ? low : high];
  $: rRange = [5, 22];
</script>

<div class="chart-container">
  <figure
    style="padding-bottom: {100 / aspectRatio}%"
    alt="Swarm plot of solo artists previously in groups and their Spotify follower count relative to their group where Beyoncé has the biggest increase"
  >
    <LayerCake
      data="{data}"
      x="delta"
      r="followers"
      xScale="{xScale}"
      xRange="{xRange}"
      rRange="{rRange}"
      position="absolute"
      ssr="{true}"
      percentRange="{true}"
      custom="{{ aspectRatio, mobile }}"
    >
      <Html>
        <Axis />
        <Nodes orientation="{orientation}" />
      </Html>
    </LayerCake>
  </figure>
</div>

<style>
  .chart-container {
    margin: 0 auto;
    max-width: 420px;
  }

  figure {
    position: relative;
    width: 100%;
  }

  @media only screen and (min-width: 1024px) {
    .chart-container {
      max-width: 80%;
    }
  }
</style>
