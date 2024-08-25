<script>
    const respuestasData = JSON.parse(localStorage.getItem('respuestasData')) || {
        cliente: {},
        proveedor: {}
    };

    function saveData() {
        localStorage.setItem('respuestasData', JSON.stringify(respuestasData));
    }

    document.getElementById("tipoInteraccion").addEventListener("change", function() {
        const tipo = this.value;
        const momentoSelect = document.getElementById("momentoConversacion");
        momentoSelect.disabled = !tipo;
        momentoSelect.innerHTML = `<option value="">-- Selecciona --</option>`;

        if (tipo) {
            Object.keys(respuestasData[tipo]).forEach(function(momento) {
                const option = document.createElement("option");
                option.value = momento;
                option.textContent = momento.charAt(0).toUpperCase() + momento.slice(1);
                momentoSelect.appendChild(option);
            });
        }

        document.getElementById("respuestas").disabled = true;
        document.getElementById("respuestas").innerHTML = "";
    });

    document.getElementById("momentoConversacion").addEventListener("change", function() {
        const tipo = document.getElementById("tipoInteraccion").value;
        const momento = this.value;
        const respuestasSelect = document.getElementById("respuestas");
        respuestasSelect.disabled = !momento;
        respuestasSelect.innerHTML = "";

        if (momento) {
            respuestasData[tipo][momento].forEach(function(respuesta) {
                const option = document.createElement("option");
                option.value = respuesta;
                option.textContent = respuesta;
                respuestasSelect.appendChild(option);
            });
        }
    });

    function copiarRespuesta() {
        const respuestaSeleccionada = document.getElementById("respuestas").value;
        if (respuestaSeleccionada) {
            navigator.clipboard.writeText(respuestaSeleccionada).then(function() {
                alert("Respuesta copiada: " + respuestaSeleccionada);
            });
        } else {
            alert("Por favor, selecciona una respuesta.");
        }
    }

    function agregarRespuesta() {
        const tipo = document.getElementById("tipoInteraccion").value;
        const momento = document.getElementById("momentoConversacion").value;
        const nuevaRespuesta = document.getElementById("nuevaRespuesta").value.trim();

        if (!tipo) {
            alert("Por favor, selecciona el tipo de interacción.");
            return;
        }
        if (!momento) {
            const nuevoMomento = document.getElementById("nuevoMomento").value.trim();
            if (!nuevoMomento) {
                alert("Por favor, selecciona o agrega un ítem.");
                return;
            }
            respuestasData[tipo][nuevoMomento] = [];
            const momentoSelect = document.getElementById("momentoConversacion");
            const option = document.createElement("option");
            option.value = nuevoMomento;
            option.textContent = nuevoMomento.charAt(0).toUpperCase() + nuevoMomento.slice(1);
            momentoSelect.appendChild(option);
            momentoSelect.value = nuevoMomento;
        }

        const finalMomento = momento || document.getElementById("nuevoMomento").value.trim();
        if (!respuestasData[tipo][finalMomento]) {
            respuestasData[tipo][finalMomento] = [];
        }

        if (nuevaRespuesta) {
            respuestasData[tipo][finalMomento].push(nuevaRespuesta);
            const respuestaSelect = document.getElementById("respuestas");
            const option = document.createElement("option");
            option.value = nuevaRespuesta;
            option.textContent = nuevaRespuesta;
            respuestaSelect.appendChild(option);
            document.getElementById("nuevaRespuesta").value = '';
            saveData();
        }
    }

    function eliminarItem() {
        const tipo = document.getElementById("tipoInteraccion").value;
        const momento = document.getElementById("momentoConversacion").value;

        if (tipo && momento && confirm("¿Estás seguro de que deseas eliminar este ítem y todas sus respuestas?")) {
            delete respuestasData[tipo][momento];
            const momentoSelect = document.getElementById("momentoConversacion");
            momentoSelect.remove(momentoSelect.selectedIndex);
            momentoSelect.value = "";
            document.getElementById("respuestas").innerHTML = "";
            document.getElementById("respuestas").disabled = true;
            saveData();
        }
    }

    function eliminarRespuesta() {
        const tipo = document.getElementById("tipoInteraccion").value;
        const momento = document.getElementById("momentoConversacion").value;
        const respuestaSeleccionada = document.getElementById("respuestas").value;

        if (tipo && momento && respuestaSeleccionada && confirm("¿Estás seguro de que deseas eliminar esta respuesta?")) {
            const respuestas = respuestasData[tipo][momento];
            respuestasData[tipo][momento] = respuestas.filter(respuesta => respuesta !== respuestaSeleccionada);
            const respuestaSelect = document.getElementById("respuestas");
            respuestaSelect.remove(respuestaSelect.selectedIndex);
            saveData();
        }
    }
</script>
