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

    $: dailyMultiplier = hoursPerWeek / 5;
    $: weeklyMultiplier = hoursPerWeek;
    $: fortnightlyMultiplier = hoursPerWeek * 2;
    $: monthlyMultiplier = hoursPerWeek * (weeksPerYear / 12);
    $: annualMultiplier = hoursPerWeek * weeksPerYear;

    $: hourlyRate = getHourlyRate(salary, payCycle, hoursPerWeek, weeksPerYear);
    $: dailyRate = hourlyRate * dailyMultiplier;
    $: weeklyRate = hourlyRate * weeklyMultiplier;
    $: fortnightlyRate = hourlyRate * fortnightlyMultiplier;
    $: monthlyRate = hourlyRate * monthlyMultiplier;
    $: annualRate = hourlyRate * annualMultiplier;

    $: hourlyString = formatCurrency(hourlyRate);
    $: dailyString = formatCurrency(hourlyRate * dailyMultiplier);
    $: weeklyString = formatCurrency(hourlyRate * weeklyMultiplier);
    $: fortnightlyString = formatCurrency(hourlyRate * fortnightlyMultiplier);
    $: monthlyString = formatCurrency(hourlyRate * monthlyMultiplier);
    $: annualString = formatCurrency(hourlyRate * annualMultiplier);

    $: hourlySuperString = calculateSuperString(hourlyRate, superRate);
    $: dailySuperString = calculateSuperString(dailyRate, superRate);
    $: weeklySuperString = calculateSuperString(weeklyRate, superRate);
    $: fortnightlySuperString = calculateSuperString(fortnightlyRate, superRate);
    $: monthlySuperString = calculateSuperString(monthlyRate, superRate);
    $: annualSuperString = calculateSuperString(annualRate, superRate);

    $: annualIncomeTax = calculateIncomeTax(annualRate);
    $: hourlyIncomeTax = annualIncomeTax / annualMultiplier;
    $: dailyIncomeTax = hourlyIncomeTax * dailyMultiplier;
    $: weeklyIncomeTax = hourlyIncomeTax * weeklyMultiplier;
    $: fortnightlyIncomeTax = hourlyIncomeTax * fortnightlyMultiplier;
    $: monthlyIncomeTax = hourlyIncomeTax * monthlyMultiplier;

    $: annualIncomeTaxString = formatCurrency(annualIncomeTax);
    $: hourlyIncomeTaxString = formatCurrency(hourlyIncomeTax);
    $: dailyIncomeTaxString = formatCurrency(dailyIncomeTax);
    $: weeklyIncomeTaxString = formatCurrency(weeklyIncomeTax);
    $: fortnightlyIncomeTaxString = formatCurrency(fortnightlyIncomeTax);
    $: monthlyIncomeTaxString = formatCurrency(monthlyIncomeTax);

    function calculateSuperString(rate: number, superRate: number): string {
        if (rate === 0) {
            return formatCurrency(0);
        } else {
            return formatCurrency(rate * (superRate / 100));
        }
    }

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

    function calculateIncomeTax(salary: number): number {
        if (salary <= 18200) {
            return 0;
        } else if (salary <= 45000) {
            return (salary - 18200) * 0.19;
        } else if (salary <= 120000) {
            return 5092 + (salary - 45000) * 0.325;
        } else if (salary <= 180000) {
            return 29467 + (salary - 120000) * 0.37;
        } else {
            return 51667 + (salary - 180000) * 0.45;
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
                    <div class="input-group">
                        <Input class="form-control" type="number" bind:value={superRate} min="0" max="100" step="0.5"/>
                        <div class="input-group-append">
                            <span class="input-group-text">%</span>
                        </div>
                    </div>

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
                    <td>{fortnightlyString}</td>
                    <td>{monthlyString}</td>
                    <td>{annualString}</td>
                </tr>
                <tr style="color: cornflowerblue">
                    <th scope="row">Super</th>
                    <td>{hourlySuperString}</td>
                    <td>{dailySuperString}</td>
                    <td>{weeklySuperString}</td>
                    <td>{fortnightlySuperString}</td>
                    <td>{monthlySuperString}</td>
                    <td>{annualSuperString}</td>
                </tr>
                <tr style="color: sandybrown">
                    <th scope="row" style="font-weight: normal">Income Tax</th>
                    <td>{hourlyIncomeTaxString}</td>
                    <td>{dailyIncomeTaxString}</td>
                    <td>{weeklyIncomeTaxString}</td>
                    <td>{fortnightlyIncomeTaxString}</td>
                    <td>{monthlyIncomeTaxString}</td>
                    <td>{annualIncomeTaxString}</td>
                </tr>
                </tbody>
            </Table>
        </div>
    </main>
</body>
