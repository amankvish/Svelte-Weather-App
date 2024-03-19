<!-- src/routes/+layout.svelte -->
<script>
  import "../app.css";

  import Navbar from "../components/Navbar.svelte";
  // @ts-ignore
  import Weather from "../components/Weather.svelte.svelte";
  import Footer from "../components/Footer.svelte";
</script>

<div>
  <Navbar />
  <Weather/>
  <!-- <main>
    <slot></slot>
  </main> -->
  <Footer />
</div>
