<template>
  <canvas :width="width" :height="height" id="tags_ball" ref="canvas_tags_ball">
    canvas
  </canvas>
</template>

<style scoped>
#tags_ball {
  border-radius: 20px;
  /* border: 2px solid #66ccff; */
}
</style>

<script>
var Color = require("color");
let Animation = function() {
  this.running = true;
};
Animation.prototype = {
  stop: function() {},
  start: function() {},
};
export default {
  name: "tags-ball",
  install: function(vue) {
    vue.component(this.name, this);
  },
  data() {
    return {
      selectItem: "",
      animation: new Animation(),
    };
  },
  props: {
    // handleSearch: Function,
    stop: {
      type: Boolean,
      default: false,
    },
    tags: {
      type: Array,
    },
    width: {
      type: Number,
      default: 200,
    },
    height: {
      type: Number,
      default: 200,
    },
    radius: {
      type: Number,
      default: 80,
    },
    fontMax: {
      type: Number,
      default: 60,
    },
    font: {
      type: String,
      default: "40px monaco",
    },
    color: {
      tupe: String,
      default: "#fc8457",
    },
  },
  methods: {
    init_ball: function() {
      let canvas = this.$refs["canvas_tags_ball"];
      let tags = this.tags;

      // console.log(tags)
      // this.tags.map(item => {
      //   // console.log(item.tennganh)
      //   tags.push(item.tennganh)
      // })
      let Radius = this.radius;
      let fontMax = this.fontMax;
      let color = Color(this.color);
      let ctx = canvas.getContext("2d");
      ctx.font = this.font;

      let angleX = Math.PI / 100 / 2;
      let angleY = Math.PI / 100 / 2;

      const hx = canvas.width / 2;
      const hy = canvas.height / 2;

      let count = tags.length;
      if (count == 0) {
        return;
      }
      canvas.addEventListener("mousemove", function(event) {
        let x = event.layerX - hx;
        let y = event.layerY - hy;

        angleY = -x * 0.0001;
        angleX = -y * 0.0001;
      });

      let points = [];
      let ve3 = function(x, y, z) {
        this.x = x;
        this.y = y;
        this.z = z;
      };
      ve3.prototype = {
        getm: function() {
          return Math.sqrt(
            Math.pow(this.x, 2) + Math.pow(this.y, 2) + Math.pow(this.z, 2)
          );
        },
        resize: function(R) {
          let pm = this.getm();
          this.x = this.x * (R / pm);
          this.y = this.y * (R / pm);
          this.z = this.z * (R / pm);
          return this;
        },
      };
      function create() {
        const gold = (Math.sqrt(5.0) - 1) / 2;
        for (let i = 1; i <= count; i++) {
          let z = (2 * i - 1) / count - 1;
          let x =
            Math.sqrt(1 - Math.pow(z, 2)) * Math.cos(2 * Math.PI * i * gold);
          let y =
            Math.sqrt(1 - Math.pow(z, 2)) * Math.sin(2 * Math.PI * i * gold);
          let p = new ve3(x, y, z);
          points.push(p.resize(Radius));
          // console.log(points)
          // console.log(p.x,p.y,p.z)
        }
      }
      create();

      let labels = [];
      let label = function(x, y, z, manganh,tennganh, width, max) {
        this.x = x;
        this.y = y;
        this.z = z;
        this.manganh = manganh;
        this.tennganh = tennganh;
        this.width = width;
        this.max = max;
      };

      label.prototype = {
        paint: function() {
          ctx.save();
          let alpha = (this.z + Radius) / (2 * Radius);
          // ctx.fillStyle = "rgba("+color+ (alpha + 0.5) + ")";
          ctx.fillStyle = color.alpha(alpha + 0.5).toString();
          ctx.fillText(
            this.tennganh,
            hx + this.x - Math.min(this.max, this.width) / 2,
            hy + this.y,
            this.max
          );
          ctx.restore();
        },
      };
      function rotateX() {
        let cos = Math.cos(angleX);
        let sin = Math.sin(angleX);
        for (let i = 0; i < labels.length; i++) {
          let y1 = labels[i].y * cos - labels[i].z * sin;
          let z1 = labels[i].z * cos + labels[i].y * sin;
          labels[i].y = y1;
          labels[i].z = z1;
        }
      }

      function rotateY() {
        let cos = Math.cos(angleY);
        let sin = Math.sin(angleY);
        for (let i = 0; i < labels.length; i++) {
          let x1 = labels[i].x * cos - labels[i].z * sin;
          let z1 = labels[i].z * cos + labels[i].x * sin;
          labels[i].x = x1;
          labels[i].z = z1;
        }
      }

      function rotateZ() {
        let cos = Math.cos(angleY);
        let sin = Math.sin(angleY);
        for (let i = 0; i < labels.length; i++) {
          let x1 = labels[i].x * cos - labels[i].y * sin;
          let y1 = labels[i].y * cos + labels[i].x * sin;
          labels[i].x = x1;
          labels[i].y = y1;
        }
      }

      let init = () => {
        for (let i = 0; i < points.length; i++) {
          let t = ctx.measureText(tags[i]);
          labels.push(
            new label(
              points[i].x,
              points[i].y,
              points[i].z,
              tags[i].manganh,
              tags[i].tennganh,
              t.width,
              fontMax
            )
          );
        }
        canvas.addEventListener("click", (event) => {
          
          let x = event.layerX - hx;
          let y = event.layerY - hy;

          angleY = -x * 0.0001;
          angleX = -y * 0.0001;

          labels.forEach((element) => {
            if (
              Math.abs(element.x - x) <= 20 &&
              Math.abs(element.y - y) <= 20
            ) {
              this.$emit("changeTxtSearch", element);
              // this.handleSearch(element.str);
              // console.log(element)
            }
          });
        });
      };
      init();

      let animation = this.animation;
      let animate = function animate() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        rotateX();
        rotateY();
        rotateZ();
        for (let i = 0; i < labels.length; i++) {
          labels[i].paint();
        }
        if (animation.running) {
          requestAnimationFrame(animate);
        }
      };

      this.animation.start = function() {
        animation.running = true;
        animate();
      };
      this.animation.stop = function() {
        animation.running = false;
      };
      this.animation.start();
    },
  },

  mounted: function() {
    this.init_ball();
  },
  computed: {
    // tags:function(){
    // }
  },
  watch: {
    tags: function(t) {
      this.init_ball(t);
    },
    stop: function(t) {
      if (t) {
        this.animation.stop();
      } else {
        this.animation.start();
      }
    },
  },
};
</script>
