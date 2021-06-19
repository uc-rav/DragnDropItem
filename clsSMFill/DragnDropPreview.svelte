<script>
    import { onMount } from 'svelte';
import DragnDrop from './DragnDrop.svelte';
    import { XMLToJSON } from './HelperAI.svelte';

    let myData = document.getElementById("special_module_xml").value;
    let obj = XMLToJSON(myData);
    let string = obj.smxml.text.__cdata; 
    let array = string.match(/[%{](.*?)[%]/g);
    console.log("preview section", array);
    let newString = string.replace(/[%{](.*?)[%]/g,`<span 
    class="textarea" style="margin: 5px; border: 1px dashed grey; 
    background-color: #f1f1f1; width: 150px; height: 40px;
    padding: 5px 20px;"></span>`);

    let listItem = [];
    onMount(() => {
        let DragnDrop_Footer = document.querySelector("#DragnDrop_Footer");
        DragnDrop_Footer.addEventListener("drop",() => { drop(event) });
        DragnDrop_Footer.addEventListener("dragover",() => { allowDrop(event)});
        listItem = document.querySelectorAll('.textarea');
        for(let i = 0 ; i < listItem.length ; i++) {
            listItem[i].setAttribute("id",`input${i}`);
            document.getElementById(`input${i}`).addEventListener("drop",() => { drop(event) });
            document.getElementById(`input${i}`).addEventListener("dragover",() => { allowDrop(event)});
            console.log(listItem[i]);
        }

        let spanList = [];
        for(let i = 0 ; i < array.length ; i++) {
            spanList[i] = document.createElement('span');
            spanList[i].setAttribute("id",`span${i}`);
            spanList[i].style = "cursor: pointer; margin: 5px; border: 1px solid grey; background-color: #f1f1f1; padding: 5px 20px;";
            spanList[i].setAttribute("draggable",true);
            DragnDrop_Footer.appendChild(spanList[i]);
            spanList[i].innerHTML = array[i].replace(/[^a-zA-Z]/g,"");
            console.log("spanlist",spanList);
            document.getElementById(`span${i}`).addEventListener("dragstart",() => { drag(event) })
        }

        //logic for drag and drop begin
        function allowDrop(ev) {
            ev.preventDefault();
        }
        function drag(ev) {
            ev.dataTransfer.setData("text",ev.target.id);
        }
        function drop(ev) {
            ev.preventDefault();
            var data = ev.dataTransfer.getData("text");
            ev.target.appendChild(document.getElementById(data));
        }
        //logic for drag and drop ends
    });

    function checkAnswer() {
        for(let i = 0 ; i < listItem.length ; i++) {
            console.log("inside drag box",document.getElementById(`input${i}`).innerText);
            console.log("inside drop box",array[i]);
            // console.log("conditions",array[i] !== `%{${document.getElementById(`span${i}`).innerHTML}}%`);
            if(array[i] !== `%{${document.getElementById(`input${i}`).innerText}}%`) {
                listItem[i].style.border = "2px solid red";
            } else {
                listItem[i].style.border = "2px solid green";
            }
        }
    }
    function correctAnswer() {
        for(let i = 0 ; i < listItem.length ; i++) {
            listItem[i].innerHTML = array[i].replace(/[^a-zA-Z]/g,"");
            listItem[i].style.border = "2px solid transparent";
        }
        document.getElementById("checkButton").disabled = true;
    }
</script>

<style>
    #DragnDrop {
        position: fixed;
        top: calc(55% - 150px);
        left: 5rem;
        right: 5rem;
        height: 300px;
        background-color: rgb(255, 255, 255);
        border: 2px solid #d9e7fd;
        border-radius: 5px;
        box-shadow: rgba(0, 0, 0, 0.6) 0px 5px 15px;
    }
    #DragnDrop_Header {
        position: relative;
        top: 0;
        left: 0;
        width: 100%;
        background-color: #d9e7fd;
        height: 2.5rem;
    }
    #DragnDrop_Body {
        outline: 0px solid transparent;
        padding: 0 1rem 0 1rem;
    }
    #DragnDrop_Footer {
        position: absolute;
        bottom: 0;
        left: 0;
        right: 0;
        height: 3.5rem;
        background-color: #d9e7fd;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    button {
        width: 150px;
        height: 2rem;
        border: none;
        padding: 5px;
        margin: 0.20rem 0rem 0rem 0.5rem;
        background-color: #f8f9f5;
        border-radius: 3px;
        border: 1px solid #dee3de;
        box-shadow: rgba(100, 100, 111, 0.2) 0px 0px 8px 0px;
    }
    p {
        line-height: 40px;
     }
</style>

<div id="DragnDrop">
    <div id="DragnDrop_Header">
        <button type="button" on:click={correctAnswer}>Correct Answer</button>
        <button type="button" on:click={checkAnswer} id="checkButton">Review Answer</button>
    </div>
    <div id="DragnDrop_Body">
        <p>{@html newString}</p>
    </div>
    <div id="DragnDrop_Footer">

    </div>
</div>