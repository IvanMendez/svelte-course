<script lang="ts">
    import AuthCheck from "$lib/components/AuthCheck.svelte";
    import { user, userData, storage, db } from "$lib/firebase";
    import { doc, updateDoc } from "firebase/firestore";
    import { getDownloadURL, ref, uploadBytes } from "firebase/storage";
  
    let previewURL: string;
    let uploading = false;
    let error: boolean;
    $: href = `/${$userData?.username}/edit`;
  
    async function upload(e: any) {
      uploading = true;
      error = false;
      const file = e.target.files[0];
      // 3.0 MB file size
      if (file.size > 3000000) {
        uploading = false;
        error = true;
        return
      }

      previewURL = URL.createObjectURL(file);
      const storageRef = ref(storage, `users/${$user!.uid}/profile.png`);
      const result = await uploadBytes(storageRef, file);
      const url = await getDownloadURL(result.ref);

      await updateDoc(doc(db, "users", $user!.uid), { photoURL: url });
      uploading = false;
    }
  </script>
  
  <AuthCheck>
    <main class="max-w-xl mx-auto text-center">
    <h2 class="mx-2 text-2xl font-bold mt-8 mb-4 text-center">Foto de perfil</h2>
  
    <form class="max-w-screen-md w-full">
      <div class="form-control w-full max-w-xs my-10 mx-auto text-center">
        <img
          src={previewURL ?? $userData?.photoURL ?? "/user.png"}
          alt="photoURL"
          width="256"
          height="256"
          class="mx-auto"
        />
        <label for="photoURL" class="label">
          <span class="label-text">Selecciona una imagen</span>
        </label>
        <input
          on:change={upload}
          name="photoURL"
          type="file"
          class="file-input file-input-bordered w-full max-w-xs"
          accept="image/png, image/jpeg, image/gif, image/webp"
        />
        {#if uploading}
          <p>Uploading...</p>
          <progress class="progress progress-info w-56 mt-6" />
        {/if}
        {#if error}
        <p class="text-warning text-sm">Tamaño máximo de 3.0 MB, por favor intenta con una imágen de menor tamaño.</p>
        {/if}
      </div>
    </form>
  
    <a {href} class="btn btn-primary"> Volver </a>
    </main>
  </AuthCheck>
  