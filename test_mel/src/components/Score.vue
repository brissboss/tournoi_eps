<template>
    <v-card
        style="
            width: 100%;
            margin-bottom: 50px;
            background-color: #efefef;
        "
    >
        <v-card-title>
            Score
        </v-card-title>

        <v-card-text
            style="
                display: flex;
                flex-direction: row;
                justify-content: space-between;
            "
        >
            <div
                style="
                    display: flex;
                    flex-direction: column;
                    width: 10%;
                "
            >
               <table
                    style="
                        width: 100%;
                        text-align: center;
                    "
               >
                    <th class="test" style="border-color: transparent; border-bottom-color: #000; color: transparent;">_</th>
                    <tr>
                        <th class="test-th" style="border-right: 0px;">Écart</th>
                    </tr>
                    <tr>
                        <td class="test-td" style="border-right: 0px;">Points</td>
                    </tr>
               </table>
            </div>
            <div
                style="
                    display: flex;
                    flex-direction: column;
                    width: 80%;
                "
            >
                <table
                    style="
                        width: 100%;
                        text-align: center;
                    "
                >
                    <th
                        class="test"
                    >
                        Au dessus de vous
                    </th>
                    <th
                        class="test"
                    >
                        En dessous de vous
                    </th>
                </table>
                <table
                    style="
                        width: 100%;
                        text-align: center;
                    "
                >
                    <th class="test-th">5</th>
                    <th class="test-th">4</th>
                    <th class="test-th">3</th>
                    <th class="test-th">2</th>
                    <th class="test-th">1</th>
                    <th class="test-th">1</th>
                    <th class="test-th">2</th>
                    <th class="test-th">3</th>
                    <th class="test-th">4</th>
                    <th class="test-th">5</th>
                    <tr>
                        <td class="test-td">6</td>
                        <td class="test-td">5</td>
                        <td class="test-td">5</td>
                        <td class="test-td">5</td>
                        <td class="test-td">4</td>
                        <td class="test-td">4</td>
                        <td class="test-td">3</td>
                        <td class="test-td">3</td>
                        <td class="test-td">3</td>
                        <td class="test-td">2</td>
                    </tr>
                </table>
            </div>
            <div
                style="
                    display: flex;
                    flex-direction: column;
                    width: 10%;
                "
            >
               <table
                    style="
                        width: 100%;
                        text-align: center;
                    "
               >
                    <th class="test" style="border-left: 0px; height: 42px;">Match perdu</th>
                    <tr>
                        <td class="test-td" style="border-left: 0px;">1</td>
                    </tr>
               </table>
            </div>
        </v-card-text>
    </v-card>
</template>

<style>
    table {
        border-collapse: collapse;
    }

    .test {
        width: 50%;
        border: 1px solid #000;
    }

    .test-th {
        border: 1px solid #000;
        border-top: 0px;
    }

    .test-td {
        font-weight: bold;
        font-size: 1.1rem;
        color: #f00;
        border: 1px solid #000;
    }
</style>