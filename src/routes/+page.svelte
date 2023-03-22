<script lang="ts">
    import {Input, Label, Table} from "sveltestrap";
    import CurrencyInput from '@canutin/svelte-currency-input';

    type PayCycle = "Annually" | "Monthly" | "Fortnightly" | "Weekly" | "Daily" | "Hourly";
    const allPayCycleOptions: PayCycle[] = ["Annually", "Monthly", "Fortnightly", "Weekly", "Daily", "Hourly"];

    let salary = 60000;
    let payCycle: PayCycle = 'Annually';
    let hoursPerWeek = 40;
    let weeksPerYear = 52;

    let superRate = 10.5;

    $: hourlyRate = getHourlyRate(salary, payCycle, hoursPerWeek, weeksPerYear)

    $: hourlyString = formatCurrency(hourlyRate);
    $: dailyString = formatCurrency(hourlyRate * (hoursPerWeek / 5));
    $: weeklyString = formatCurrency(hourlyRate * hoursPerWeek);
    $: fornightlyString = formatCurrency(hourlyRate * hoursPerWeek * 2);
    $: monthlyString = formatCurrency(hourlyRate * (hoursPerWeek * (weeksPerYear / 12)));
    $: annualString = formatCurrency(hourlyRate * (hoursPerWeek * weeksPerYear));

    function getHourlyRate(pay: number, payCycle: PayCycle, workWeekHours: number, weeksPerYear: number): number {
        switch (payCycle) {
            case "Annually":
                return pay / (workWeekHours * weeksPerYear);
            case "Monthly":
                return pay / (workWeekHours * (weeksPerYear / 12));
            case "Fortnightly":
                return pay / (workWeekHours * (weeksPerYear / 26));
            case "Weekly":
                return pay / workWeekHours;
            case "Daily":
                return pay / (workWeekHours / 5);
            case "Hourly":
                return pay;
        }
    }

    function formatCurrency(value: number): string {
        return value.toLocaleString('en-US', {
            style: 'currency',
            currency: 'USD',
        });
    }
</script>

<svelte:head>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css">
</svelte:head>
<body>
    <main>
        <div class="col-lg-8 pt-5 mx-auto">
            <h1>Australian Pay Calculator</h1>

            <div class="row pb-5">
                <div class="col-md-4">
                    <Label>Salary</Label>
                    <CurrencyInput bind:value={salary} name="default" />

                    <Label>Pay Cycle</Label>
                    <Input type="select" bind:value={payCycle}>
                        {#each allPayCycleOptions as option}
                            <option value={option}>{option}</option>
                        {/each}
                    </Input>

                    <Label>Hours Worked Per Week</Label>
                    <Input bind:value={hoursPerWeek}></Input>

                    <Label>Weeks Worked Per Year</Label>
                    <Input bind:value={weeksPerYear}></Input>
                </div>
                <div class="col-md-4">
                    <Label>Super Rate</Label>
                    <Input bind:value={superRate}></Input>
                </div>
            </div>

            <Table borderless>
                <thead>
                <tr>
                    <th>#</th>
                    <th>Hourly</th>
                    <th>Daily</th>
                    <th>Weekly</th>
                    <th>Fortnightly</th>
                    <th>Monthly</th>
                    <th>Yearly</th>
                </tr>
                </thead>
                <tbody>
                <tr style="color: green">
                    <th scope="row">Total Income</th>
                    <td>{hourlyString}</td>
                    <td>{dailyString}</td>
                    <td>{weeklyString}</td>
                    <td>{fornightlyString}</td>
                    <td>{monthlyString}</td>
                    <td>{annualString}</td>
                </tr>
                </tbody>
            </Table>
        </div>
    </main>
</body>
