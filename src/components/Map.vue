<template>
  <div class="map">
    <h3>Карта офиса</h3>

    <div v-if="!isLoading" class="map-root">
      <MapSVG ref="svg" />
      <TableSVG ref="table" v-show="false" />
    </div>
    <div v-else>Loading...</div>
  </div>
</template>

<script>
import MapSVG from "@/assets/images/map.svg";
import TableSVG from "@/assets/images/workPlace.svg";
import legend from "@/assets/data/legend.json";
import tables from "@/assets/data/tables.json";
import people from "@/assets/data/people.json";
import * as d3 from "d3";

export default {
  components: {
    MapSVG,
    TableSVG,
  },
  data() {
    return {
      isLoading: false,
      svg: null,
      g: null,
      tables: [],
      tableSVG: null,
      people,
      cardPeople: null,
    };
  },
  mounted() {
    this.svg = d3.select(this.$refs.svg);
    this.tableSVG = d3.select(this.$refs.table);
    this.g = this.svg.select("g");

    this.tables = tables;

    if (this.g) {
      this.drawTables();
    } else {
      console.log("ERROR");
    }
  },
  methods: {
    drawTables() {
      const svgTablesGroup = this.g.append("g").classed("groupPlaces", true);

      this.tables.map((table) => {
        const svgTable = svgTablesGroup
          .append("g")
          .on("click", this.clickTable)
          .attr("transform", `translate(${table.x}, ${table.y}) scale(0.5)`)
          .attr("id", table._id)
          .classed("employer-place", true);

        svgTable
          .append("g")
          .attr("transform", `rotate(${table.rotate || 0})`)
          .attr("group_id", table.group_id)
          .html(this.tableSVG.html())
          .attr(
            "fill",
            legend.find((it) => it.group_id === table.group_id)?.color ??
              "transparent"
          );
      });
    },
    clickTable(d) {
      this.people.forEach((pers) => {
        if (pers.tableId === Number(d.currentTarget.id)) {
          this.cardPeople = pers;
          this.$emit('showCardPerson', pers);
          // this.$modal.show("card-modal");
        }
      });
    },
    hide() {
      // this.$modal.hide("card-modal");
    },
  },
};
</script>

<style scoped>
.map {
  height: 100%;
  width: 100%;
  padding: 24px;
  overflow: hidden;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}

.map-root {
  height: 100%;
  width: 100%;
  overflow: hidden;
  box-sizing: border-box;
}

h3 {
  margin-top: 0px;
}

::v-deep svg {
  height: 100%;
  width: 100%;
}

::v-deep .table {
  cursor: pointer;
}

.card {
  padding: 30px;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.card__header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}

.card__header-close {
  cursor: pointer;
}

.card__body {
  display: flex;
  flex: 1 1 540px;
  justify-content: space-around;
}

.card__foto-container {
  height: 200px;
}

.info {
  padding: 10px;
}

.card__img {
  max-height: 100%;
}

.info__name {
  font-size: 22px;
  margin-bottom: 5px;
}
.info__group {
  margin-bottom: 10px;
}

.info__group span {
  font-weight: 900;
}
</style>
