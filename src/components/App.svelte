<main>
  <div id="tree-container"></div>
  <p> Click on the text to collapse or decollapse the Pokemon Tree! </p>
  <h1>Pokemon Collapsible Tree </h1>
  <p>
    We began the project by data cleaning the Pokemon dataset by dropping unnecessary columns, fixing N/A values, and converting types into a list. The next step began by looking at examples of how to implement our visualization. Our original plan was to implement a radar chart to display how effective different types of Pokemon are against each other. After spending a few hours, we kept running into errors because we couldn’t figure out how to get the visualization to display properly. We decided to shift gears and approach our project by implementing a Zoomable sunburst and changing our goals to exploring different Pokemon types sorted by generation. As we tried to implement this visualization, we stumbled upon the Collapsible Tree visualization which was perfect for the Pokemon dataset. With this visualization, the Pokemon are sorted by generation and then by classification, which allows the users to navigate and see the variety of Pokemon. 
  </p>
  <p>
    The most challenging part of our project design was getting the visualization to load onto the website by connecting all the moving parts such as importing d3. We realized after many trials and errors that our issues lay in the format of our data file. We were using a csv file that was not compatible with the format needed for collapsible tree visualization. When we switched to a JSON file with the data in a hierarchical format, we were able to make significant progress. Another difficulty we faced was correctly importing the data as we intended because there was confusion in figuring out how to use the proper variables in each file. Although this is just a prototype, I anticipate further data cleaning for each Pokemon evolution will be tedious to implement. Once we figure out the finishing touches such as color coding, the visualization will look complete.
  </p>
</main>

<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let svg;

  onMount(() => {
    const width = 928;
    const marginTop = 10;
    const marginRight = 10;
    const marginBottom = 10;
    const marginLeft = 50;

    // Load data from JSON files
    Promise.all([
      d3.json('pokemon3.json'),
      d3.json('tooltipStats.json')
    ]).then(([pokemonData, tooltipData]) => {
      const root = d3.hierarchy(pokemonData);

      const dx = 10;
      const dy = (width - marginRight - marginLeft) / (1 + root.height);

      const tree = d3.tree().nodeSize([dx, dy]);
      const diagonal = d3.linkHorizontal().x(d => d.y).y(d => d.x);

      svg = d3.select('#tree-container')
        .append('svg')
        .attr('width', width)
        .attr('height', dx)
        .attr('viewBox', [-marginLeft, -marginTop, width, dx])
        .attr('style', 'max-width: 110%; height: auto; font: 10px sans-serif; user-select: none;');

      const gLink = svg.append('g')
        .attr('fill', 'none')
        .attr('stroke', '#555')
        .attr('stroke-opacity', 0.4)
        .attr('stroke-width', 1.5);

      const gNode = svg.append('g')
        .attr('cursor', 'pointer')
        .attr('pointer-events', 'all');

      function update(event, source) {
        const duration = event?.altKey ? 2500 : 250; // hold the alt key to slow down the transition
        const nodes = root.descendants().reverse();
        const links = root.links();

        // Compute the new tree layout.
        tree(root);

        let left = root;
        let right = root;
        root.eachBefore(node => {
          if (node.x < left.x) left = node;
          if (node.x > right.x) right = node;
        });

        const height = right.x - left.x + marginTop + marginBottom;

        const transition = svg.transition()
          .duration(duration)
          .attr('height', height)
          .attr('viewBox', [-marginLeft, left.x - marginTop, width, height])
          .tween('resize', window.ResizeObserver ? null : () => () => svg.dispatch('toggle'));

        // Update the nodes…
        const node = gNode.selectAll('g')
          .data(nodes, d => d.id);

        const nodeEnter = node.enter().append('g')
          .attr('transform', d => `translate(${source.y0},${source.x0})`)
          .attr('fill-opacity', 0)
          .attr('stroke-opacity', 0)
          .on('click', (event, d) => {
            d.children = d.children ? null : d._children;
            update(event, d);
          });

        nodeEnter.append('circle')
          .attr('r', 2.5)
          .attr('fill', d => d._children ? '#555' : '#999')
          .attr('stroke-width', 10);

        nodeEnter.append('text')
          .attr('dy', '0.31em')
          .attr('x', d => d._children ? -6 : 6)
          .attr('text-anchor', d => d._children ? 'end' : 'start')
          .text(d => d.data.name)
          .attr('stroke-linejoin', 'round')
          .attr('stroke-width', 3)
          .attr('stroke', 'white')
          .attr('paint-order', 'stroke')
          // Add tooltip functionality
          .on('mouseover', (event, d) => {
            const tooltip = d3.select('body').append('div')
              .attr('class', 'tooltip')
              .style('position', 'absolute')
              .style('background-color', 'white')
              .style('border', 'solid')
              .style('border-width', '1px')
              .style('border-radius', '5px')
              .style('padding', '10px')
              .text(tooltipData[d.data.name]); // Use the tooltip data from the external file

            // Position the tooltip relative to the mouse pointer
            tooltip.style('left', (event.pageX + 10) + 'px')
              .style('top', (event.pageY + 10) + 'px');
          })
          .on('mouseout', () => {
            // Remove the tooltip when mouse is out
            d3.selectAll('.tooltip').remove();
          });

        const nodeUpdate = node.merge(nodeEnter).transition(transition)
          .attr('transform', d => `translate(${d.y},${d.x})`)
          .attr('fill-opacity', 1)
          .attr('stroke-opacity', 1);

        const nodeExit = node.exit().transition(transition).remove()
          .attr('transform', d => `translate(${source.y},${source.x})`)
          .attr('fill-opacity', 0)
          .attr('stroke-opacity', 0);

        const link = gLink.selectAll('path')
          .data(links, d => d.target.id);

        const linkEnter = link.enter().append('path')
          .attr('d', d => {
            const o = { x: source.x0, y: source.y0 };
            return diagonal({ source: o, target: o });
          });

        link.merge(linkEnter).transition(transition)
          .attr('d', diagonal);

        link.exit().transition(transition).remove()
          .attr('d', d => {
            const o = { x: source.x, y: source.y };
            return diagonal({ source: o, target: o });
          });

        root.eachBefore(d => {
          d.x0 = d.x;
          d.y0 = d.y;
        });
      }

      root.x0 = dy / 2;
      root.y0 = 0;
      root.descendants().forEach((d, i) => {
        d.id = i;
        d._children = d.children;
        if (d.depth && d.data.name.length !== 7) d.children = null;
      });

      update(null, root);
    });
  });
</script>

<style>
  /* Write your CSS here */

  p {  
    padding-left: 75px;  
    padding-right: 80px;
  } 

  .tooltip {
    /* Define tooltip styles here */
  }
</style>
