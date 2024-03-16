<h1>Gotta Catch 'Em All!</h1>
<div class="author-line">
  <h1>by Tracy Pham and Jenna Canicosa</h1>
</div>

<main style="margin-bottom: 100px;">
  <h1 style="font-size: 1.2em; margin-top: 20px;">Welcome to the Pokémon universe!</h1>
  <div style="border: none; background-color: #3c5aa6; border-radius: 10px; text-align: center; padding: 20px;">
  <p style="margin: 0;">
  Once upon a time, in the vibrant world of Pokémon, trainers embarked on an exhilarating journey to unravel the secrets behind what truly makes a Pokémon powerful. Through this website, we will delve deep into the captivating world of Pokémon to uncover what truly makes a Pokémon strong. Our endeavor is tailored for those eager to expand their knowledge about Pokémon and discover how statistics and strategic thinking can shape effective gameplay strategies. Whether you're a seasoned enthusiast of the mainline games, a dedicated Pokémon GO player, or simply curious about the intricacies of Pokémon combat mechanics, you're in the right place. Join us as we embark on a journey to answer the age-old question: what makes a Pokémon strong?
  </p>
</div>

  

  <h1 style="font-size: 1.2em; margin-top: 20px;text-align: center;">How does weight affect stats?</h1>
  <p style = "border: none; background-color: #3c5aa6; border-radius:10px;">
In their quest for knowledge, the trainers stumbled upon a perplexing question: does a Pokémon's weight determine its strength? To answer this, they delved into the depths of data, comparing the average strengths of hefty Pokémon weighing more than 65 kg against their lighter counterparts. Given that the median Pokémon weight stands at 27 kg, while the average hovers around 61 kg, it's evident that the presence of heavier Pokémon skews the average. Hence, to provide a more accurate depiction, we've chosen a weight threshold of 65 kg, which represents the 75th percentile of Pokémon weights, to determine what is considered a heavy Pokémon. As they sifted through the statistics, they uncovered a surprising truth - weight alone did not dictate power. Instead, it was the Pokémon's innate abilities and training that truly defined its strength. 
    
    </p>
  <div id="bar-chart-container"></div>
  <h1 style="font-size: 1.2em; margin-top: 20px;text-align: center;">Are Legendary Pokémon actually stronger?</h1>
  <p style = "border: none; background-color: #3c5aa6; border-radius:10px;">
But their exploration didn't stop there. Legendary Pokémon, mysterious and revered for their epic tales and immense power, hold a pivotal role in the Pokémon universe. They are not merely creatures; they are the very essence of the game's storyline, steeped in lore and mythology. Whether worshiped, revered as guardians, or viewed as symbols of power and balance, their presence is undeniable.

With this in mind, the trainers embarked on a daring quest to unravel the lure surrounding legendary Pokémon. How much stronger could they truly be compared to their non-legendary counterparts? Through meticulous analysis, the trainers sought to shed light on this age-old question. Each data point examined revealed the intricate nuances of power, leading to a profound discovery: strength transcends mere rarity.
  </p>
  <div id="legendary-bar-chart-container"></div>

  <h1 style="font-size: 1.2em; margin-top: 20px;text-align: center;">Pokemon Collapsible Tree </h1>
  <p style = "border: none; background-color: #3c5aa6; border-radius:10px;">
   Pokémon were released in generations based on the main series of the Pokémon video games. Each generation introduced new Pokémon species, regions to explore, gameplay mechanics, and often advances in the Pokémon storyline. There are 18 different elemental types of Pokémon each with their own strengths, weaknesses, and resistances. Knowing the type of each Pokémon can be crucial for success in battles and general strategy. The visualization below allows users to explore different kinds of Pokémon sorted by generation and type.   
  </p>
<div id="tree-container"></div>
 <h1 style="font-size: 1.2em; margin-top: 20px;text-align: center;">Pokémon Radar Chart</h1>
 <p style = "border: none; background-color: #3c5aa6; border-radius:10px;">
