<main>
  <h1>Gotta Catch 'Em All!</h1>
  <div class="author-line">
    <h1>by Tracy Pham and Jenna Canicosa</h1>
  </div>
  <h1 style="font-size: 1.2em; margin-top: 20px;">Pokemon Collapsible Tree</h1>
  <p>
    We began the project by data cleaning the Pokemon dataset by dropping unnecessary columns, fixing N/A values, and converting types into a list. The next step began by looking at examples of how to implement our visualization. Our original plan was to implement a radar chart to display how effective different types of Pokemon are against each other. After spending a few hours, we kept running into errors because we couldn’t figure out how to get the visualization to display properly. We decided to shift gears and approach our project by implementing a Zoomable sunburst and changing our goals to exploring different Pokemon types sorted by generation. As we tried to implement this visualization, we stumbled upon the Collapsible Tree visualization which was perfect for the Pokemon dataset. With this visualization, the Pokemon are sorted by generation and then by classification, which allows the users to navigate and see the variety of Pokemon. 
  </p>
  <p>
    The most challenging part of our project design was getting the visualization to load onto the website by connecting all the moving parts such as importing d3. We realized after many trials and errors that our issues lay in the format of our data file. We were using a csv file that was not compatible with the format needed for collapsible tree visualization. When we switched to a JSON file with the data in a hierarchical format, we were able to make significant progress. Another difficulty we faced was correctly importing the data as we intended because there was confusion in figuring out how to use the proper variables in each file. Although this is just a prototype, I anticipate further data cleaning for each Pokemon evolution will be tedious to implement. Once we figure out the finishing touches such as color coding, the visualization will look complete.
  </p>
  <p> Click on the text to collapse or decollapse the Pokemon Tree! </p>
  
  <div id="bar-chart-container"></div>
  <div id="tree-container"></div>
  <div id="radar-chart-container"></div> 
</main>

