<script lang="ts">
    import { onDestroy, onMount } from "svelte";
    import { writable } from "svelte/store";

    let timerValue = '00:00:00';
    let interval: any;
    let isRunning = false;
    const timerValues = writable<string[]>([]);

    async function fetchTimerValues() {
        try {
            const response = await fetch("https://time-tracker-api-zfm5.onrender.com/timers");
            if (response.ok) {
                const data = await response.json();
               timerValues.set(data.map((timer: { time: string }) => timer.time));
            } else {
                console.error("Failed to fetch timer values:", response.status);
            }
        } catch (error) {
            console.error("Error fetching timer values:", error);
        }
    }


  async function saveTimerValue(time: string) {
        try {
            const response = await fetch("https://time-tracker-api-zfm5.onrender.com/timer", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json"
                },
                body: JSON.stringify({ time })
            });
            if (response.ok) {
                console.log("Timer value saved successfully");
                fetchTimerValues();
            } else {
                console.error("Failed to save timer value:", response.status);
            }
        } catch (error) {
            console.error("Error saving timer value:", error);
        }
    }

    function toggleTimer() {
        if (isRunning) {
            stopTimer();
            saveTimerValue(timerValue);
            timerValues.update(values=>[...values, timerValue]); 
            timerValue = '00:00:00';
            isRunning = false;
        } else {
            startTimer();
            isRunning = true;
        }
    }

    function startTimer() {
        clearInterval(interval);
        let totalSeconds = 0;

        interval = setInterval(() => {
            totalSeconds++;
            const hours = Math.floor(totalSeconds / 3600);
            const minutes = Math.floor((totalSeconds % 3600) / 60);
            const seconds = totalSeconds % 60;
            timerValue = `${String(hours).padStart(2, '0')}:${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
        }, 1000);
    }

    function stopTimer() {
        clearInterval(interval);
    }

    onMount(() => {
        fetchTimerValues();
    });

    onDestroy(() => {
        clearInterval(interval); 
    });
</script>

<div class="container">
    <ul class="list">
        {#each $timerValues as timerValue}
        <li class="list__item">{timerValue}</li>
        {/each}
    </ul>
    <div class="timer">
        <div class="timer__monitor">
            <div>{timerValue}</div>
        </div>
        <button class="timer__btn" on:click={toggleTimer}>
            {isRunning ? 'Stop' : 'Start'}
        </button>
    </div>
</div>

<style>
    :root {
        background: #35374B;
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }

    .container {
        display: flex;
    }

    .timer{
        width: 30%;
        margin-left: auto;
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 32px;
        margin-top: 128px;
    }

    .timer__monitor {
        background: #78A083;
        border-radius: 16px;
        padding: 16px 32px;
        text-align: center;
        font-size: 48px;
        font-family: 'Courier New', Courier, monospace;
        font-weight: 700;
        color: #344955;
    }

    .timer__btn {
        background: #78A083;
        border-radius: 16px;
        padding: 16px 32px;
        text-align: center;
        font-size: 20px;
        font-family:'Courier New', Courier, monospace;
        font-weight: 700;
        border: none;
        cursor: pointer;
        color: #344955;
    }

    .list {
        background: #78A083;
        border-radius: 16px;
        padding: 16px 32px;
        list-style-type: none;
        display: flex;
        flex-direction: column;
        gap: 16px;
        width: 160px;
    }

    .list__item {
        background: #50727B;
        border-radius: 16px;
        padding: 16px 32px;
        font-size: 20px;
        font-family:'Courier New', Courier, monospace;
        color: #344955;
    }
</style>