Let's dive into the statistics of each Pokémon, examining their attack, defense, HP, special attack, and special defense. While we've already established that factors like legendary status and weight do have a significance on strength, our next step involves delving into the intricate details of each Pokémon's abilities.
    <br>
    To facilitate this analysis, we'll utilize a radar chart, which offers a comprehensive view of a Pokémon's stats across these key attributes. Unlike simplistic metrics such as weight or rarity, this approach allows us to compare the actual combat capabilities of Pokémon relative to one another.
    <br>
    By plotting each Pokémon's stats on the radar chart, we can discern nuanced differences in their offensive and defensive capabilities, stamina, and special prowess. Through this method, we aim to uncover patterns and insights that transcend surface-level characteristics, providing a deeper understanding of what truly makes a Pokémon formidable in battle.
If you want to see an interesting comparison, type in “Flareon, Vaporeon”. The two Pokemon may have similar defense and speed stats but you can see that Flareon triumphs when it comes to hp. Depending on what you’re looking for when it comes to selecting your most important stats, this radar chart may help you find your ideal Pokemon! Hover over the radar chart to explore the stats of each pokemon. Type in a max of 3 Pokemon to explore how they compare. “__, ___, __”
</p>

  <div id="radar-chart-container"></div>
  <p style = "border: none; background-color: #3c5aa6; border-radius:10px;">Have a specific Pokémon in mind? Utilize the search bar below to learn more about that Pokémon! 
