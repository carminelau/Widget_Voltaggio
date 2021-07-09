<svelte:head>
  <script src={"https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.9.4/Chart.js"}>
    </script>
</svelte:head>

<style type="text/css">

        .mainContainerStyle {
            position: relative;
        }

        .chartContainerStyle {
            height: 300px;
            width: 50%;
            display:table;
            padding-top: 100px;
            padding-left: 300px;
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
        .select {
            position: relative;
            display: flex;
            width: 20em;
            height: 3em;
            line-height: 3;
            background: greenyellow;
            overflow: hidden;
            border-radius: .25em;
        }
        select {
            flex: 1;
            padding: 0 .5em;
            color: darkgreen;
            cursor: pointer;
        }
        /* Arrow */
        .select::after {
            content: '\25BC';
            position: absolute;
            top: 0;
            right: 0;
            padding: 0 1em;
            background: greenyellow;
            cursor: pointer;
            pointer-events: none;
            -webkit-transition: .25s all ease;
            -o-transition: .25s all ease;
            transition: .25s all ease;
        }
        /* Transition */
        .select:hover::after {
            color: darkgreen;
        }

        .form-style-3{
            max-width: 450px;
            font-family: "Lucida Sans Unicode", "Lucida Grande", sans-serif;
        }
        .form-style-3 label{
            display:block;
            margin-bottom: 10px;
        }
        .form-style-3 label > span{
            float: left;
            width: 400px;
            color: darkgreen;
            font-weight: bold;
            font-size: 13px;
            text-shadow: 1px 1px 1px #fff;
        }
        .form-style-3 fieldset{
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
        .form-style-3 fieldset legend{
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

        #my-legend{
            display:inline-block;
            float: right;
            padding-top:150px;
        }
        .my-legend .legend-title {
            text-align: left;
            margin-bottom: 5px;
            font-weight: bold;
            font-size: 90%;
        }
        .my-legend .legend-scale ul {
            margin: 0;
            margin-bottom: 5px;
            padding: 0;
            float: left;
            list-style: none;
        }
        .my-legend .legend-scale ul li {
            font-size: 80%;
            list-style: none;
            margin-left: 0;
            line-height: 18px;
            margin-bottom: 2px;
        }
        .my-legend ul.legend-labels li span {
            display: block;
            float: left;
            height: 16px;
            width: 30px;
            margin-right: 5px;
            margin-left: 0;
            border: 1px solid #999;
        }
        .my-legend .legend-source {
            font-size: 70%;
            color: #999;
            clear: both;
        }
        .my-legend a {
            color: #777;
        }
    </style>

<script>
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

            for( var k=0; k < risultato.length; k++){
                if((centralina_selected===risultato[k].id)&&(day_selected===risultato[k].timestamp)){
                    if((((risultato[k])["1"]).alimentazione)==="corrente"){
                        valori_asse_y.push(1);

                    }else{
                        valori_asse_y.push(0);
                    }

                    if((((risultato[k])["1"]).alimentazione)==="corrente"){
                        var inizio_p1= risultato[k]["1"].inizio;
                        var fine_p1= risultato[k]["1"].fine;
                        valori_asse_x.push(inizio_p1.substr(17,18)+' - '+fine_p1.substr(17,18));
                    }else{
                        var inizio_p2= risultato[k]["1"].inizio;
                        var fine_p2= risultato[k]["1"].fine;
                        valori_asse_x.push(inizio_p2.substr(17,18)+' - '+fine_p2.substr(17,18));
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
            for( var k=0; k < risultato.length; k++){
                if((centralina_selected===risultato[k].id)&&(day_selected===risultato[k].timestamp)){
                    if((((risultato[k])["1"]).alimentazione)==="corrente"){
                        valori_asse_y.push(1);

                    }else{
                        valori_asse_y.push(0);
                    }
                    if((((risultato[k])["1"]).alimentazione)==="corrente"){
                        var inizio_p1= risultato[k]["1"].inizio;
                        var fine_p1= risultato[k]["1"].fine;
                        valori_asse_x.push(inizio_p1.substr(17,18)+' - '+fine_p1.substr(17,18));
                    }else{
                        var inizio_p2= risultato[k]["1"].inizio;
                        var fine_p2= risultato[k]["1"].fine;
                        valori_asse_x.push(inizio_p2.substr(17,18)+' - '+fine_p2.substr(17,18));
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

<main>

<div id="mainContainer" class="mainContainerStyle">
    <div class="form-style-3" id="setup1" >
        <fieldset><legend>Setup 1</legend>
            <form action="" method="post" name="modulo1">
                <label><span>Seleziona una centralina:<select name="chartType" id="chartType" class="select-field" onchange="aggiornagrafico()"></select></span></label>
            </form>
        </fieldset>
    </div>
    <div class="form-style-3" id="setup2">
        <fieldset><legend>Setup 2</legend>
            <form action="" method="post" name="modulo2">
                <label><span>Seleziona un giorno:<select name="dayType" id="dayType" class="select-field" onchange="aggiornagrafico()"></select></span></label>
            </form>
        </fieldset>
    </div>
    <div id="graphContainer" class="chartContainerStyle">
        <canvas id="chartContainer" ></canvas>
    </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.9.4/Chart.js"></script>

</main>