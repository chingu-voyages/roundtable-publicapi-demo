<script>
  import Header from '$lib/header.svelte'

  let searchFor = ''
  let wordDefinition
  let isDefinitionFound = false 
  let isSearchTextError = false
  let isError = false

  let errorMessage
  const ERR_INVALID_SEARCH_TEXT = "Please enter a single word to search for before clicking 'Get Definition'"
  const ERR_API_ERROR = "An error occurred in the API. Error: "

  const captureKeystroke = (event) => {
    isSearchTextError = false
    if (event.key === 'Enter') {
      searchForDefinition()
    }
  }

  const validateSearchWord = () => {
    isError = false
    if (searchFor === null || searchFor === undefined || searchFor === '') {
      isError = true
      errorMessage = ERR_INVALID_SEARCH_TEXT
      return
    }

    const searchWords = searchFor.split(' ')
    isSearchTextError = (searchWords.length === 1) ? false : true
    if (isSearchTextError) { // Search text must be exactly one word
      isError = true
      errorMessage = ERR_INVALID_SEARCH_TEXT
      return
    }
  }

  const searchForDefinition = async () => {
    isDefinitionFound = false
    validateSearchWord()
    if (isSearchTextError) {
      return
    }
    fetch(`https://api.dictionaryapi.dev/api/v2/entries/en/${ searchFor }`)
      .then(async (response) => {
        wordDefinition = await response.json()
        if (response.status === 404) {
          isDefinitionFound = false
          isError = true
          errorMessage = wordDefinition.message
          return
        }
        isDefinitionFound = true
        console.log('response: ', response, ' \nwordDefinition: ', wordDefinition)
      })
      .catch((error) => {
        isError = true
        errorMessage = ERR_API_ERROR.concat(error)
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

      {#if isError}
        <div class="pt-4 pb-4 text-red-500">
          { errorMessage }
        </div>
      {/if}

      <div class="flex flex-col items-center mt-4 w-1/6 bg-green-500 border border-black rounded-lg">
        <button class="font-bold" on:click={ searchForDefinition }>Get Definition</button>
      </div>

      {#if isDefinitionFound}
        {#each wordDefinition as definition}
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