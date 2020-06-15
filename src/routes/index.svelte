
<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
        overflow: hidden;
    }
    body {
        font: 13px "Noto Sans";
        background: hsl(240, 83%, 37%);
    }
    .main {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        height: 100vh;
        padding: 35px;
    }
    form {
        margin: 10px auto auto auto;
        background:  inherit;
        border-radius: 15px;

        padding: 20px;
        align-items: center;
        width: 100%;
        display: flex;
        justify-content: space-around;
        align-self: flex-end;
        background-color: hsl(230, 83%, 40%);
        box-shadow: -2px -2px 2px hsl(240, 83%, 60%),
        2px 2px 2px hsl(240, 83%, 20%),
        4px 4px 2px inset hsl(240, 83%, 60%),
        -4px -4px 2px inset hsl(240, 83%, 20%);

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
            font-size: 1em;
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
        padding: 15px;
        transition: box-shadow 500ms ease-in;
        box-shadow: 3px 3px 6px hsl(240, 83%, 20%), -3px -3px 6px hsl(240, 83%, 60%), -2px -2px 3px inset hsl(240, 83%, 20%), 2px 2px 3px inset hsla(240, 83%, 60%,.6);;
    }

    form button:active {
        box-shadow: 3px 3px 6px inset  hsl(240, 83%, 20%),
        -3px -3px 6px inset hsl(240, 83%, 60%),
        -2px -2px 3px inset hsl(240, 83%, 20%),
        2px 2px 3px inset hsla(240, 83%, 60%,.6);
        outline: none;


    }
    form button:focus, form button:hover {
        outline: none;
    }
    #chatWindow {
        height: 450px;
        width: 100%;
        background-color: hsl(230, 83%, 40%);
        box-shadow: -2px -2px 2px hsl(240, 83%, 60%),
        2px 2px 2px hsl(240, 83%, 20%),
        4px 4px 2px inset hsl(240, 83%, 60%),
        -4px -4px 2px inset hsl(240, 83%, 20%);
        overflow: auto;
        scrollbar-track-color: hsl(220, 90%, 60%);
        padding-top: 15px;
        border-radius: 25px;
       flex-grow: 1;
    }
    /* width */
    ::-webkit-scrollbar {
        width: 20px;
    }

    /* Track */
    ::-webkit-scrollbar-track {
        background: #17176c;

        border-radius: 15px;
        box-shadow:  -1px -1px 5px inset hsl(240, 83%, 60%), 1px 1px 5px inset hsl(240, 83%, 20%);
    }

    /* Handle */
    ::-webkit-scrollbar-thumb {
        background: hsl(240, 83%, 37%);
        border-radius: 15px;
        box-shadow:  1px 1px 5px inset hsl(240, 83%, 60%), -1px -1px 5px inset hsl(240, 83%, 20%);
    }

    /* Handle on hover */
    ::-webkit-scrollbar-thumb:hover {
        background: hsl(240, 83%, 37%);
        box-shadow:  2px 2px 5px inset hsl(240, 83%, 65%), -2px -2px 5px inset hsl(240, 83%, 15%);
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
        padding: 8px 8px;
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


</style>
<svelte:head>
    <title>chatterbox</title>
    <link
            href="https://fonts.googleapis.com/css?family=Bungee+Outline|Noto+Sans&display=swap"
            rel="stylesheet" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</svelte:head>
<svelte:window on:unload={emitUserDisconnect}></svelte:window>

<script>
    import Heading from '../components/Heading.svelte';

    import io from "socket.io-client";
    import { fade } from "svelte/transition";

    let socket = io();
    let placeholder = "Enter messsage";
    let greeting = 'You have joined the chat '


    let message = '';
    let messages = [greeting];
    let name = "guest";
    let numUsersConnected = 0;

    socket.on("message", function(message)  {
        messages = messages.concat(message);
        updateScroll();
    });

    socket.on("user joined", function({message, numUsers}) {
        messages = messages.concat(message);
        numUsersConnected = numUsers;
        updateScroll();
    });
    socket.on("user left", function (numUsers)  {
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
        <Heading text={'Chatterbox'} />
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
</body>