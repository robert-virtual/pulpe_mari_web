<script lang="ts">
  import { Router, Route, Link } from "svelte-navigator";
  import Products from "./pages/Products.svelte";
  import Product from "./pages/Product.svelte";
</script>

<Router>
  <header>
    <nav>
      <Link to="/">Productos</Link>
    </nav>
  </header>
  <main>
    <Route path="/">
      <Products />
    </Route>
    <Route path="prod">
      <Product />
    </Route>
  </main>
</Router>

