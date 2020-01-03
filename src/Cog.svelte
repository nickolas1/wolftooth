<script>
  const side = 150;
  const linkLength = 20;
  let nTeeth = 16;

  const getRotations = nRepeats => {
    const rotations = [];
    for (let i = 1; i <= nRepeats; i++) {
      rotations.push((i * 360) / nRepeats);
    }
    return rotations;
  };

  // derived variables for teeth
  $: toothDegrees = 360 / nTeeth;
  $: semiToothDegreesRadians = (0.5 * toothDegrees * Math.PI) / 180;
  $: toothCircleRadius = 0.31 * linkLength;
  $: dSin = toothCircleRadius * Math.sin(semiToothDegreesRadians);
  $: dCos = toothCircleRadius * Math.cos(semiToothDegreesRadians);
  $: dx = 0.5 * (linkLength - 2 * dSin);
  $: apothem = dx / Math.tan(Math.PI / nTeeth);
  $: radius = apothem + dSin * Math.SQRT1_2;
  $: x0 = side / 2 - dx;
  $: x1 = side / 2 + dx;
  $: y = side / 2 - apothem;
  $: toothWidth = x1 - x0 + 2 * dSin - 2 * dCos;
  $: toothHeight = 0.75 * toothWidth;
  $: toothPath = `M ${x0} ${y} A ${toothCircleRadius} ${toothCircleRadius} 0 0 0 ${x0 -
    dSin +
    dCos} ${y - dCos} L ${x0 - dSin + dCos + 0.25 * toothHeight} ${y -
    dCos -
    toothHeight} L ${x1 + dSin - dCos - 0.25 * toothHeight} ${y -
    dCos -
    toothHeight} L ${x1 + dSin - dCos} ${y -
    dCos} A ${toothCircleRadius} ${toothCircleRadius} 0 0 0 ${x1} ${y}`;
  $: teethRotations = getRotations(nTeeth);

  // derived variables for inner spline
  $: splineOuterRadius = 1.37 * linkLength;
  $: splineRidgeRadius = splineOuterRadius + toothCircleRadius / 2;

  // derived variables for text
  $: textSize = toothCircleRadius / 2.1;
  $: textRadius = apothem - toothCircleRadius / 2.25;
  $: textPath = `M ${side / 2} ${side / 2 -
    textRadius} A ${textRadius} ${textRadius} 0 0 1 ${side / 2 +
    textRadius} ${side / 2}`;

  // derived variables and constants for holes
  const holeRotations = getRotations(7);
  const holeAngle = ((holeRotations[1] - holeRotations[0]) * Math.PI) / 180;
  $: holeInnerRadius = splineRidgeRadius + toothCircleRadius / 8;
  $: holeOuterRadius = textRadius - toothCircleRadius / 4;
  $: holeDx =
    holeOuterRadius * Math.sin(holeAngle) -
    toothCircleRadius * Math.cos(holeAngle);
  $: holeDy =
    holeOuterRadius * (1 - Math.cos(holeAngle)) -
    toothCircleRadius * Math.sin(holeAngle);
  $: holePath = `M ${side / 2} ${side / 2 - holeInnerRadius} L ${side /
    2} ${side / 2 - holeOuterRadius} L ${side / 2 - holeDx} ${side / 2 -
    holeOuterRadius +
    holeDy}`;
</script>

<svg
  width="300"
  height="300"
  viewBox="0 0 {side}
  {side}"
  xmlns="http://www.w3.org/2000/svg">
  <g transform="rotate(0, {side / 2}, {side / 2})">
    <path id="cogTooth" d="{toothPath}" class="steel"></path>
    {#each teethRotations as rotation}
      <use
        xlink:href="#cogTooth"
        transform="rotate({rotation}, {side / 2}, {side / 2})"></use>
    {/each}
    }
    <circle cx="{side / 2}" cy="{side / 2}" r="{radius}" class="steel"></circle>
    <circle
      cx="{side / 2}"
      cy="{side / 2}"
      r="{splineOuterRadius}"
      class="hole"></circle>
    <circle
      cx="{side / 2}"
      cy="{side / 2}"
      r="{splineRidgeRadius}"
      class="spline-ridge"></circle>
    <path id="hole" d="{holePath}" class="hole"></path>
    {#each holeRotations as rotation}
      <use
        xlink:href="#hole"
        transform="rotate({rotation}, {side / 2}, {side / 2})"></use>
    {/each}
    }
    <path id="textPath" d="{textPath}" stroke="none" fill="none"></path>
    <text class="cog-text" font-size="{textSize}">
      <textPath href="#textPath">made in usa</textPath>
    </text>
    <text
      class="cog-text"
      font-size="{textSize}"
      transform="rotate(90, {side / 2}, {side / 2})">
      <textPath href="#textPath">{nTeeth}T stainless steel</textPath>
    </text>
    <text
      class="cog-text"
      font-size="{textSize}"
      transform="rotate(180, {side / 2}, {side / 2})">
      <textPath href="#textPath">wolf tooth components</textPath>
    </text>
  </g>
</svg>

<style>
  .cog-text {
    text-transform: uppercase;
  }
  .spline-ridge {
    fill: none;
    stroke: #5d5d5d;
    stroke-width: 0.5;
  }
  .steel {
    stroke: none;
    fill: #9c9c9c;
  }
  .hole {
    stroke: none;
    fill: white;
  }
</style>
