<script>
  import { onMount } from 'svelte';
  import { select, scaleLinear, max, scaleBand, axisBottom, axisLeft } from 'd3';

  // Dom parsing
  export let doc;
  let count = 0;
  let tags = {};

  onMount(async () => {
    parseElement(doc);
    startPlot();
  });

  function parseElement(element) {

    if (!tags.hasOwnProperty(element.tagName)) {
      const tag = element.tagName;
      tags[tag] = 1;
    } else {
      tags[element.tagName]++;
    }

    count++;

    for (let i = 0; i < element.children.length; i++) {
      parseElement(element.children[i]);
    };
    
  };

  //Plotting with d3
  const margin = 30;
  let svg;
  let width;
  let height;

  function startPlot() {
    svg = select('svg');
    width = +svg.attr('width') - 2 * margin; //the unary '+' operator parses strings into numbers
    height = +svg.attr('height')  - 2 * margin;
    render(Object.entries(tags));
  }

  function render(data) {

    const xValue = d => d[0];
    const yValue = d => d[1];

    const g = svg.append('g')
      .attr('transform', `translate(${margin},${margin})`);

    // Setting up the Y axis
    const yScale = scaleLinear()
      .domain([0, max(data, yValue)])
      .range([height, 0]);

    g.append('g')
      .call(axisLeft(yScale))

    // Setting up the X axis
    const xScale = scaleBand()
      .domain(data.map(xValue))
      .range([0, width])
      .padding(0.5)

    g.append('g')
      .call(axisBottom(xScale))
      .attr('transform', `translate(0, ${height})`)
      

    // Drawing the bars
    g.selectAll('rect')
      .data(data)
      .attr('class', 'bar')
      .enter().append('rect')
      .attr('x', d => xScale(xValue(d)))
      .attr('y', d => yScale(yValue(d)))
      .attr('width', xScale.bandwidth())
      .attr('height', d => {
        return (height - yScale(yValue(d)));
      });
  };
	
</script>

<style>
  h4 {
    text-align: center;
  }
</style>

<h4>
  Dom Elements in page {count}
</h4>

<svg width="1000" height="400"></svg>