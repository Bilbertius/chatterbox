<style>
    #heading {
        font-family: "Bungee Outline", cursive;
        text-transform: uppercase;
        font-size: 60px;
        font-weight: bold;
        margin: 0;
        top: 0;
        left: -6px;
        color: #52ed99;
        mix-blend-mode: screen;
        position: relative;
        white-space: nowrap;
        -webkit-filter: blur(1px);
        -moz-filter: blur(1px);
        filter: blur(1px);

    }
    #heading:after,
    #heading:before {
        content: attr(data-text);
        position: absolute;
        mix-blend-mode: screen;
    }
    #heading:after {
        top: 0;
        left: 6px;
        color: #00ba62;
        -webkit-animation: move 1.5s infinite;
        -moz-animation: move 1.5s infinite;
        -o-animation: move 1.5s infinite;
        animation: move 1.5s infinite;
    }
    #heading:before {
        top: -2.4px;
        left: 3.6px;
        color: #00aa65;
    }
    @media (min-width: 375px) {
        #heading {
            font-size: 50px;
        }
    }

</style>

<script>
    export let text;
</script>

<div id="heading">{text}</div>