<script lang="ts">
  import { onMount } from "svelte";

  let showNextNode = $state(true);
  let networkContainer: HTMLElement | null = $state(null);

  function update() {
    if (networkContainer) {
      const connectorsContainer =
        networkContainer.querySelector(".connectors")!;
      const svgRect = connectorsContainer.getBoundingClientRect();

      connectorsContainer.querySelector("#line")?.remove();

      if (showNextNode) {
        // line
        const svgLine = document.createElementNS(
          "http://www.w3.org/2000/svg",
          "line",
        );
        svgLine.setAttribute("id", `line`);
        svgLine.setAttribute("stroke", "gray");
        svgLine.setAttribute("marker-end", "url(#arrow)");
        connectorsContainer.appendChild(svgLine);

        // nodes
        const n1 = document.getElementById("n1")!;
        const n2 = document.getElementById("n2")!;

        const n1Rect = n1.getBoundingClientRect();
        const n2Rect = n2.getBoundingClientRect();

        // Calculate center points of nodes
        const x1 = n1Rect.left - svgRect.left + n1Rect.width / 2;
        const y1 = n1Rect.top - svgRect.top + n1Rect.height / 2;
        const x2 = n2Rect.left - svgRect.left + n2Rect.width / 2;
        const y2 = n2Rect.top - svgRect.top + n2Rect.height / 2;

        svgLine.setAttribute("x1", x1.toString());
        svgLine.setAttribute("y1", y1.toString());
        svgLine.setAttribute("x2", x2.toString());
        svgLine.setAttribute("y2", y2.toString());
      } else {
      }
    }
  }

  $effect(() => {
    update();
  });

  onMount(() => {
    addEventListener("resize", update);
    addEventListener("scroll", update);

    return () => {
      removeEventListener("resize", update);
      removeEventListener("scroll", update);
    };
  });
</script>

<div class="network" bind:this={networkContainer}>
  <svg class="connectors">
    <defs>
      <marker
        id="arrow"
        viewBox="0 0 10 10"
        refX="10"
        refY="5"
        markerWidth="10"
        markerHeight="10"
        orient="auto-start-reverse"
        fill="gray"
      >
        <path d="M 0 0 L 10 5 L 0 10 z" />
      </marker>
    </defs>
  </svg>
  <div class="node">
    <div class="content">
      <div>
        <p>Anterface – the Artificial Neural Network Interface</p>
      </div>
    </div>
    <div class="connector bottom">
      <button
        id="n1"
        type="button"
        onpointerdown={() => (showNextNode = !showNextNode)}
        >{#if showNextNode}-{:else}+{/if}</button
      >
    </div>
  </div>
  {#if showNextNode}
    <div class="node">
      <div class="connector top" id="n2"></div>
      <div>
        <div class="content">
          <div>
            <p>🚧 Coming soon...</p>
          </div>
        </div>
      </div>
    </div>
  {/if}
</div>

<style>
  .network {
    display: flex;
    flex-direction: column;
    position: relative;
    gap: 4ch;

    .connectors {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: -1;
    }

    .node {
      position: relative;
      .content {
        display: flex;
        justify-content: center;

        & > * {
          border: 1px solid gray;
          padding: 1ch;
          border-radius: 1ch;
          width: fit-content;
        }
      }
      .connector {
        text-align: center;
        transform: translateY(-50%);

        button {
          border-radius: 50%;
          aspect-ratio: 1 / 1;
          border: 1px solid gray;
          background-color: var(--color-bg);
          font-family: monospace;
        }
      }
    }
  }
</style>
