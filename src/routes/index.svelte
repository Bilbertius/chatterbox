
<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
        overflow: hidden;
    }
    body {
        font: 13px "Noto Sans";
        background: hsl(240, 90%, 20%);
    }
    .main {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        align-items: center;
        height: 100vh;
    }
    form {
        margin: auto auto 0 auto;
        background:  inherit;
        border-bottom-radius: 15px;

        padding: 20px;

        width: 100%;
        display: flex;
        justify-content: space-around;
        align-self: flex-end;
        box-shadow: 0px 45px 25px inset hsla(240, 100%, 63%, 0.48);
    }
    form input {
        border: 0;
        padding: 20px;
        width: 70%;
        border-radius: 10px;
        background: inherit;
        color: hsl(146, 100%, 50%);
        font-size: 1.2em;
        box-shadow: 3px 3px 6px inset hsl(240, 94%, 19%), -3px -3px 6px inset hsla(240, 90%, 71%,.6);

    }
    @media screen and (device-aspect-ratio: 375/667) {
        form input {
            font-size: 16px;
        }
    }
    form button {

        background: inherit;
        border: none;
        color: hsl(146, 100%, 50%);
        font-weight: bolder;
        font-size: 1.2em;
        width: 20%;
        border-radius: 15px;

        box-shadow: 3px 3px 6px hsl(240, 94%, 10%), -3px -3px 6px hsl(240, 100%, 81%), -2px -2px 3px inset hsl(240, 94%, 15%), 2px 2px 3px inset hsla(240, 90%, 71%,.6);;
    }
    #chatWindow {
        height: 450px;
        width: 80%;
        box-shadow: -10px -10px 20px hsla(240, 100%, 60%, 0.7),
        10px 10px 20px hsl(240, 100%, 12%),
        4px 4px 2px inset hsla(240, 100%, 60%, 0.7),
        -4px -4px 2px inset hsl(240, 100%, 27%);
        overflow: auto;

        padding-top: 15px;
        border-radius: 25px;
        display: flex;
        flex-flow: column nowrap;
        justify-content: space-between;
    }
    @media (min-height: 845px) {
        #chatWindow {
            height: 600px;
        }
    }
    #messages {
        align-self: center;
        list-style-type: none;
        margin: 0;
        padding: 0;
        font-size: 1.2em;
        width: 100%;
    }
    #messages li {
        padding: 15px 10px;
        border-radius: 1em;
        margin: 1.5em 20px 1.5em auto;
        width: 75%;
        background: hsl(240, 83%, 37%);
        color: #c1ceff;
        overflow-wrap: break-word;
        box-shadow: 5px 5px 10px hsla(240, 100%, 60%, 0.7),
        -5px -5px 10px hsl(240, 100%, 15%),
        -2px -2px 4px  inset hsla(240, 100%, 60%, 0.7),
        2px 2px 4px inset  hsl(240, 100%, 15%);
    }
    #messages li:nth-child(odd) {
        box-shadow: 2px 2px 6px inset hsla(240, 100%, 60%, 0.7),
        -2px -2px 6px inset hsl(240, 100%, 15%),
        -5px -5px 10px  hsla(240, 100%, 60%, 0.7),
        5px 5px 10px hsl(240, 100%, 15%);

        background: hsl(240, 83%, 32%);
        color: #fff8b8;

        margin: 1.5em auto 1.5em 20px;
    }
    ::placeholder {
        color: #b1e6de;
    }
    #numUsers {
        color: #fff8b8;
        padding: 1em;
        font-size: 14px;
    }

    #atlarge {
        display:flex;
        flex-flow: column nowrap;
    }
</style>
<svelte:head>
    <title>chatterbox</title>
    <link
            href="https://fonts.googleapis.com/css?family=Bungee+Outline|Noto+Sans&display=swap"
            rel="stylesheet" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</svelte:head>

<svelte:window on:unload={emitUserDisconnect}/>

<script>
    import io from "socket.io-client";
    import { fade } from "svelte/transition";

    import Heading from "../components/Heading.svelte";
    const socket = io();
    const placeholder = "Type your message here...";
    const greeting = `You have joined the chat. Use '/nick your_nickname' to set your nickname!`
    let messages = [greeting];
    let message = "";
    let name = `Guest${numUsersConnected}`;
    let numUsersConnected = 0;
    socket.on("message", function(message) {
        messages = messages.concat(message);
        updateScroll();
    });

    socket.on("user joined", function({message, numUsers}) {
        messages = messages.concat(message);
        numUsersConnected = numUsers;
        updateScroll();
    });
    socket.on("user left", function(numUsers) {
        numUsersConnected = numUsers;
        updateScroll();
    });
    function emitUserDisconnect() {
        socket.emit('user disconnect', name);
    }
    function handleSubmit() {
        message = message.trim();

        if (message == '') {
            return;
        }
        let messageString = `${name}: ${message}`;
        if (message.slice(0, 5) == '/nick') {
            let newName = message.slice(6);
            messageString = `Server: ${name} changed their nickname to ${newName}`;
            name = newName;
        }
        messages = messages.concat(messageString);
        socket.emit("message", messageString);
        updateScroll();
        message = "";
    }

    function updateScroll() {
        const chatWindow = document.getElementById('chatWindow');
        setTimeout(() => {
            chatWindow.scrollTop = chatWindow.scrollHeight;
        }, 0);
    }
</script>

<body>
<div class="main">
    <Heading text={'Chat App'} />
    <div id="atlarge">

    <div id="chatWindow">
        <ul id="messages">
            {#each messages as message}
                <li transition:fade>{message}</li>
            {/each}
        </ul>
    </div>
    <form action="">
        <input id="m" autocomplete="off" {placeholder} bind:value={message} />
        <button on:click|preventDefault={handleSubmit}>Send</button>
    </form>
    </div>
    <p id="numUsers">There {numUsersConnected == 1 ? 'is' : 'are'} {numUsersConnected} {numUsersConnected == 1 ? 'user' : 'users'} currently chatting!</p>
</div>
</body>