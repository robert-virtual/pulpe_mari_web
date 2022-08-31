<script lang="ts">
  import { sheetsApi } from "../axios/sheetsApi";

  import { useLocation} from "svelte-navigator";
  import { spreadsheetId } from "../constantes";
  // titulo,precio,imagen,disponible
  let product = ["", 0, "", true];

  let id: number | undefined;
  const location = useLocation();
  if ($location.state.prod && $location.state.id) {
    console.log("there is state");
    product = $location.state.prod;
    id = $location.state.id;
  }
  console.log(!$location.state);
  async function saveProduct() {
    const range = "!A:D";
    const { data } = await sheetsApi.post(
      `/${spreadsheetId}/values/${range}:append`,
      {
        values: [product],
      },
      {
        params: {
          valueInputOption: "USER_ENTERED",
        },
      }
    );
    console.log("append: ", { data });
  }

  async function updateProduct() {
    const range = `!A${id}:D${id}`;
    sheetsApi.put(
      `/${spreadsheetId}/values/${range}`,
      {
        values: [product],
      },
      {
        params: {
          valueInputOption: "USER_ENTERED",
        },
      }
    );
  }
</script>

<div class="prod">
  <img src={product[2]} />
  <label>
    Nombre de producto
    <input
      type="text"
      bind:value={product[0]}
      placeholder="Nombre de producto"
    />
  </label>
  <label>
    Precio de producto
    <input
      type="number"
      bind:value={product[1]}
      placeholder="Precio de producto"
    />
  </label>
  <label>
    Imagen de producto
    <input
      type="text"
      bind:value={product[2]}
      placeholder="Imagen de producto"
    />
  </label>
  <label>
    Producto disponible
    <input type="checkbox" bind:checked={product[3]} />
  </label>
  <button on:click={id ? updateProduct : saveProduct}>Guardar</button>
  <hr />
</div>

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
</style>
