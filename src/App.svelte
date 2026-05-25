<script lang="ts">
  import { onMount } from "svelte";
  import "./app.css";

  interface Shortcut {
    name: string;
    url: string;
  }

  let time = $state("");
  let date = $state("");
  let greeting = $state("");
  let searchQuery = $state("");

  let shortcuts: Shortcut[] = $state(
    JSON.parse(localStorage.getItem("shortcuts") || "[]") as Shortcut[],
  );

  if (shortcuts.length === 0) {
    shortcuts = [
      { name: "GitHub", url: "https://github.com" },
      { name: "YouTube", url: "https://youtube.com" },
      { name: "Reddit", url: "https://reddit.com" },
      { name: "Svelte", url: "http://localhost:5173" },
    ];
  }

  function updateTime() {
    const now = new Date();
    time = now.toLocaleTimeString([], { hour: "2-digit", minute: "2-digit" });
    date = now.toLocaleDateString([], {
      weekday: "long",
      month: "long",
      day: "numeric",
    });

    const hours = now.getHours();
    if (hours < 12) greeting = "Good morning";
    else if (hours < 18) greeting = "Good afternoon";
    else greeting = "Good evening";
  }

  function handleSearch(e: SubmitEvent) {
    e.preventDefault();
    if (searchQuery.trim()) {
      window.location.href = `https://google.com/search?q=${encodeURIComponent(searchQuery)}`;
    }
  }

  onMount(() => {
    updateTime();
    const interval = setInterval(updateTime, 1000);
    return () => clearInterval(interval);
  });

  $effect(() => {
    localStorage.setItem("shortcuts", JSON.stringify(shortcuts));
  });
</script>

<main class="dashboard-container">
  <!-- Header Section -->
  <header class="header">
    <h1 class="clock">{time}</h1>
    <p class="date">{date}</p>
    <p class="greeting">{greeting}, user.</p>
  </header>

  <!-- Search Component -->
  <form onsubmit={handleSearch} class="search-form">
    <div class="search-wrapper">
      <input
        type="text"
        bind:value={searchQuery}
        placeholder="Search Google..."
        autofocus
        class="search-input"
      />
      <button type="submit" class="search-button">
        <svg
          xmlns="http://w3.org"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="2"
          stroke="currentColor"
          class="search-icon"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M21 21l-5.197-5.197m0 0A7.5 7.5 0 105.196 5.196a7.5 7.5 0 0010.607 10.607z"
          />
        </svg>
      </button>
    </div>
  </form>

  <!-- Links Grid Section -->
  <section class="shortcuts-section">
    <h2 class="section-title">Quick Links</h2>
    <div class="grid">
      {#each shortcuts as shortcut}
        <a href={shortcut.url} class="card">
          <div class="avatar">
            {shortcut.name.charAt(0).toUpperCase()}
          </div>
          <span class="card-name">{shortcut.name}</span>
        </a>
      {/each}
    </div>
  </section>
</main>

<style>
  .dashboard-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    padding: 1.5rem;
  }

  .header {
    text-align: center;
    margin-bottom: 2rem;
  }

  .clock {
    font-size: 4.5rem;
    font-weight: 800;
    letter-spacing: -0.05em;
    margin-bottom: 0.5rem;
  }

  .date {
    font-size: 0.875rem;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    color: var(--text-secondary);
    margin-bottom: 0.25rem;
  }

  .greeting {
    font-size: 1.25rem;
    color: var(--text-muted);
    font-weight: 300;
  }

  .search-form {
    width: 100%;
    max-width: 36rem;
    margin-bottom: 3rem;
  }

  .search-wrapper {
    position: relative;
    display: flex;
    align-items: center;
  }

  .search-input {
    width: 100%;
    background-color: var(--input-bg);
    border: 1px solid var(--card-border);
    border-radius: var(--radius-xl);
    padding: 1rem 3.5rem 1rem 1.5rem;
    color: var(--text-primary);
    font-size: 1rem;
    outline: none;
    transition: var(--transition);
    backdrop-filter: blur(8px);
  }

  .search-input:focus {
    border-color: var(--accent-color);
    box-shadow: 0 0 0 4px rgba(99, 102, 241, 0.15);
  }

  .search-button {
    position: absolute;
    right: 1.25rem;
    background: none;
    border: none;
    color: var(--text-muted);
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: var(--transition);
  }

  .search-button:hover {
    color: var(--accent-color);
  }

  .search-icon {
    width: 1.25rem;
    height: 1.25rem;
  }

  .shortcuts-section {
    width: 100%;
    max-width: 42rem;
  }

  .section-title {
    font-size: 0.75rem;
    text-transform: uppercase;
    font-weight: 600;
    color: var(--text-muted);
    letter-spacing: 0.05em;
    margin-bottom: 1rem;
    padding-left: 0.5rem;
  }

  .grid {
    display: grid;
    grid-template-columns: repeat(3, minmax(0, 1fr));
    gap: 1rem;
  }

  @media (min-width: 640px) {
    .grid {
      grid-template-columns: repeat(4, minmax(0, 1fr));
    }
  }

  .card {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 1rem;
    background-color: var(--card-bg);
    border: 1px solid var(--card-border);
    border-radius: var(--radius-md);
    transition: var(--transition);
  }

  .card:hover {
    background-color: var(--card-hover-bg);
    border-color: var(--card-hover-border);
    transform: translateY(-1px);
  }

  .avatar {
    width: 2.5rem;
    height: 2.5rem;
    border-radius: 0.5rem;
    background-color: var(--card-hover-bg);
    color: var(--text-secondary);
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
    font-size: 1.125rem;
    margin-bottom: 0.5rem;
    transition: var(--transition);
  }

  .card:hover .avatar {
    background-color: rgba(99, 102, 241, 0.1);
    color: var(--accent-color);
  }

  .card-name {
    font-size: 0.75rem;
    color: var(--text-secondary);
    font-weight: 500;
    max-width: 100%;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    transition: var(--transition);
  }

  .card:hover .card-name {
    color: var(--text-primary);
  }
</style>
