<script>
  import Header from '$lib/header.svelte'

  let searchFor = ''
  let definitionFromAPI
  let isSearchTextError = false
  let isDefinitionFound = false
  let isDefinitionNotFound = false
  let isAPIError = false

  const NO_DEFINITION_FOUND = 'No Definitions Found'

  const captureKeystroke = (event) => {
    isSearchTextError = false
    if (event.key === 'Enter') {
      searchForDefinition()
    }
  }

  const validateSearchFor = () => {
    isAPIError = false
    isSearchTextError = false
    isDefinitionFound = false
    isDefinitionNotFound = false
    if (searchFor === null || searchFor === undefined || searchFor === '') {
      isSearchTextError = true
      isDefinitionNotFound = true
      return
    }

    const searchWords = searchFor.split(' ')
    isSearchTextError = (searchWords.length === 1) ? false : true
    if (isSearchTextError) {
      return
    }
  }

  const searchForDefinition = () => {
    validateSearchFor()
    if (isSearchTextError) {
      return
    }
    fetch(`https://api.dictionaryapi.dev/api/v2/entries/en/${ searchFor }`)
      .then((response) => response.json())
      .then((data) => {
        definitionFromAPI = data
        if (data.title === NO_DEFINITION_FOUND) {
          isDefinitionFound = false
          isDefinitionNotFound = true
          return
        }
        isDefinitionFound = true
        isDefinitionNotFound = false
        console.log(data)
      })
      .catch((error) => {
        console.log('Error: ', error)
        isAPIError = true
      })
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
  <h2 class="mt-4 text-2xl font-bold">Find the meaning of a word</h2>
  <div class="mt-4 ml-8 text-lg">
    <div class="mt-4">
      <label for="searchFor">
        Search for the definition of:
        <input class="pl-1 border w-32" id="searchFor" name="searchFor" type="search"
        bind:value={ searchFor } on:input={ searchFor } on:keydown={ captureKeystroke }>
      </label>

      {#if isSearchTextError} 
        <div class="pt-4 pb-4 text-red-500">
          Please enter a word to search for before clicking 'Get Definition'
        </div>
      {/if}

      {#if isAPIError}
        <div class="pt-4 pb-4 text-red-500">
          An error occurred trying to locate your word.
        </div>
      {/if}

      {#if isDefinitionNotFound}
        <div class="pt-4 pb-4 text-red-500">
          { definitionFromAPI.message }
        </div>
      {/if}

      <div class="flex flex-col items-center mt-4 w-1/6 bg-green-500 border border-black rounded-lg">
        <button class="font-bold" on:click={ searchForDefinition }>Get Definition</button>
      </div>

      {#if isDefinitionFound}
        {#each definitionFromAPI as definition}
          {#each definition.meanings as meaning, index }
            <div class="flex flex-col mt-4 mb-4">
              <div class="font-bold">
                {index}. { meaning.partOfSpeech}
              </div>
              <div class="ml-8">
                {#each meaning.definitions as item }
                  <div>
                    - { item.definition }
                  </div>
                {/each}
              </div>
            </div>
          {/each}
        {/each}
      {/if}
    </div>
  </div>
</section>