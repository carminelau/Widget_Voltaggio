<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.9.4/Chart.js">

    /* NON MODIFICARE -> INIZIO */ 
    import { createEventDispatcher } from 'svelte';
    import Line from 'svelte-chartjs/src/Line.svelte';
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

    var risultato;
        var lista_centraline;
        var lista_giorni;
        var centralina_selected;
        var day_selected;
        var valori_asse_x=[];
        var valori_asse_y=[];


        var data = new FormData();
        data.append("apikey", "WDBNX4IUF66C");

        fetch("https://sqd.sensesquare.eu:5001/voltage", {
            method: "POST",
            body:  data
        }).then(function(response){
            return response.json();
        }).then(function(data){
            risultato = data.result; //lista di centraline e relative misurazioni


            //costruisco lista centraline
            lista_centraline=[];
            for (var i=0;i<risultato.length;i++){
                var id_centralina=risultato[i].id;
                var checklist=lista_centraline.includes(id_centralina);
                if(checklist===false){
                    lista_centraline.push(id_centralina);
                    //alert(lista_centraline);
                    var ct=document.getElementById("chartType");
                    var co=document.createElement('option');
                    co.setAttribute("value",id_centralina);
                    co.innerHTML = id_centralina;
                    ct.appendChild(co);
                }

            }

            lista_giorni=[];
            for (var j=0;j<risultato.length;j++){
                var timestamp_centralina_provvisorio=risultato[j].timestamp;
                var timestamp_centralina=timestamp_centralina_provvisorio.substr(4,12);
                var checklist_t=lista_giorni.includes(timestamp_centralina);
                if(checklist_t===false){
                    lista_giorni.push(timestamp_centralina);
                    //alert(lista_centraline);
                    var ct_time=document.getElementById("dayType");
                    var co_time=document.createElement('option');
                    ct_time.setAttribute("value",timestamp_centralina_provvisorio);
                    co_time.innerHTML = timestamp_centralina_provvisorio;
                    ct_time.appendChild(co_time);
                }

            }


        });













        window.onload =  function () {

            centralina_selected = risultato[0].id;
            day_selected=risultato[0].timestamp;
            valori_asse_x=[];
            valori_asse_y=[];


            var count;

            for( var k=0; k < risultato.length; k++){
                if((centralina_selected===risultato[k].id)&&(day_selected===risultato[k].timestamp)){
                    count=Object.keys(risultato[k]).length;
                    count=count-3;
                    for(var i=1;i<=count;i++){

                        if((((risultato[k])[i]).alimentazione)==="corrente"){
                            valori_asse_y.push(1);
                            var inizio_p1= (risultato[k])[i].inizio;
                            var fine_p1= (risultato[k])[i].fine;
                            valori_asse_x.push(inizio_p1.substr(17,18)+' - '+fine_p1.substr(17,18));

                        }else{
                            if((((risultato[k])[i]).alimentazione)==="batteria"){
                                valori_asse_y.push(0);
                                var inizio_p2= (risultato[k])[i].inizio;
                                var fine_p2= (risultato[k])[i].fine;
                                valori_asse_x.push(inizio_p2.substr(17,18)+' - '+fine_p2.substr(17,18));
                            }

                        }

                    }

                }

            }


            var xValues = valori_asse_x;
            var yValues = valori_asse_y;

            new Chart("chartContainer", {
                type: "line",
                data: {
                    labels: xValues,
                    datasets: [{
                        fill: false,
                        lineTension: 0,
                        backgroundColor: "rgba(0,0,255,1.0)",
                        borderColor: "rgba(0,0,255,0.1)",
                        data: yValues
                    }]
                },
                options: {
                    legend: {display: false},
                    scales: {
                        yAxes: [{ticks: {min: 0, max:1}}],
                    }
                }
            });

        };



        function updateChart () {

            centralina_selected = (document.getElementById('chartType')).value;
            day_selected = (document.getElementById('dayType')).value;
            valori_asse_x=[];
            valori_asse_y=[];
            var count;

            for( var k=0; k < risultato.length; k++){
                if((centralina_selected===risultato[k].id)&&(day_selected===risultato[k].timestamp)){
                    count=Object.keys(risultato[k]).length;
                    count=count-3;
                    for(var i=1;i<=count;i++){

                        if((((risultato[k])[i]).alimentazione)==="corrente"){
                            valori_asse_y.push(1);
                            var inizio_p1= (risultato[k])[i].inizio;
                            var fine_p1= (risultato[k])[i].fine;
                            valori_asse_x.push(inizio_p1.substr(17,18)+' - '+fine_p1.substr(17,18));

                        }else{
                            if((((risultato[k])[i]).alimentazione)==="batteria"){
                                valori_asse_y.push(0);
                                var inizio_p2= (risultato[k])[i].inizio;
                                var fine_p2= (risultato[k])[i].fine;
                                valori_asse_x.push(inizio_p2.substr(17,18)+' - '+fine_p2.substr(17,18));
                            }

                        }

                    }

                }

            }






            var xValues = valori_asse_x;
            var yValues = valori_asse_y;

            new Chart("chartContainer", {
                type: "line",
                data: {
                    labels: xValues,
                    datasets: [{
                        fill: false,
                        lineTension: 0,
                        backgroundColor: "rgba(0,0,255,1.0)",
                        borderColor: "rgba(0,0,255,0.1)",
                        data: yValues
                    }]
                },
                options: {
                    legend: {display: false},
                    scales: {
                        yAxes: [{ticks: {min: 0, max:1}}],
                    }
                }
            });

        }

        function aggiornagrafico () {

            document.getElementById("chartContainer").remove();
            var mc=document.getElementById("graphContainer");
            var cc=document.createElement('canvas');
            cc.setAttribute("id","chartContainer");
            mc.appendChild(cc);
            updateChart();


        }

