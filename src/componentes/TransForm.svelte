<script>   
    export let descripcion = '';
    export let monto = null;
    export let tipos = '';
    export let transacciones = [];
    export let fecha = '';

    //UUID es una libreria de node para generar identificadores únicos:

    import {v4} from 'uuid'     
    import {createEventDispatcher} from 'svelte';

    // aqui uso la reactividad para guardar transacciones desde el localStorage
    // y se ejecuta automáticamente cuando se carga el componente y cada vez que  se cambia transacciones:

    $:{
        transacciones = localStorage.getItem('transacciones')
        ? JSON.parse(localStorage.getItem('transacciones')) 
        : [];
    }
    // aqui creo un dispatcher de eventos para comunicar cambios a otros componentes:

    const dispatch = createEventDispatcher()

    // esta función es para manejar las actualizaciones de transacciones
    // se actualiza una transacción existente en el array y se guarda en localStorage

    function handleUpdate(e){
        const {id, descripcion, monto, tipo, fecha} = e.detail;
        const index = transacciones.findIndex(transaccion => transaccion.id === id);
        if (index !== -1) {
            transacciones[index] = { id, descripcion, monto, tipo, fecha};
            transacciones = [...transacciones]; 
        }
        localStorage.setItem('transacciones', JSON.stringify(transacciones));
    };

    // esta función para actualiza cambios en una transacción (cuando se edita en el historial) y lo guarda en localStorage
    function handleChange(id, descripcion, monto, tipos, fecha){
        dispatch('uptade', {
            id: id,
            descripcion: descripcion,
            monto: monto,
            tipos: tipos,
            fecha: fecha,
        });
        localStorage.setItem('transacciones', JSON.stringify(transacciones));
    };

    // esta función elimina una transacción
    // filtra el array de transacciones para eliminar la que coincide con el id dado y luego lo guarda en localStorage

    function handleRemove(id) {
        transacciones = transacciones.filter(transaccion => transaccion.id !== id);
        localStorage.setItem('transacciones', JSON.stringify(transacciones));
    };
     
    // esta función es para agregar una nueva transacción
    // se activa al hacer el submit y no permite agregar la transacción si los campos estan vacios

    function addTransaccion(e) {
        e.preventDefault()
        const id = v4();

        if (!descripcion || monto === null || !tipos || !fecha) {
            alert("Por favor, complete todos los campos antes de añadir una transacción.");
            return;
        };

        const transaccion = {
            id: id,
            descripcion: descripcion,
            monto: monto !== null ? parseFloat(monto).toFixed(2) : '0.00',
            tipos: tipos,
            fecha: fecha,
        };
        transacciones = [transaccion, ...transacciones];
        localStorage.setItem('transacciones', JSON.stringify(transacciones));
        descripcion = '';
        monto = null;
        tipos = '';
        fecha = '';
    };

    // estas son las funciones de que recuperan el value de los inputs

    function setDescripcion(e){
        descripcion = e.target.value
    };
    function setMonto(e){
        monto = parseFloat(e.target.value)
    };
    function setTime(e){
        fecha = e.target.value
    };
</script>
    

<div>
    <form on:submit={addTransaccion}> 
        <span>
        <!-- estos son los inputs para la descripción, monto y fecha, vinculados a sus respectivas variables y funciones -->
            <input
                class="border border-indigo-700 bg-zinc-600 text-white px-3 py-2 rounded-lg "  
                type="text" placeholder="Ingresa una descripcion"
                value={descripcion}
                on:input={setDescripcion}
            /> 
            <input
                class="border border-indigo-700 bg-zinc-600 text-white px-3 py-2 rounded-lg"  
                type="number" placeholder="$ 00.00" step="0.01"
                value={monto}
                on:input={setMonto}
            /> 
            <input
                class="border border-indigo-700 bg-zinc-600 text-white px-3 py-2 rounded-lg"  
                type="date"
                value={fecha}
                on:input={setTime}
            /> 
        </span>
            <!-- Botones de selección de tipo de transacción (me costo mucho siendo tan simple de hacer aprendi mucho) -->
            <input
                class="w-8" 
                type="radio" 
                value="ingreso" 
                bind:group={tipos} 
            /> 
            Ingreso
            <input 
                class="w-8"
                type="radio" 
                value="egreso" 
                bind:group={tipos} 
            /> 
            Egreso
           <!-- Botón para añadir la transacción -->
            <button class="bg-indigo-700 text-white px-3 py-2 rounded-lg block mb-2 mt-3 w-full">
                Añadir
            </button>
    </form>
            <!-- historial de transacciones -->
    <div> 
        <h3 class="text-center font-semibold mb-2">Historial:</h3>
        {#each transacciones as {descripcion, monto, id, tipos, fecha}}

                 <!-- cada transacción se renderiza dentro de una lista con sus datos -->

            <list class="bg-zinc-600 text-white text-sm rounded-lg px-1 py-1 mb-1 mt-1 flex justify-center items-center" on:update={handleUpdate}>

                <!-- Inputs para editar la descripción, monto y fecha de la transacción -->

                <input
                    class="bg-zinc-600 text-white rounded-lg px-1 py-1 w-full"
                    type="text" 
                    placeholder="editar" 
                    bind:value={descripcion} 
                    on:change={handleChange}/>

                <!-- aqui se muestra el monto de la transacción con dos decimales(creo que era obligatorio)y el $ del dolar-->
                <!-- originalmente iba a hacer un input donde podrias cambiar el monto pero no sabia como colocarle el signo si que viera raro-->
                <h3 
                    class="bg-zinc-600 text-white rounded-lg px-1 py-1 w-full">
                    $ {parseFloat(monto).toFixed(2)}
                </h3>

                <input 
                    class="bg-zinc-600 rounded-lg" 
                    bind:value={fecha}
                />
                     <!-- selector para cambiar el tipo de transacción -->

                    <label  class="rounded-lg px-1 py-1 w-full">
                    <select
                         class="bg-zinc-600"
                        on:change={handleChange}
                        bind:value= {tipos}> 
                        <option value="ingreso">Ingreso</option>
                        <option value="egreso">Egreso</option>
                    </select>
                    </label>

                    <!-- Botón para eliminar la transacción -->

                <button  class="bg-indigo-700 text-white rounded-lg px-1 py-1 w-full"on:click={() => handleRemove(id)}>
                    Eliminar
                </button>
            </list>
        
        {/each}  
    </div>
</div>

<style>     
</style>