<script>

    /* NON MODIFICARE -> INIZIO */ 
    import { createEventDispatcher } from 'svelte';
    import Voltaggio from './Voltaggio.svelte';
    export let visible = false;
	const dispatch = createEventDispatcher();
    const showResult = () => dispatch("showResult"); 
    const showError = (text) => dispatch("showError", {text}); 
    const showMaintenance = (text) => dispatch("showMaintenance", {text}); 
    const showLoading = (text) => dispatch("showLoading", {text}); 
    const showProgressBar = (text, value=0) => dispatch("showProgressBar", {text, value});
    const updateProgressBar = (text, value=0) => dispatch("updateProgressBar", {text, value}); 
    /* NON MODIFICARE -> FINE */ 

    /* ESEMPIO FUNZIONAMENTO ->  INIZIO */
    import { Button } from 'svelte-materialify';
    setTimeout(() => {
        showResult();
    }, 1000);
    /* ESEMPIO FUNZIONAMENTO ->  FINE */

</script>

<main style={visible ? "" : "display: none"}>

    <Voltaggio />

</main>

<style>

    main {
        width: 100%;
        height: 100%;
    }

</style>