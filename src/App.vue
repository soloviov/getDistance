<template>
    <div id="app">
        <fieldset>
            <legend>Settings</legend>
            <div>
                <select v-model="startPoint">
                    <option value="">Select Place</option>
                    <option v-for="(item, key) in list" :key="key" :value="key">{{ item.name }}</option>
                </select>
            </div>
            <div>
                <input type="range" v-model.number="distance" min="0" max="1000" step="50">{{ distance }} km
            </div>
        </fieldset>
        <fieldset>
            <legend>Results</legend>
            <ol>
                <li v-for="(item, key) in result" :key="key">{{ item.name }} ({{ item.distance }} km)</li>
            </ol>
        </fieldset>
    </div>
</template>

<script>
import sourceData from './data.json';

const earthRadius = 6371 * 1000; // metres

export default {
    name: 'App',
    data() {
        return {
            startPoint: '',
            distance: 50,
            list: sourceData
        };
    },
    methods: {
        calculateDistance(posA, posB) {
            const lat1 = (posA.latitude * Math.PI) / 180;
            const lat2 = (posB.latitude * Math.PI) / 180;
            const disLat = ((posB.latitude - posA.latitude) * Math.PI) / 180;
            const disLon = ((posB.longitude - posA.longitude) * Math.PI) / 180;

            const a = Math.sin(disLat / 2) * Math.sin(disLat / 2) + Math.cos(lat1) * Math.cos(lat2) * Math.sin(disLon / 2) * Math.sin(disLon / 2);
            const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));

            return (earthRadius * c);
        }
    },
    computed: {
        result() {
            const items = [];
            const selected = this.list[this.startPoint];
            if (!selected || !this.distance) {
                return items;
            }

            this.list.forEach((item, key) => {
                if (this.startPoint !== key) {
                    let distance = this.calculateDistance(selected, item);
                    distance = (distance / 1000).toFixed(2);

                    if (distance <= this.distance) {
                        items.push({
                            name: item.name,
                            distance
                        });
                    }
                }
            });

            items.sort((a, b) => (a.distance - b.distance));

            return items;
        }
    }
};
</script>

<style>
#app {
    font-family: Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    width: 600px;
    margin: 20px auto;
}
</style>
