<script lang="ts">
    export let payData: number[];
    import { Doughnut } from 'svelte-chartjs';

    import {
        Chart as ChartJS,
        Title,
        ArcElement,
        CategoryScale,
    } from 'chart.js';

    ChartJS.register(Title, ArcElement, CategoryScale);

    $: data = {
        labels: ['Red', 'Green', 'Yellow', 'Grey', 'Dark Grey'],
        datasets: [
            {
                data: payData,
                backgroundColor: ['#9ACD32FF', '#F4A460FF', '#6495EDFF']
            },
        ],
    };

</script>

<Doughnut {data} options={{ responsive: true, cutout: '70%', animation: false}} />
