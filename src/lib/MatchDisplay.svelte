<svelte:options customElement="match-display"/>

<script>
    import {onMount} from 'svelte';
    import {reorderPlayersBySum, getMostFrequentName} from '../lib/data_utils.js'
    import PlayerName from '../lib/PlayerName.svelte'
    // @ts-ignore
    import PlayerEntry from './PlayerEntry.svelte';

    import Backcity from "../static/backcity.png"

    export let id = "[id]"
    export let version = "[version]"
    export let timestamp = "[timestamp]"
    export let length = "[length]"
    export let gamemode = "[gamemode]"
    export let map = "[map]"
    export let moderator = "[moderator]"
    export let winner = "[winner]"

    export let players = {
        "example_player_1": {
            "FA": 26,
            "Ne": 19
        },
        "example_player_2": {
            "Sw": 16,
            "Sp": 24
        }
    }

    export let rounds = undefined

    let reorderedPlayers = reorderPlayersBySum(players);
    let position = 0

    let mainDiv
    let playersContainer
    let roundsContainer
    let roundsSegment
    
    onMount(() => {
        for (const player in reorderedPlayers){
            position += 1
            let playerEntry = document.createElement("player-entry")
            // @ts-ignore
            playerEntry.name = player
            // @ts-ignore
            playerEntry.data = players[player]
            // @ts-ignore
            playerEntry.position = position
            playersContainer.appendChild(playerEntry)

            if (position == 1 && rounds == undefined){
                winner = player
                roundsSegment.style.display = "none"
            }

            if (rounds != undefined){
                winner = getMostFrequentName(rounds)
            }
        }

        if (rounds != undefined){
            // @ts-ignore
            for (let round  = 0; round < rounds.length; round++) {
                let playername = document.createElement("player-name")
                let p = document.createElement("p")
                let div = document.createElement("div")
                div.style.display = "flex"
                div.style.flexDirection = "row"
                div.style.justifyContent = "left"
                div.style.alignItems = "center"
                div.style.marginBottom = "10px"
                div.style.fontSize = "14px"
                p.style.margin = "0"
                p.style.marginRight = "10px"
                p.textContent = (round + 1) + "."
                // @ts-ignore
                playername.name = rounds[round]
                div.appendChild(p)
                div.appendChild(playername)
                roundsContainer.appendChild(div)
            }
        }
    })

</script>

<div class="match-display" style="background-color: lightgray" bind:this={mainDiv}>
    <div class="header-bar">
        <div style="width: 50%">
            {id} • {version}
        </div>
        <div style="width: 50%; font-size: 14px">
            {gamemode}
        </div>
        <div style="width: 50%; font-size: 14px">
            {map}
        </div>
        <div style="width: 40%; font-size: 13px">
            M: <PlayerName name={moderator}/> 
        </div>
        <div style="width: 50%;">
            <p style="font-size: 13px;">{timestamp} ({length})</p>
        </div>
    </div>
    <div style="padding: 15px;">
        <div id="winner-tag"><p style="margin: 0; margin-right: 10px">Won by:</p> <PlayerName name={winner}/></div>
        <div class="horiz-separator"></div>

        <div style="display: flex; flex-direction: row; justify-content: center; align-items: center; color: #444444; font-size: 14px">
            <p style="width: 3%"></p>
            <p style="width: 19%">Username</p>
            <p style="width: 11%">Kills</p>
            <p style="width: 100%">(per Class)</p>
            <p style="width: 5%">pts.</p>
        </div>
        <div id="players-container" bind:this={playersContainer}></div>

        <div id="rounds-segment" bind:this={roundsSegment}>
            <div class="horiz-separator" style="border-color: #AAAAAA; margin-top: 15px"></div>
            <p style="font-size: 14px; color: #444444; margin-left: 20px"> Rounds</p>
            <div id="rounds-container" style="margin-left: 20px"bind:this={roundsContainer}></div>
        </div>
    </div>
</div>

<style>
    .match-display {
        /* background-color: lightgray; */
        background-size: cover;
        background-position: center;
        width: 85%;
        margin-bottom: 15px;
        border-radius: 10px;
    }

    .header-bar {
        height: 25px;
        background-color: gray;
        color: white;
        padding: 5px;
        padding-left: 10px;
        display: flex;
        flex-direction: row;
        align-items: center;
        border-top-left-radius: 10px;
        border-top-right-radius: 10px;
    }

    #players-container {
        display: flex;
        flex-direction: column;
        align-items: stretch;
    }

    #winner-tag {
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: center;
        width: 100%;
        margin-bottom: 10px;
    }

    .horiz-separator {
        width: 55%;
        border: solid 2px gray;
        margin: 0 auto;
    }
</style>