<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let svg;

  onMount(() => {
    const width = 1100;
    const marginTop = 30;
    const marginRight = 10;
    const marginBottom = 30;
    const marginLeft = 80;

    // Load data from JSON files
    Promise.all([
      d3.json('pokemon.json'),
      d3.json('tooltipStatsNew.json'),
      d3.json('avgPokemon.json'),
      d3.json('weightPokemon.json'),
      d3.json('pokemonStats.json')
    ]).then(([pokemonData, tooltipData, radarData, weightData, statsData]) => {
    
      const root = d3.hierarchy(pokemonData);

      const dx = 20;
      const dy = ((width - marginRight - marginLeft) / (1 + root.height))+30;

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
        .attr('stroke', '#ffcb05')
        .attr('stroke-opacity', 0.4)
        .attr('stroke-width', 1.8);

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
          .attr('fill', d => d._children ? '#FFFF00' : '#FFFF00')
          .attr('stroke-width', 10);

      nodeEnter.append('text')
        .attr('dy', '0.31em')
        .attr('x', d => d._children ? -6 : 6)
        .attr('text-anchor', d => d._children ? 'end' : 'start')
        .text(d => d.data.name)
        .attr('stroke-linejoin', 'round')
        .attr('stroke-width', 4)
        .attr('stroke', 'black')
        .attr('paint-order', 'stroke')
        .attr('fill', 'white')
        .on('mouseover', (event, d) => {
          if (!d.children) {
            const tooltip = d3.select('body').append('div')
              .attr('class', 'tooltip')
              .style('position', 'absolute')
              .style('background-color', '#ffcb05')
              .style('border', 'none')
              .style('border-width', '1px')
              .style('border-radius', '5px')
              .style('padding', '5px')
              .style('color', 'black')
              .html(`
    Stats: <br>
    Attack - ${tooltipData[d.data.name].attack} <br>
    Defense - ${tooltipData[d.data.name].defense} <br>
    Speed - ${tooltipData[d.data.name].speed} <br>
    HP - ${tooltipData[d.data.name].hp} <br>
    Special Attack - ${tooltipData[d.data.name].sp_attack} <br>
    Special Defense - ${tooltipData[d.data.name].sp_defense}<br>
    Weight (kg) - ${tooltipData[d.data.name].weight_kg} <br>
    Legendary - ${tooltipData[d.data.name].is_legendary}
  `);
    
              // Position the tooltip relative to the mouse pointer
              tooltip.style('left', (event.pageX + 10) + 'px')
                .style('top', (event.pageY + 10) + 'px');
            }
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
    
      const radarChartContainer = d3.select('#radar-chart-container');

      function drawRadarChart(data) {
        // Extract labels and datasets from the provided data
        const labels = data.labels;
        const datasets = data.datasets;

        // Set up the radar chart dimensions
        const width = 400;
        const height = 400;
        const margin = { top: 100, right: 100, bottom: 100, left: 100 };
        const chartWidth = width - margin.left - margin.right;
        const chartHeight = height - margin.top - margin.bottom;
        const radius = Math.min(chartWidth, chartHeight) / 2;

        // Append an SVG element to the radar chart container
        const svg = radarChartContainer
          .append('svg')
          .attr('width', width)
          .attr('height', height)
          .append('g')
          .attr('transform', `translate(${width / 2}, ${height / 2})`);

        // Create a hexagon background
        const hexagonData = [
          { angle: 0, radius: radius },
          { angle: Math.PI / 3, radius: radius },
          { angle: (2 * Math.PI) / 3, radius: radius },
          { angle: Math.PI, radius: radius },
          { angle: (4 * Math.PI) / 3, radius: radius },
          { angle: (5 * Math.PI) / 3, radius: radius },
          { angle: 0, radius: radius } // To close the path
        ];

        // Create the hexagon line generator
        const hexagonLine = d3.lineRadial()
          .angle(d => d.angle)
          .radius(d => d.radius);

        
        // Append the hexagon path
        svg.append('path')
          .datum(hexagonData)
          .attr('class', 'hexagon-background')
          .attr('d', hexagonLine)
          .style('fill', 'none')
          .style('stroke', '#ccc')
          .style('stroke-width', 1)
          .style('stroke-dasharray', '5,5');
          
        // Append grid lines
        const gridData = labels.map((_, i) => {
          const angle = i * (Math.PI * 2 / labels.length);
          return [
            { angle, radius: 0 },
            { angle, radius }
          ];
        });

        const gridLines = svg.selectAll('.grid-line')
          .data(gridData)
          .enter().append('path')
          .attr('class', 'grid-line')
          .attr('d', d => hexagonLine(d))
          .style('fill', 'none')
          .style('stroke', '#ccc')
          .style('stroke-width', 1)
          .style('stroke-dasharray', '2,2');

        // Create the radial scales for the radar chart
        const rScale = d3.scaleLinear()
          .domain([0, 100]) // Assuming data values range from 0 to 100
          .range([0, radius]);

        // Create the angle scales for the radar chart
        const angleScale = d3.scaleLinear()
          .domain([0, labels.length])
          .range([0, Math.PI * 2]);

        // Create a radial line generator
        const radarLine = d3.lineRadial()
          .radius(d => rScale(d))
          .angle((d, i) => angleScale(i));

        // Create a single dataset from the average data
        const avgDataset = datasets[0];

        // Add the first data point to the end for a closed path
        avgDataset.data.push(avgDataset.data[0]);

        // Append the radar area path
        svg.append('path')
          .datum(avgDataset.data)
          .attr('class', 'radar-area')
          .attr('d', radarLine)
          .style('fill', '#ffcb05'); 

        // Append the radar line path
        svg.append('path')
          .datum(avgDataset.data)
          .attr('class', 'radar-line')
          .attr('d', radarLine)
          .style('fill', 'none')
          .style('stroke', 'white') 
          .style('stroke-width', 1);

        // Append the labels around the radar chart
        const label = svg.selectAll('.radar-label')
          .data(labels)
          .enter()
          .append('g')
          .attr('class', 'radar-label');

        // Append the label text
        label.append('text')
          .attr('x', (d, i) => rScale(100) * Math.cos(angleScale(i) - Math.PI / 2))
          .attr('y', (d, i) => rScale(100) * Math.sin(angleScale(i) - Math.PI / 2))
          .attr('text-anchor', 'middle')
          .text(d => d)
          .style('fill', 'white');
        svg.append('text')
          .attr('class', 'radar-title')
          .attr('x', 0)
          .attr('y', -margin.top - 60)
          .attr('text-anchor', 'middle')
          .text('Radar Chart with Pok\u00e9mon\'s Stats') // Set your desired title text here
          .style('fill', 'white');  
        
      }

      drawRadarChart(radarData);
    const heavyPokemon = weightData.filter(pokemon => pokemon.weight_kg > 30);
      const lightPokemon = weightData.filter(pokemon => pokemon.weight_kg <= 30);
      
      // Calculate average stats for heavier and lighter Pokémon
      const averageHeavyStats = calculateAverageStats(heavyPokemon);
      const averageLightStats = calculateAverageStats(lightPokemon);
       drawBarChart(averageHeavyStats, averageLightStats);
       function calculateAverageStats(pokemonData) {
    const stats = ['attack', 'defense', 'speed', 'sp_attack', 'sp_defense'];
    const totalStats = {};
    stats.forEach(stat => {
      totalStats[stat] = pokemonData.reduce((sum, pokemon) => sum + pokemon[stat], 0);
    });
    const count = pokemonData.length;
    const averageStats = {};
    stats.forEach(stat => {
      averageStats[stat] = totalStats[stat] / count;
    });
    return averageStats;
  }
  // Function to draw the bar chart
function drawBarChart(averageHeavyStats, averageLightStats) {
    // Define the data for the bar chart
    const data = [
        { stat: 'Attack', heavy: averageHeavyStats.attack, light: averageLightStats.attack },
        { stat: 'Defense', heavy: averageHeavyStats.defense, light: averageLightStats.defense },
        { stat: 'Speed', heavy: averageHeavyStats.speed, light: averageLightStats.speed },
        { stat: 'Special Attack', heavy: averageHeavyStats.sp_attack, light: averageLightStats.sp_attack },
        { stat: 'Special Defense', heavy: averageHeavyStats.sp_defense, light: averageLightStats.sp_defense },
    ];

    // Set up the dimensions for the bar chart
    const margin = { top: 20, right: 30, bottom: 80, left: 80 }; // Increased bottom margin
    const width = 700 - margin.left - margin.right; // Adjusted width
    const height = 450 - margin.top - margin.bottom; // Adjusted height

// Append an SVG element to the bar chart container
const svg = d3.select('#bar-chart-container')
    .append('svg')
    .attr('width', width + margin.left + margin.right)
    .attr('height', height + margin.top + margin.bottom)
    .append('g')
    .attr('transform', `translate(${margin.left},${margin.top})`);

    // Define scales for x and y axes
    const x = d3.scaleBand()
        .domain(data.map(d => d.stat))
        .range([0, width])
        .padding(0.2);

    const y = d3.scaleLinear()
        .domain([0, d3.max(data, d => Math.max(d.heavy, d.light))])
        .nice()
        .range([height, 0]);

    // Append x axis
    svg.append('g')
        .attr('transform', `translate(0,${height})`)
        .call(d3.axisBottom(x))
        .attr('transform', `translate(0,${height})`)
        .call(d3.axisBottom(x))
        .selectAll('text')
        .style('text-anchor', 'end')
        .attr('dx', '-0.8em')
        .attr('dy', '0.15em')
        .attr('transform', 'rotate(-65)');

    // Append y axis
    svg.append('g')
        .call(d3.axisLeft(y));

    // Append bars for heavier Pokémon
    svg.selectAll('.bar-heavy')
        .data(data)
        .enter().append('rect')
        .attr('class', 'bar-heavy')
        .attr('x', d => x(d.stat))
        .attr('y', d => y(d.heavy))
        .attr('width', x.bandwidth() / 2)
        .attr('height', d => height - y(d.heavy))
        .attr('fill', '#c7a008');

    // Append bars for lighter Pokémon
    svg.selectAll('.bar-light')
        .data(data)
        .enter().append('rect')
        .attr('class', 'bar-light')
        .attr('x', d => x(d.stat) + x.bandwidth() / 2)
        .attr('y', d => y(d.light))
        .attr('width', x.bandwidth() / 2)
        .attr('height', d => height - y(d.light))
        .attr('fill', '#E2DC69');

    // Add legend
    const legend = svg.append('g')
        .attr('class', 'legend')
        .attr('transform', `translate(${width - 60},${margin.top-30})`);

    legend.append('rect')
        .attr('x', 0)
        .attr('y', 0)
        .attr('width', 15)
        .attr('height', 15)
        .attr('fill', '#c7a008');

    legend.append('text')
        .attr('x', 20)
        .attr('y', 9)
        .text('Heavier');

    legend.append('rect')
        .attr('x', 0)
        .attr('y', 20)
        .attr('width', 15)
        .attr('height', 15)
        .attr('fill', '#E2DC69');

    legend.append('text')
        .attr('x', 20)
        .attr('y', 29)
        .text('Lighter');
}

  
    });
  });
</script>

<style>
  /* Write your CSS here */
  p {  
    padding-left: 75px;  
    padding-right: 40px;
  } 

  .tooltip {
    /* Define tooltip styles here */
  }

  .hexagon-background {
    stroke-dasharray: 5,5;
  }

  .grid-line {
    stroke-dasharray: 2,4;
  }

</style>
