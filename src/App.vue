<template>
  <div id="app">
    <svg
      :height='height'
      :width='width'
    >
      <circle
        :cx="circle.cx"
        :cy="circle.cy"
        :r="circle.r"
        stroke="black"
        :fill="randoFill()"
      />
      <rect
        :width="rect.size"
        :height="rect.size"
        :x="rect.x"
        :y="rect.y"
        stroke="black"
        :fill="randoFill()"
      />
      <line
        :key="index + 'l'"
        v-for="(line, index) in lines"
        :x1="line.x1"
        :y1="line.y1"
        :x2="line.x2"
        :y2="line.y2"
        stroke="black"
      />
      <polygon :points="trianglePoints" stroke="black" :fill="randoFill()" />

      <path
        :key="index"
        v-for="(squiggle, index) in squiggles"
        :d="squiggle"
        stroke="black"
        fill="none"
      />
    </svg>
  </div>
</template>

<script>

export default {
  name: 'app',
  data: function data() {
    return {
      height: 900,
      width: 1414,
      padding: 10,
      radius: 100,
      circle: {
        r: 0,
        cx: 0,
        cy: 0,
      },
    };
  },
  created: function created() {
    this.circle = this.buildCircle();
    this.rect = this.buildRect();
  },
  computed: {
    trianglePoints: function trianglePoints() {
      const tempSize = this.size();
      const startCoords = this.startCoordsWithinSize(tempSize);
      const c1 = { x: startCoords.x, y: startCoords.y };

      // points 2 and 3 are relative to first point
      const c2 = { x: c1.x + tempSize, y: c1.y };
      const c3 = { x: c1.x + (c2.x - c1.x) / 2, y: c1.y - (tempSize * 0.85) };

      const coordinates = `${c3.x},${c3.y} ${c2.x},${c2.y} ${c1.x},${c1.y}`;
      return coordinates;
      // return `150,15 200,100 100,100`;
    },
    squiggles: function squiggles() {
      const buildCurve = function buildCurve(start) {
        let curve = `M ${start.x} ${start.y}`;

        curve += 'C ';
        curve += `${start.x + 40} ${start.y + 10},`;
        curve += ' ';
        curve += `${start.x + 65} ${start.y + 10},`;
        curve += ' ';
        curve += `${start.x + 95} ${start.y + 80}`;

        curve += 'S ';
        curve += `${start.x + 150} ${start.y + 150},`;
        curve += ' ';
        curve += `${start.x + 180} ${start.y + 80}`;

        // C 40 10, 65 10, 95 80 S 150 150, 180 80`;
        return curve;
      };

      const start1 = this.startCoordsWithinSize(170);
      const start2 = {
        x: start1.x,
        y: start1.y + 100,
      };

      return [
        buildCurve(start1),
        buildCurve(start2),
      ];
    },
    lines: function lines() {
      const arrLines = [
        {
          x1: 0,
          y1: (this.height * Math.random()).toFixed(),
          x2: (this.width * Math.random()).toFixed(),
          y2: 0,
        },
        {
          x1: (this.width * Math.random()).toFixed(),
          y1: 0,
          x2: (this.width * Math.random()).toFixed(),
          y2: this.height,
        },
      ];
      return arrLines;
    },
  },
  methods: {
    startCoordsWithinSize: function startCoordsWithinSize(size) {
      // start x and start y must be with boundaries, e.g. a tempSize square box must fit
      let x = (this.width - size) * Math.random();
      if (x < this.padding) {
        x = this.padding;
      }
      if (x + size >= this.width) {
        x = this.width - size - this.padding;
      }

      const y = Math.min(
        this.height - this.padding,
        size + ((this.height - size) * Math.random()),
      );
      return { x, y };
    },
    randoFill: function randoFill() {
      const rand = this.random13();
      switch (rand) {
        case 1:
          return '#000';
        case 2:
          return '#CCC';
        default:
          return '#FFF';
      }
    },
    buildCircle: function buildCircle() {
      const size = this.size();
      const r = size / 2;
      const startCoords = this.startCoordsWithinSize(size);
      return { cx: startCoords.x + r, cy: startCoords.y - r, r };
    },
    buildRect: function buildRect() {
      const size = this.size();
      const startCoords = this.startCoordsWithinSize(size);
      return { size, x: startCoords.x, y: startCoords.y - size };
    },
    random13: function random13() {
      return Math.ceil(Math.random() * 3);
    },
    size: function size() {
      // return sizes 1-3
      const rand = this.random13();
      return rand * 150;
    },
  },
};
</script>

<style lang="scss">
  svg {
    border: thick solid #000;

    * {
      stroke-width: 5;
    }
  }
</style>
