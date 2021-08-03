<template>
  <div id="app">
      <input type="file" @change="readCSV">
      <chart v-if="csv" :data="csv" />
    </div>
</template>

<script lang="ts">
import Vue from 'vue';
import PieChart from "./components/PieChart.vue";

export default Vue.extend({
    name: "app",
    components: {
        chart: PieChart
    },
    data(){
        return {
            csv: undefined as string[][]
        }
    },
    methods: {
        handler() {
            var args = arguments;
            for (var arg of args) {
                if (arg instanceof Function) {
                    arg();
                }
            }
        },
        parseCSV(text: string){
            // Obtenemos las lineas del texto
            let lines = text.replace(/\r/g, '').split('\n');
            return lines.map(line => {
                // Por cada linea obtenemos los valores
                let values = line.split(';');
                return values;
            });
        },
        reverseMatrix(matrix: any[][]){
            let output = [];
            // Por cada fila
            matrix.forEach((values, row) => {
                // Vemos los valores y su posicion
                values.forEach((value, col) => {
                    // Si la posición aún no fue creada
                    if (output[col] === undefined) 
                        output[col] = [];
                    output[col][row] = value;
                });
            });
            return output;
        },
        readCSV(evt: any) {
            let file = evt.target.files[0];
            let reader = new FileReader();
            reader.onload = (e) => {
                // Cuando el archivo se terminó de cargar
                let lines = this.parseCSV(e.target.result);
                let output = this.reverseMatrix(lines);
                this.csv = output;
            };
            // Leemos el contenido del archivo seleccionado
            reader.readAsBinaryString(file);
        },
    }
});
</script>