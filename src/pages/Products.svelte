<script lang="ts">
  import { spreadsheetId } from "../constantes";

  import { useNavigate } from "svelte-navigator";
  import { sheetsApi } from "../axios/sheetsApi";
  let { access_token } = localStorage;
  // titulo,precio,imagen,disponible
  let product = ["", 0, "", true];
  let gclient = google.accounts.oauth2.initTokenClient({
    client_id:
      "1047629314149-f5sp9o7ol8ta174r41hur8untntt188j.apps.googleusercontent.com",
    scope: "https://www.googleapis.com/auth/spreadsheets",
    callback: (token) => {
      localStorage.access_token = token.access_token;
      access_token = localStorage.access_token;
      sheetsApi.defaults.params = {
        access_token,
      };
    },
  });
  console.log({ google });
  $: if (access_token) {
    loadProducts();
  }
  let products: typeof product[] = [];
  let productosCopy: typeof product[] = [];
  async function loadProducts() {
    const range = "!A:D";
    try {
      const { data } = await sheetsApi.get(`/${spreadsheetId}/values/${range}`);
      console.log("notes: ", { data });
      data.values.shift();
      products = data.values;
      productosCopy = data.values;
    } catch (error) {
      console.warn(error.message);
      localStorage.removeItem("access_token");
    }
  }
  function oauth2Login() {
    gclient.requestAccessToken();
  }
  function createProd() {
   navigate("prod") 
  }
  const navigate = useNavigate();
  function logout() {
    localStorage.removeItem("access_token");
    localStorage.removeItem("spreadsheetId");
    access_token = null;
    // spreadsheetId = null
  }
  let buscar = "";
  function buscarKeyup() {
    products = productosCopy.filter((p) => p[0].includes(buscar));
  }
</script>

{#if !access_token}
  <h2>Auth with google</h2>
  <button on:click={oauth2Login}>Login</button>
{:else}
  <button on:click={logout}>Cerrar session</button>
  <hr />
  <h3>Productos</h3>
  <input
    type="text"
    placeholder="Buscar"
    bind:value={buscar}
    on:keyup={buscarKeyup}
  />
  <div class="grid">
    {#each products as prod, i}
      <div
        class="prod"
        on:click={() => navigate("prod", { state: { id: i + 2, prod } })}
      >
        <img src={prod[2]} />
        <h3>{prod[0]}</h3>
        <h3>Precio: Lps. {prod[1]}.00</h3>
      </div>
    {/each}
  </div>
{/if}

<button class="floating" on:click={createProd}>
  <svg
    xmlns="http://www.w3.org/2000/svg"
    fill="none"
    viewBox="0 0 24 24"
    stroke-width="1.5"
    stroke="currentColor"
    class="w-6 h-6"
  >
    <path
      stroke-linecap="round"
      stroke-linejoin="round"
      d="M12 4.5v15m7.5-7.5h-15"
    />
  </svg>
</button>

<style>
  input {
    display: block;
    margin: 0.5rem;
  }
  .prod {
    background-color: #333;
    padding: 0.5rem;
    border-radius: 0.5rem;
    width: 15rem;
  }
  .prod img {
    width: 13rem;
  }
  .grid {
    display: grid;
    margin-top: 1rem;
    width: 80vw;
    grid-template-columns: repeat(auto-fit, minmax(15rem, 1fr));
    gap: 0.5rem;
  }
  .floating {
    position: fixed;
    padding: 0.5rem;
    border-radius: 1.5rem;
    width: 3rem;
    height: 3rem;
    bottom: 2rem;
    right: 2rem;

  }
</style>
