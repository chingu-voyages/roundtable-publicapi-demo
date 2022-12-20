<script>
  import Header from '$lib/header.svelte'

  let searchFor = 'slither'

  const validateSearchFor = (event) => {
    
  }

  const searchForDefinition = () => {
    fetch(`https://api.dictionaryapi.dev/api/v2/entries/en/${ searchFor }`)
      .then((response) => response.json())
      .then((data) => console.log(data));
  }
</script>

<Header/>

<section class="pt-20 ml-6 mr-8">
  <h2 class="mt-4 text-2xl font-bold">How to use a Public API in your web apps</h2>
  <div class="mt-4 text-xl">
    This app was built to provide a test platform to show how to use a public
    API in your web apps. It utilizes the <a class="underline italic text-blue-500" target="_blank noreferrer" 
    href="https://dictionaryapi.dev/">Free Dictionary API</a> to retrieve
    word definitions.
  </div>
  <div class="mt-4 text-xl">
    This will be referenced in an upcoming weekly Chingu Roundtable in our
    <a class="underline italic text-blue-500" target="_blank noreferrer" 
      href="https://www.youtube.com/playlist?list=PLmnPfbM4_HyszkLRkLtlUgoMCHer3jpg7">YouTube channel.</a>
  </div>
</section>

<!-- Mock Checkout Form -->
<section class="ml-6 mr-8">
  <h2 class="mt-4 text-2xl font-bold">Mock Checkout Form</h2>
  <div class="mt-4 ml-8 text-lg">
    <div class="mt-4">
      <label for="searchFor">
        Search for the definition of:
        <input class="border w-32" id="searchFor" name="searchFor" type="search"
        bind:value={ searchFor } on:input={ searchFor } on:click={ validateSearchFor }>
      </label>
      <div class="flex flex-col items-center mt-4 w-1/6 bg-green-500 border rounded-lg">
        <button class="font-bold" on:click={ searchForDefinition }>Checkout</button>
      </div>
    </div>
  </div>
</section>