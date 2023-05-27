<script>
  import Item from './items.svelte';
  let motRechercher = '';
  let items = [];
  let questions = [];

  const handleKeyPress = (event) => {
    if (event.key === 'Enter') {
      fetchData();
    }
  };

  const normalizeString = (str) => {
    return str.normalize('NFD').replace(/[\u0300-\u036f]/g, '');
  };

  const fetchData = () => {
    items = [];
    fetch(
      'https://raw.githubusercontent.com/Phil1010/quiz-exo7-stage2023/main/catalogue.json'
    )
      .then((response) => response.json())
      .then((data) => {
        const searchString = normalizeString(
          motRechercher.trim().replace(/\s+/g, ' ')
        );
        const regex = new RegExp(searchString, 'gi');
        data.forEach((element, index) => {
          const normalizedText = normalizeString(element.texte);
          if (regex.test(normalizedText) && searchString !== '') {
            const newItem = { texte: element.texte, num: index };
            items = [...items, newItem];
          }
        });
      });
  };

  const moveItemUp = (item) => {
    const currentIndex = questions.indexOf(item);
    if (currentIndex > 0) {
      questions = [
        ...questions.slice(0, currentIndex - 1),
        item,
        questions[currentIndex - 1],
        ...questions.slice(currentIndex + 1),
      ];
    }
  };

  const moveItemDown = (item) => {
    const currentIndex = questions.indexOf(item);
    if (currentIndex < questions.length - 1) {
      questions = [
        ...questions.slice(0, currentIndex),
        questions[currentIndex + 1],
        item,
        ...questions.slice(currentIndex + 2),
      ];
    }
  };

  const exportAMC = () => {
    console.log(
      'APPEL EXPORT AMC cette fonction va fetcher et concatener les questions des fichier AMC de github dans un fichier .amc'
    );
  };
</script>

<main>
  <div class="row">
    <div class="col s6">
      <input
        type="text"
        bind:value={motRechercher}
        on:keypress={handleKeyPress}
        placeholder="Entrer un mot clé"
      />
    </div>
    <div class="col s6">
      <button class="btn" on:click={fetchData}>
        <i class="material-icons">search</i>
      </button>
    </div>
  </div>

  <div class="row">
    <div class="col s3">
      <h6>Résultats de la recherche :</h6>
      <div class="card white z-depth-3">
        <div
          class="card-content"
          style="height: calc(100vh - 30px); overflow: scroll"
        >
          <div class="items">
            {#each items as item}
              <div class="row valign-wrapper">
                <div class="col s10">
                  <Item {item} />
                </div>
                <div class="col s2 right-align">
                  <button
                    class="btn-floating btn-small waves-effect"
                    on:click={() => {
                      questions = [...questions, item];
                      if (questions.includes(item)) {
                        // supprimer l'affichage de l'item
                        items = items.filter((element) => element !== item);
                      }
                    }}
                  >
                    +
                  </button>
                </div>
              </div>
            {/each}
          </div>
        </div>
      </div>
    </div>
    <div class="col s9">
      <div class="container">
        <h6>Questions sélectionnées :</h6>

        <div class="card white z-depth-3">
          <div
            class="card-content"
            style="height: calc(70vh - 30px); overflow: scroll"
          >
            <div class="questions">
              {#each questions as item}
                <div class="row valign-wrapper">
                  <div class="col s10">
                    <Item {item} />
                  </div>
                  <div class="col s1">
                    <button
                      class="btn-floating btn-small waves-effect"
                      on:click={() => moveItemUp(item)}
                    >
                      <i class="material-icons">keyboard_arrow_up</i>
                    </button>
                  </div>
                  <div class="col s1">
                    <button
                      class="btn-floating btn-small waves-effect"
                      on:click={() => moveItemDown(item)}
                    >
                      <i class="material-icons">keyboard_arrow_down</i>
                    </button>
                  </div>
                </div>
              {/each}
            </div>
          </div>
        </div>
        <h6>Options :</h6>
        <button class="btn" on:click={() => exportAMC()}>Exporter en AMC</button
        >
      </div>
    </div>
  </div>
</main>

<style>
  /* Styles here */
</style>
