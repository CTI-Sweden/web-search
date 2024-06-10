<script lang="ts">
  import { onMount } from 'svelte';
  import * as d3 from "d3";

  import { Input } from "$lib/components/ui/input";
  import { Button } from "$lib/components/ui/button";


  import axios from 'axios';
  import { graphResultsStore } from "/src/stores/graphResultsStore.ts";

  import { env } from '$env/dynamic/public';

  let searchText = "";
  let timeout;

  function keyPressed() {
    if (timeout) clearTimeout(timeout);
    timeout = setTimeout(() => {
        fetchSearchResults();
      }, 600)
  }

  async function fetchSearchResults() {
    try {
      let {data, status} = await axios.post(env.PUBLIC_CTI_API_HOST + '/search', {
        text: searchText
      });
      if (status != 200) {
        alert("Error fetching search results!");
        return;
      }
      console.log(data.results)
      graphResultsStore.set(data.results);
    } catch(error) {
      console.log("Error fetching search results! Probably the API is not ready yet.");
      return;
    }
  }










  let canvas: HTMLCanvasElement;
  let context;
  let links: Link[];
  let nodes: Node[];
  let simulation;
  const width = 928;
  const height = 600;
  const nodeRadius = 8;
  const colour = d3.scaleOrdinal<number, string>(d3.schemeCategory10);

  type Node = {
    id: string;
    group: number;
    x?: number;
    y?: number;
    fx?: number | null;
    fy?: number | null;
  }
  type Link = {
    source: Node | string;
    target: Node | string;
  };

  type Data = {
    nodes: Node[];
    links: Link[];
  };

  function draw(): void {
    console.log("draw");
    context.save();
    context.clearRect(0, 0, width, height);
    // context.translate(transform.x, transform.y);
    // context.scale(transform.k, transform.k);

    links.forEach(drawLink);
    nodes.forEach(drawNode);

    context.restore();
  }

  function drawLink(d: Link): void {
    if (typeof d.source !== 'string' && typeof d.target !== 'string') {
      context.beginPath();
      context.moveTo(d.source.x!, d.source.y!);
      context.lineTo(d.target.x!, d.target.y!);
      context.globalAlpha = 0.6;
      context.strokeStyle = "#999";
      context.lineWidth = Math.sqrt(d.value!);
      //context.lineWidth = 10;
      context.stroke();
      context.globalAlpha = 1;
    }
  }

  function drawNode(d: Node): void {
    context.beginPath();
    context.moveTo(d.x! + 5, d.y!);
    context.arc(d.x, d.y, nodeRadius, 0, 2 * Math.PI);
    context.strokeStyle = "#fff";
    context.linewidth = 1.5;
    context.stroke();
    context.fillStyle = colour(d.group);
    context.fill();
  }


  function dragstarted(event: d3.D3DragEvent<HTMLCanvasElement, Node, Node>): void {
    if (!event.active) simulation.alphaTarget(0.3).restart();
    event.subject.fx = event.subject.x;
    event.subject.fy = event.subject.y;
  }

  function dragged(event: d3.D3DragEvent<HTMLCanvasElement, Node, Node>): void {
    event.subject.fx = event.x;
    event.subject.fy = event.y;
  }

  function dragended(event: d3.D3DragEvent<HTMLCanvasElement, Node, Node>): void {
    if (!event.active) simulation.alphaTarget(0);
    event.subject.fx = null;
    event.subject.fy = null;
  }

  let data = {
    nodes: [
      { id: 'A', group: 1 },
      { id: 'B', group: 1 },
      { id: 'C', group: 2 },
      { id: 'D', group: 2 }
    ],
    links: [
      { source: 'A', target: 'B' },
      { source: 'A', target: 'C' },
      { source: 'B', target: 'D' }
    ]
  };


  function generateGraph(value): void {
    simulation.stop();
    data.links = [];
    data.nodes = [];
    if (value.length == 0) {
      console.log("No data");
      links = data.links.map(d => ({ ...d }));
      nodes = data.nodes.map(d => ({ ...d }));
      simulation.nodes(nodes);
      simulation.alpha(0.3).restart();
      return
    }
    let industires = new Set<string>();
    let authors = new Set<string>();
    let countries = new Set<string>();
    let cve = new Set<string>();
    let organizations = new Set<string>();
    let sectors = new Set<string>();
    let tactics = new Set<string>();
    let tags = new Set<string>();
    let technologies = new Set<string>();
    let type = new Set<string>();
    value.forEach((result) => {
      try {
        console.log("generateGraph", result)
        data.nodes.push({id: result.link, group: 1, x: width/2, y: height/2});
        result.affected_industries?.forEach((e) => {industires.add(e); data.links.push({source: result.link, target: e})});
        // result.authors?.forEach((e) => {authors.add(e); data.links.push({source: result.link, target: e})});
        result.countries?.forEach((e) => {countries.add(e); data.links.push({source: result.link, target: e})});
        result.cve?.forEach((e) => {cve.add(e); data.links.push({source: result.link, target: e})});
        result.organizations.forEach((e) => {organizations.add(e); data.links.push({source: result.link, target: e})});
        result.sector.forEach((e) => {sectors.add(e); data.links.push({source: result.link, target: e})});
        result.tactics.forEach((e) => {tactics.add(e); data.links.push({source: result.link, target: e})});
        result.tags.forEach((e) => {tags.add(e); data.links.push({source: result.link, target: e})});
        result.technologies.forEach((e) => {technologies.add(e); data.links.push({source: result.link, target: e})});
        type.add(result.type);
        data.links.push({source: result.link, target: result.type});
      } catch(e) {
        console.log((e as Error).message);
      }
    })
    industires.forEach((e) => {data.nodes.push({id: e, group: 2, x: width/2, y: height/2})});
    authors.forEach((e) => {data.nodes.push({id: e, group: 3, x: width/2, y: height/2})});
    countries.forEach((e) => {data.nodes.push({id: e, group: 4, x: width/2, y: height/2})});
    cve.forEach((e) => {data.nodes.push({id: e, group: 5, x: width/2, y: height/2})});
    organizations.forEach((e) => {data.nodes.push({id: e, group: 6, x: width/2, y: height/2})});
    sectors.forEach((e) => {data.nodes.push({id: e, group: 7, x: width/2, y: height/2})});
    tactics.forEach((e) => {data.nodes.push({id: e, group: 8, x: width/2, y: height/2})});
    tags.forEach((e) => {data.nodes.push({id: e, group: 9, x: width/2, y: height/2})});
    technologies.forEach((e) => {data.nodes.push({id: e, group: 10, x: width/2, y: height/2})});
    type.forEach((e) => {data.nodes.push({id: e, group: 11, x: width/2, y: height/2})});
    console.log("data", data);
    links = data.links.map(d => ({ ...d }));
    nodes = data.nodes.map(d => ({ ...d }));
    simulation.nodes(nodes)
      .force("link", d3.forceLink<Node, Link>(links).id((d: Node) => d.id));
    simulation.alpha(0.3).restart();
  };


  // Create the chart and append it to the document body.
  onMount(() => {
    const dpi = (typeof window !== 'undefined' && window.devicePixelRatio) || 1;
    links = data.links.map(d => ({ ...d }));
    nodes = data.nodes.map(d => ({ ...d }));

    simulation = d3.forceSimulation(nodes)
      .force("link", d3.forceLink<Node, Link>(links).id((d: Node) => d.id))
      .force("charge", d3.forceManyBody().strength(-200))
      // .force("center", d3.forceCenter(width / 2, height / 2))
      //.force("x", d3.forceX(width / 2))
      //.force("y", d3.forceY(height / 2))
      .force("x", d3.forceX(width / 2))
      .force("y", d3.forceY(height / 2))
      .on("tick", draw);

    context = canvas.getContext("2d")!;
    context.scale(dpi, dpi);

    d3.select(canvas)
      .call(d3.drag<HTMLCanvasElement, Node>()
        .subject((event: any) => {
          const [px, py] = d3.pointer(event, canvas);
          return d3.least(nodes, ({ x, y }) => {
            const dist2 = (x! - px) ** 2 + (y! - py) ** 2;
            if (dist2 < 400) return dist2;
          });
        })
        .on("start", dragstarted)
        .on("drag", dragged)
        .on("end", dragended));

    // Create a promise to handle invalidation.
    const invalidation = new Promise<void>((resolve) => {
      // For example, resolve the promise after 10 seconds to stop the simulation.
      setTimeout(() => resolve(), 10000);
    });
    invalidation.then(() => simulation.stop());

    graphResultsStore.subscribe((value) => {
      generateGraph(value);
    })
  });

</script> 

 
<div class="flex">
  <Input bind:value={searchText}  on:keydown={()=> keyPressed()}  />
  <Button variant="outline">Button</Button>
</div>
<canvas 
  bind:this={canvas}
  width={width}
  height={height}
  style="width: {width}px; height: {height}px;" />

<style>
  canvas {
    width: 100%;
    height: 100%;
    border:1px solid #000000;
  }
</style>

