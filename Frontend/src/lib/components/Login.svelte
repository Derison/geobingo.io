<script>
  import { browser } from "$app/environment";
  import NameChange from "$lib/components/NameChange.svelte";
  import SignIn from "$lib/components/SignIn.svelte";
  import { api } from "$lib/js/api";

  let lobbyToJoin = "";
  let code = "";
  const setRoom = () => {
    if (browser) {
      let url = new URL(window.location.href);
      code = url.searchParams.get("code");

      console.log("code", code);
      if (code) {
        lobbyToJoin = code;
      }
    }
  };

  let privateLobby = false;
  setRoom();
</script>

<div class="w-5/12 shadow-lg bg-base-100 rounded-box flex h-72">
  <div class="w-1/2 p-10">
    <div class="relative">
      <input
        type="text"
        placeholder="Code"
        bind:value={lobbyToJoin}
        class="w-full select-auto input border-primary-focus"
      />
      <button
        on:click={() => api.player.join(lobbyToJoin)}
        class="btn btn-primary absolute right-0">join</button
      >
    </div>

    <label class="cursor-pointer label mt-2">
      <span class="label-text">hide link to game</span>
      <input
        type="checkbox"
        on:click={() => (privateLobby = !privateLobby)}
        checked={privateLobby}
        class="toggle"
      />
    </label>

    <div class="w-full mt-8">
      <SignIn bind:code />
    </div>
    {#if !$api.player.twitch}
      <NameChange />
    {/if}
  </div>
  <div class="w-1/2 p-10">
    <button
      on:click={() => {
        if (api.player) {
          console.log(navigator.language || navigator.userLanguage);
          const lang = (navigator.language || navigator.userLanguage).slice(
            0,
            2
          );
          // maybe get languages from backend in the future
          console.log(lang);
          $api.player.host(privateLobby, "game", lang);
        }
      }}
      class="btn btn-primary w-full"
    >
      Play with friends/chat
    </button>
    <div class="pt-4 relative">
      <span class="indicator-item badge badge-info absolute right-2 -mt-2"
        >BETA</span
      >
      <button
        on:click={() => {
          if (api.player) {
            $api.player.host(privateLobby, "MMGame");
          }
        }}
        class="btn btn-primary w-full h-fit"
      >
        join public game
      </button>
    </div>
  </div>
</div>
