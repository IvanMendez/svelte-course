<script lang="ts">
      import { page } from "$app/stores";
      import { auth, userData } from "$lib/firebase";
      import { signOut } from "firebase/auth";
      const view_href = `/${$userData?.username}/`;
      const edit_href = `/${$userData?.username}/edit`;
      const bio_href = `/${$userData?.username}/bio`;

      async function signOutSSR() {
    const res = await fetch("/api/signin", { method: "DELETE" });
        await signOut(auth);
    }
</script>

{#if $userData?.username == $page.params.username && $page.route['id'] == "/[username]"}
<nav class="flex justify-center my-6">
    <ul class="steps">
        <a href={edit_href} class="btn btn-secondary btn-xs absolute right-4 top-4">Edit Profile</a>
    </ul>
</nav>
{:else if $userData?.username == $page.params.username}
  <nav class="flex justify-center my-6">
    <ul class="steps">
      <a href = {view_href} class="btn btn-ghost normal-case text-xl">Ver perfil</a>
      <a href = {edit_href} class="btn btn-ghost normal-case text-xl">Editar perfil</a>
      <a href = {bio_href} class="btn btn-ghost normal-case text-xl">Editar biografía</a>
      <button class="btn btn-outline btn-xs absolute right-4 top-4" on:click={signOutSSR}>Sign Out</button>
    </ul>
  </nav>
{/if}
  
<main class="max-w-xl mx-auto">
    <slot />
</main>
