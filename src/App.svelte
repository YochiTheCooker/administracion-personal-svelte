<script>
	import TransForm from "./componentes/TransForm.svelte";
	import "./app.css"

// queria hacer un header y tener todo los elementos de la app en varios 
// componentes pero al final termine haciendo todo en uno solo pude
// haberlo hecho mejor y mas legible pero cuando me di cuenta pense que
// tardaria mas en acomodar todo el codigo que terminar la app en si.

</script>

<!-- aqui puse el fondo y coloque el componente en un div diferente para que la interfaz se llamativa y este en el centro -->

<main class="bg-zinc-900 text-white h-screen flex justify-center items-center"> 
	<div class="bg-zinc-800 p-10 rounded-lg flex">
		<TransForm/>
	</div>
</main>

<style>
</style>