</script>

<main style={visible ? "" : "display: none"}>

    <div id="mainContainer" class="mainContainerStyle">
        <div class="form-style-1" id="setup1" >
            <fieldset><legend>Centralina</legend>
                <form action="" method="post" name="modulo1">
                    <span>Seleziona una centralina:<select name="chartType" id="chartType" class="select-field" onchange="aggiornagrafico()"></select></span>
                </form>
            </fieldset>
        </div>
        <div class="form-style-2" id="setup2">
            <fieldset><legend>Giorno</legend>
                <form action="" method="post" name="modulo2">
                    <span>Seleziona un giorno:<select name="dayType" id="dayType" class="select-field" onchange="aggiornagrafico()"></select></span>
                </form>
            </fieldset>
        </div>
        <!--<div id="graphContainer" class="chartContainerStyle">
            <canvas id="chartContainer" ></canvas>
        </div> -->

        <Line width={60} height={20}/>
    </div>
    
</main>

<style>

    main {
        width: 100%;
        height: 100%;
    }

    .mainContainerStyle {
        position: relative;
    }

    .chartContainerStyle {
        height: 70%;
        width: 70%;
        display:table;
        padding-top: 10%;
        padding-left: 10%;
    }
    /*justify-content: center;*/
    select {
        -webkit-appearance: none;
        -moz-appearance: none;
        outline: 0;
        box-shadow: none;
        border: 0 !important;
        background: white;
        background-image: none;
        border-radius: 5px 5px 5px 5px;
    }
    /* Remove IE arrow */
    select::-ms-expand {
        display: none;
    }
    /* Custom Select */
    
    select {
        flex: 1;
        padding: 0 .5em;
        color: darkgreen;
        cursor: pointer;
    }
    
    .form-style-1{
        width: 30%;
        font-family: "Lucida Sans Unicode", "Lucida Grande", sans-serif;
        font-size:medium;
        margin-right: 4%;
        margin-left: 1%;
        margin-top: 1%;
        position:relative;
    }
    
    .form-style-1 fieldset{
        border-radius: 10px;
        -webkit-border-radius: 10px;
        -moz-border-radius: 10px;
        margin: 0px 0px 10px 0px;
        border: 1px solid darkgreen;
        padding: 20px;
        background: lightgoldenrodyellow;
        box-shadow: inset 0px 0px 15px lightgoldenrodyellow;
        -moz-box-shadow: inset 0px 0px 15px lightgoldenrodyellow;
        -webkit-box-shadow: inset 0px 0px 15px lightgoldenrodyellow;
    }
    .form-style-1 fieldset legend{
        color: darkgreen;
        border-top: 1px solid green;
        border-left: 1px solid green;
        border-right: 1px solid green;
        border-radius: 5px 5px 0px 0px;
        -webkit-border-radius: 5px 5px 0px 0px;
        -moz-border-radius: 5px 5px 0px 0px;
        background: #FFF4F4;
        padding: 0px 8px 3px 8px;
        box-shadow: -0px -1px 2px #F1F1F1;
        -moz-box-shadow:-0px -1px 2px #F1F1F1;
        -webkit-box-shadow:-0px -1px 2px #F1F1F1;
        font-weight: normal;
        font-size: 12px;
    }

    .form-style-2{
        width: 20%;
        font-family: "Lucida Sans Unicode", "Lucida Grande", sans-serif;
        font-size:medium;
        margin-top: 1%;
        position:relative;
    }
    
    .form-style-2 fieldset{
        border-radius: 10px;
        -webkit-border-radius: 10px;
        -moz-border-radius: 10px;
        margin: 0px 0px 10px 0px;
        border: 1px solid darkgreen;
        padding: 20px;
        background: lightgoldenrodyellow;
        box-shadow: inset 0px 0px 15px lightgoldenrodyellow;
        -moz-box-shadow: inset 0px 0px 15px lightgoldenrodyellow;
        -webkit-box-shadow: inset 0px 0px 15px lightgoldenrodyellow;
    }
    .form-style-2 fieldset legend{
        color: darkgreen;
        border-top: 1px solid green;
        border-left: 1px solid green;
        border-right: 1px solid green;
        border-radius: 5px 5px 0px 0px;
        -webkit-border-radius: 5px 5px 0px 0px;
        -moz-border-radius: 5px 5px 0px 0px;
        background: #FFF4F4;
        padding: 0px 8px 3px 8px;
        box-shadow: -0px -1px 2px #F1F1F1;
        -moz-box-shadow:-0px -1px 2px #F1F1F1;
        -webkit-box-shadow:-0px -1px 2px #F1F1F1;
        font-weight: normal;
        font-size: 12px;
    }

    #setup1{
        display:inline-block;
    }
    #setup2{
        display:inline-block;
    }

</style>