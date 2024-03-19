<!-- Navbar.svelte -->
<script>
    // Get Date
    const date = new Date();
    const month = date.toLocaleString("default", { month: "long" }).slice(0, 3);
    const dateString = `${date.getDate()} ${month} ${date.getFullYear()}`;
  
    // Get time
    const time = date.toLocaleTimeString("en-US", {
      hour: "numeric",
      minute: "numeric",
      hour12: true,
    });
  </script>
  
  <nav class="p-8 mb-2 w-full backdrop-blur flex h-16 items-center justify-between text-white bg-black/50">
    <h1 class="text-2xl">Weather App</h1>
  
    <div>
      <div>{dateString}</div>
      <div>{time}</div>
    </div>
  </nav>
  