</p>
  <input type="text" id="pokemon-search" placeholder="Search Pokémon">

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
    const marginLeft = 90;

    // Load data from JSON files
    Promise.all([
      d3.json('pokemon.json'),
      d3.json('tooltipStatsNew.json'),
      d3.json('tooltipStatsNew_copy.json'),
      d3.json('pokemonStats.json'),
      d3.json('weightPokemon.json')
    ]).then(([pokemonData, tooltipData, tooltipCopy, radarData, weightData]) => {
    console.log(radarData);
      const root = d3.hierarchy(pokemonData);
    
      const dx = 35;
      const dy = ((width - marginRight - marginLeft) / (1 + root.height))+10;

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
    const width = 600;
    const height = 800;
    const margin = { top: 400, right: 400, bottom: 2000, left: 400 };
    const chartWidth = 350 - 200;
    const chartHeight = 500 -200;
    const radius = Math.min(chartWidth, chartHeight) / 2;

    // Remove any existing SVG elements within the container
    radarChartContainer.selectAll('svg').remove();

    // Append an SVG element to the radar chart container
    const svg = radarChartContainer
        .append('svg')
        .attr('width', width)
        .attr('height', height)
        .append('g')
        .attr('transform', `translate(${width / 2 +6}, ${height / 2 +30})`);


  // Define an array of colors for the layers
  const colorScale = d3.scaleOrdinal(d3.schemeCategory10);

  // Create a hexagon background
  const hexagonData = [
    { angle: 0, radius: radius+115},
    { angle: Math.PI / 3, radius: radius+115},
    { angle: (2 * Math.PI) / 3, radius: radius+115 },
    { angle: Math.PI, radius: radius+115 },
    { angle: (4 * Math.PI) / 3, radius: radius+115 },
    { angle: (5 * Math.PI) / 3, radius: radius+115 },
    { angle: 0, radius: radius+115 } // To close the path
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


  // Create the radial scales for the radar chart
  const rScale = d3.scaleLinear()
    .domain([0, 100]) // Assuming data values range from 0 to 100
    .range([0, radius]);

  // Create the angle scales for the radar chart
  const angleScale = d3.scaleLinear()
    .domain([0, 6])
    .range([0, Math.PI * 2]);

  // Create a radial line generator
  const radarLine = d3.lineRadial()
    .radius(d => rScale(d))
    .angle((d, i) => angleScale(i));

    // Append radar area and line paths for each dataset
  datasets.forEach((dataset, i) => {
    // Add the first data point to the end for a closed path
    dataset.data.push(dataset.data[0]);

    // Append the radar area path
    svg.append('path')
      .datum(dataset.data)
      .attr('class', 'radar-area')
      .attr('d', radarLine)
      .style('fill', colorScale(i));

    // Append the radar line path
    svg.append('path')
      .datum(dataset.data)
      .attr('class', 'radar-line')
      .attr('d', radarLine)
      .style('fill', 'none')
      .style('stroke', colorScale(i))
      .style('stroke-width', 1);
  });

  // Append the labels around the radar chart
  const label = svg.selectAll('.radar-label')
    .data(labels)
    .enter()
    .append('g')
    .attr('class', 'radar-label');

  // Append the label text
  label.append('text')
    .attr('x', (d, i) => (radius + 120) * Math.cos(angleScale(i) - Math.PI / 2)) // Adjusted x position
    .attr('y', (d, i) => (radius + 120) * Math.sin(angleScale(i) - Math.PI / 2)) // Adjusted y position
    .attr('text-anchor', 'middle')
    .text(d => d)
    .style('fill', 'white');
  svg.append('text')
    .attr('class', 'radar-title')
    .attr('x', 0)
    .attr('y', -margin.top + 160)
    .attr('text-anchor', 'middle')
    .text('Radar Chart with Pokémon\'s Stats') // Set your desired title text here
    .style('fill', 'white');
  const gridLines = svg.selectAll('.grid-line')
  .data(d3.range(80, 300, 58)) // Adjust range and step according to your preference
  .enter()
  .append('g')
  .attr('class', 'grid-line');

gridLines.append('path')
  .attr('class', 'grid-line-path')
  .attr('d', d => {
    const gridLineData = [
      { angle: 0, radius: rScale(d) },
      { angle: Math.PI / 3, radius: rScale(d) },
      { angle: (2 * Math.PI) / 3, radius: rScale(d) },
      { angle: Math.PI, radius: rScale(d) },
      { angle: (4 * Math.PI) / 3, radius: rScale(d) },
      { angle: (5 * Math.PI) / 3, radius: rScale(d) },
      { angle: 0, radius: rScale(d) } // To close the path
    ];
    return hexagonLine(gridLineData);
  })
  .style('fill', 'none')
  .style('stroke', '#ccc')
  .style('stroke-width', 1)
  .style('stroke-dasharray', '3,3');

  gridLines.append('text')
  .attr('class', 'grid-line-text')
  .attr('x', d => rScale(d) * Math.cos(Math.PI / 6)) // Adjusted x position
  .attr('y', d => rScale(d) * Math.sin(Math.PI )) // Adjusted y position
  .attr('dy', 5) // Adjust vertical alignment
  .text(d => d) // Display the value of the grid line
  .style('fill', 'yellow')
  .style('font-size', '12px')
  .style('text-anchor', 'middle');
}

function drawFilteredChart(filteredData) {
    // Set up the radar chart dimensions
    const width = 600;
    const height = 800;
    const margin = { top: 400, right: 400, bottom: 2000, left: 400 };
    const chartWidth = 350 - 200;
    const chartHeight = 500 - 200;
    const radius = Math.min(chartWidth, chartHeight) / 2;

    // Remove any existing SVG elements within the container
    radarChartContainer.selectAll('svg').remove();

    // Append an SVG element to the radar chart container
    const svg = radarChartContainer
        .append('svg')
        .attr('width', width)
        .attr('height', height)
        .append('g')
        .attr('transform', `translate(${width / 2 + 6}, ${height / 2 + 30})`);

    // Define an array of colors for the layers
    const colorScale = d3.scaleOrdinal(d3.schemeCategory10);

    // Create a hexagon background
    const hexagonData = [
        { angle: 0, radius: radius + 115 },
        { angle: Math.PI / 3, radius: radius + 115 },
        { angle: (2 * Math.PI) / 3, radius: radius + 115 },
        { angle: Math.PI, radius: radius + 115 },
        { angle: (4 * Math.PI) / 3, radius: radius + 115 },
        { angle: (5 * Math.PI) / 3, radius: radius + 115 },
        { angle: 0, radius: radius + 115 } // To close the path
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

    // Create the radial scales for the radar chart
    const rScale = d3.scaleLinear()
        .domain([0, 100]) // Assuming data values range from 0 to 100
        .range([0, radius]);

    // Create the angle scales for the radar chart
    const angleScale = d3.scaleLinear()
        .domain([0, 6])
        .range([0, Math.PI * 2]);

    // Create a radial line generator
    const radarLine = d3.lineRadial()
        .radius(d => rScale(d))
        .angle((d, i) => angleScale(i));

    // Append the radar area path for each filtered dataset
    filteredData.forEach((data, index) => {
        svg.append('path')
            .datum(data.data)
            .attr('class', 'radar-area')
            .attr('d', radarLine)
            .style('fill', 'none')
            .style('stroke', colorScale(index));
    });

    // Append the radar line path for each filtered dataset
    filteredData.forEach((data, index) => {
        svg.append('path')
            .datum(data.data)
            .attr('class', 'radar-line')
            .attr('d', radarLine)
            .style('fill', 'none')
            .style('stroke', colorScale(index)) // Use color for each dataset
            .style('stroke-width', 4);
    });

    // Append the labels around the radar chart
    const label = svg.selectAll('.radar-label')
        .data(["attack", "defense", "speed", "hp", "sp_attack", "sp_defense"])
        .enter()
        .append('g')
        .attr('class', 'radar-label');

    // Append the label text
    label.append('text')
        .attr('x', (d, i) => (radius + 120) * Math.cos(angleScale(i) - Math.PI / 2)) // Adjusted x position
        .attr('y', (d, i) => (radius + 120) * Math.sin(angleScale(i) - Math.PI / 2)) // Adjusted y position
        .attr('text-anchor', 'middle')
        .text(d => d)
        .style('fill', 'white');
    svg.append('text')
        .attr('class', 'radar-title')
        .attr('x', 0)
        .attr('y', -margin.top + 160)
        .attr('text-anchor', 'middle')
        .text('Radar Chart with Pokémon\'s Stats') // Set your desired title text here
        .style('fill', 'white');

    const gridLines = svg.selectAll('.grid-line')
        .data(d3.range(80, 300, 58)) // Adjust range and step according to your preference
        .enter()
        .append('g')
        .attr('class', 'grid-line');

    gridLines.append('path')
        .attr('class', 'grid-line-path')
        .attr('d', d => {
            const gridLineData = [
                { angle: 0, radius: rScale(d) },
                { angle: Math.PI / 3, radius: rScale(d) },
                { angle: (2 * Math.PI) / 3, radius: rScale(d) },
                { angle: Math.PI, radius: rScale(d) },
                { angle: (4 * Math.PI) / 3, radius: rScale(d) },
                { angle: (5 * Math.PI) / 3, radius: rScale(d) },
                { angle: 0, radius: rScale(d) } // To close the path
            ];
            return hexagonLine(gridLineData);
        })
        .style('fill', 'none')
        .style('stroke', '#ccc')
        .style('stroke-width', 1)
        .style('stroke-dasharray', '3,3');

    gridLines.append('text')
        .attr('class', 'grid-line-text')
        .attr('x', d => rScale(d) * Math.cos(Math.PI / 6)) // Adjusted x position
        .attr('y', d => rScale(d) * Math.sin(Math.PI)) // Adjusted y position
        .attr('dy', 5) // Adjust vertical alignment
        .text(d => d) // Display the value of the grid line
        .style('fill', 'yellow')
        .style('font-size', '12px')
        .style('text-anchor', 'middle');
     const tooltip = d3.select('#radar-chart-container')
        .append('div')
        .attr('class', 'tooltip')
        .style('opacity', 0);

    // Add event listeners to radar areas to show/hide tooltip
    svg.selectAll('.radar-line')
        .on('mouseover', function(event, d) {
            const hoveredData = d; // Assuming d contains the stats for the hovered data point
            let tooltipContent = '';

            filteredData.forEach((data, index) => {
                const pokemonName = data.label; // Assuming each dataset contains the Pokémon name
                const stats = data.data; // Assuming each dataset contains the stats
                const color = colorScale(index); // Get the color for the Pokémon
                console.log(stats)
                // Add color swatch and Pokémon name
                tooltipContent += `<div style="display: flex; align-items: center;">`;
                tooltipContent += `<div style="width: 10px; height: 10px; background-color: ${color}; margin-right: 5px;"></div>`;
                tooltipContent += `<strong>${pokemonName}</strong>`;
                tooltipContent += `</div>`;
              
                // Add Pokémon stats
                tooltipContent += `Stats:<br>`;
                tooltipContent += `Attack: ${stats[0]}<br>`;
                tooltipContent += `Defense: ${stats[1]}<br>`;
                tooltipContent += `Speed: ${stats[2]}<br>`;
                tooltipContent += `HP: ${stats[3]}<br>`;
                tooltipContent += `Special Attack: ${stats[4]}<br>`;
                tooltipContent += `Special Defense: ${stats[5]}<br><br>`;
            });

            // Show tooltip
            tooltip.transition()
                .duration(100)
                .style('opacity', .9);
            tooltip.html(tooltipContent)
                .style('left', (event.pageX) + 'px')
                .style('top', (event.pageY) + 'px');
        })
        .on('mouseout', function(d) {
            // Hide tooltip
            tooltip.transition()
                .duration(500)
                .style('opacity', 0);
        });
}
    drawRadarChart(radarData);
const pokemonSearchInput = document.getElementById('pokemon-search');
pokemonSearchInput.addEventListener('input', function () {
  // Get the input value
  const searchValue = pokemonSearchInput.value.toLowerCase();

  // Split the input value by comma to handle two inputs
  const searchValues = searchValue.split(',');

  // Filter radar data based on the input values
  const filteredData = radarData.datasets.filter(dataset =>
    searchValues.some(value => dataset.label.toLowerCase().includes(value.trim()))
  );
  console.log(filteredData);

  // If no matching Pokémon found, clear the radar chart
  if (filteredData.length == 0) {
    radarChartContainer.selectAll('*').remove();
    return;
  }

  // Update the radar chart with the data of the matching Pokémon
  drawFilteredChart(filteredData);
});

    const heavyPokemon = weightData.filter(pokemon => pokemon.weight_kg > 65);
      const lightPokemon = weightData.filter(pokemon => pokemon.weight_kg <= 65);
      
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
    const width = 800 - margin.left - margin.right; // Adjusted width
    const height = 500 - margin.top - margin.bottom; // Adjusted height

// Append an SVG element to the bar chart container
const svg = d3.select('#bar-chart-container')
    .append('svg')
    .attr('width', width + margin.left + margin.right)
    .attr('height', height + margin.top + margin.bottom)
    .append('g')
    .attr('transform', `translate(${margin.left},${margin.top})`);


// Define tooltip
const tooltip = d3.select('#bar-chart-container')
    .append('div')
    .style('opacity', 0)
    .attr('class', 'tooltip')
    .style('position', 'absolute')
    .style('border', 'solid')
    .style('border-width', '1px')
    .style('border-radius', '5px')
    .style('padding', '10px');

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
    .attr('fill', '#c7a008')
    .attr('stroke', 'white')
    // Add tooltip on hover
    .on('mouseover', function(event, d) {
        // Show tooltip on hover
        tooltip.transition()
            .duration(200)
            .style('opacity', .9)
            .style('background-color', '#c7a008')
        tooltip.html(`<strong>${d.stat}:</strong> ${d.heavy}`)
            .style('left', (event.pageX+10) + 'px')
            .style('top', (event.pageY-30) + 'px');
    })
    .on('mouseout', function(d) {
        // Hide tooltip on mouseout
        tooltip.transition()
            .duration(500)
    });

// Append bars for lighter Pokémon
    svg.selectAll('.bar-light')
    .data(data)
    .enter().append('rect')
    .attr('class', 'bar-light')
    .attr('x', d => x(d.stat) + x.bandwidth() / 2)
    .attr('y', d => y(d.light))
    .attr('width', x.bandwidth() / 2)
    .attr('height', d => height - y(d.light))
    .attr('fill', '#E2DC69')
    .attr('stroke', 'white')
    // Add tooltip on hover
    .on('mouseover', function(event, d) {
        // Show tooltip on hover
        tooltip.transition()
            .duration(200)
            .style('background-color', '#B0AB4A')
        tooltip.html(`<strong>${d.stat}:</strong> ${d.light}`)
            .style('left', (event.pageX+10) + 'px')
            .style('top', (event.pageY-30) + 'px');
    })
    .on('mouseout', function(d) {
        // Hide tooltip on mouseout
        tooltip.transition()
            .duration(700)
    });

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
// Filter legendary and non-legendary Pokémon
const legendaryPokemon = tooltipCopy.filter(pokemon => pokemon.is_legendary == 1);
const nonLegendaryPokemon = tooltipCopy.filter(pokemon => pokemon.is_legendary == 0);

// Calculate average stats for legendary and non-legendary Pokémon
const averageLegendaryStats = calculateAverageStats(legendaryPokemon);
const averageNonLegendaryStats = calculateAverageStats(nonLegendaryPokemon);

// Draw bar chart for legendary comparison
drawLegendaryBarChart(averageLegendaryStats, averageNonLegendaryStats);

// Function to draw the legendary bar chart
function drawLegendaryBarChart(averageLegendaryStats, averageNonLegendaryStats) {
    // Define data for the bar chart
    const data = [
        { stat: 'Attack', legendary: averageLegendaryStats.attack, nonLegendary: averageNonLegendaryStats.attack },
        { stat: 'Defense', legendary: averageLegendaryStats.defense, nonLegendary: averageNonLegendaryStats.defense },
        { stat: 'Speed', legendary: averageLegendaryStats.speed, nonLegendary: averageNonLegendaryStats.speed },
        { stat: 'Special Attack', legendary: averageLegendaryStats.sp_attack, nonLegendary: averageNonLegendaryStats.sp_attack },
        { stat: 'Special Defense', legendary: averageLegendaryStats.sp_defense, nonLegendary: averageNonLegendaryStats.sp_defense },
    ];

    // Set up dimensions and margins for the chart
    const margin = { top: 20, right: 30, bottom: 80, left: 80 };
    const width = 800 - margin.left - margin.right;
    const height = 500 - margin.top - margin.bottom;

    // Append an SVG element to the legendary chart container
    const svg = d3.select('#legendary-bar-chart-container')
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
        .domain([0, d3.max(data, d => Math.max(d.legendary, d.nonLegendary))])
        .nice()
        .range([height, 0]);
    const legendarytooltip = d3.select('#legendary-bar-chart-container')
    .append('div')
    .style('opacity', 0)
    .attr('class', 'tooltip')
    .style('position', 'absolute')
    .style('border', 'solid')
    .style('border-width', '1px')
    .style('border-radius', '5px')
    .style('padding', '10px');

    // Append x axis
    svg.append('g')
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


    // Append bars for legendary Pokémon
    svg.selectAll('.bar-legendary')
        .data(data)
        .enter().append('rect')
        .attr('class', 'bar-legendary')
        .attr('x', d => x(d.stat))
        .attr('y', d => y(d.legendary))
        .attr('width', x.bandwidth() / 2)
        .attr('height', d => height - y(d.legendary))
        .attr('fill', '#E10A03')
        .attr('stroke', 'white')
    // Add tooltip on hover
    .on('mouseover', function(event, d) {
        legendarytooltip.transition()
            .duration(200)
            .style('opacity', .9)
            .style('background-color', '#E10A03')
        legendarytooltip.html(`<strong>${d.stat}:</strong> ${d.legendary}`)
            .style('left', (event.pageX+10) + 'px')
            .style('top', (event.pageY-30) + 'px');
    })
    .on('mouseout', function(d) {
        // Hide tooltip on mouseout
        legendarytooltip.transition()
            .duration(500)
    });

    // Append bars for non-legendary Pokémon
    svg.selectAll('.bar-non-legendary')
        .data(data)
        .enter().append('rect')
        .attr('class', 'bar-non-legendary')
        .attr('x', d => x(d.stat) + x.bandwidth() / 2)
        .attr('y', d => y(d.nonLegendary))
        .attr('width', x.bandwidth() / 2)
        .attr('height', d => height - y(d.nonLegendary))
        .attr('fill', 'white')
         .attr('stroke', 'red')
    // Add tooltip on hover
        .on('mouseover', function(event, d) {
        // Show tooltip on hover
        legendarytooltip.transition()
            .duration(200)
            .style('background-color', '#656561')
        legendarytooltip.html(`<strong>${d.stat}:</strong> ${d.nonLegendary}`)
            .style('left', (event.pageX+10) + 'px')
            .style('top', (event.pageY-30) + 'px');
    })
    .on('mouseout', function(d) {
        // Hide tooltip on mouseout
        tooltip.transition()
            .duration(700)
    });


    // Add legend
    const legend = svg.append('g')
        .attr('class', 'legend')
        .attr('transform', `translate(${width - 110},${margin.top - 30})`);

    legend.append('rect')
        .attr('x', 0)
        .attr('y', 0)
        .attr('width', 15)
        .attr('height', 15)
        .attr('fill', '#E10A03');

    legend.append('text')
        .attr('x', 20)
        .attr('y', 9)
        .text('Legendary');

    legend.append('rect')
        .attr('x', 0)
        .attr('y', 20)
        .attr('width', 15)
        .attr('height', 15)
        .attr('fill', 'white');

    legend.append('text')
        .attr('x', 20)
        .attr('y', 29)
        .text('Non-Legendary');
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


  /* Add borders around the paragraphs */
  p {
    border: 1px solid #ccc; /* Add a border around each paragraph */
    padding: 10px; /* Add padding to give some space between the border and text */
    margin-bottom: 20px; /* Add margin to separate paragraphs */
  }

</style>
