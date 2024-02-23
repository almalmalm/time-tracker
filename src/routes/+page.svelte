<script lang="ts">
	import { onDestroy } from "svelte";

    let timerValue = '00:00:00';
    let interval: number | undefined;
    let isRunning = false;
    let storedTimerValue = '';

    function toggleTimer() {
        if (isRunning) {
            stopTimer();
            storedTimerValue = timerValue; 
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

    onDestroy(() => {
        clearInterval(interval); 
    });
</script>

<div class="container">
    <ul class="list">
        <li class="list__item">{storedTimerValue}</li>
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
        height: 100vh;
        width: 100%;
    }

    .timer{
        width: 30%;
        margin-left: auto;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        gap: 32px;
    }

    .timer__monitor {
        background: #78A083;
        border-radius: 16px;
        padding: 16px 32px;
        text-align: center;
        font-size: 48px;
        font-family: 'Courier New', Courier, monospace;
        font-weight: 700;
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
    }

    .list {
        background: #78A083;
        border-radius: 16px;
        padding: 16px 32px;
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