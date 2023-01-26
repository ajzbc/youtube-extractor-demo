<script lang="ts">
  let searchInput: string;
  let data: any;

  async function search(input: string) {
    const videoID = youtube_parser(input);

    if (videoID === false) {
      alert("Invalid URL");
      return;
    }

    const reponse = await fetch(
      `https://youtube-extractor.ajzbc.workers.dev/?id=${videoID}`
    );
    let json = await reponse.json();

    if (json.playabilityStatus.status == "OK") {
      data = json;
    } else {
      data = null;
      alert("Unexpected status. Check console for data");
    }

    console.log(data);
  }

  // https://stackoverflow.com/a/9102270
  function youtube_parser(url: string): string | false {
    var regExp =
      /^.*(youtu\.be\/|v\/|u\/\w\/|embed\/|watch\?v=|\&v=)([^#\&\?]*).*/;
    var match = url.match(regExp);
    if (match && match[2].length == 11) {
      return match[2];
    } else {
      return false;
    }
  }

  search("https://www.youtube.com/watch?v=dQw4w9WgXcQ");
</script>

<div class="flex flex-col w-full gap-12">
  <form
    on:submit|preventDefault={() => search(searchInput)}
    class="w-full flex flex-col"
  >
    <label for="search">Youtube Link</label>
    <input
      id="search"
      type="text"
      class="w-full border py-2 px-4"
      placeholder="https://www.youtube.com/watch?v=dQw4w9WgXcQ"
      bind:value={searchInput}
    />
  </form>
  {#if data}
    <div class="flex flex-col gap-4">
      <div class="p-2 bg-neutral-200">
        <span class="flex mb-1">
          <h2 class="text-xl font-extrabold">Basic Data</h2>
        </span>
        <div class="flex">
          <div>
            <p class="font-bold">
              Title: <span class="font-mono font-normal"
                >{data.videoDetails.title}</span
              >
            </p>
            <p class="font-bold">
              Channel: <span class="font-mono font-normal"
                >{data.videoDetails.author}</span
              >
            </p>
            <p class="font-bold">
              Views: <span class="font-mono font-normal"
                >{data.videoDetails.viewCount}</span
              >
            </p>
          </div>
          <img
            src={data.videoDetails.thumbnail.thumbnails[0].url}
            alt="video thumbnail"
          />
        </div>
      </div>
      <div class="p-2 bg-neutral-200">
        <span class="flex justify-between items-center mb-1">
          <h2 class="text-xl font-extrabold">Raw Data</h2>
          <a
            target="_blank"
            rel="noreferrer"
            href={`https://youtube-extractor.ajzbc.workers.dev/?id=${data.videoDetails.videoId}`}
            >Open</a
          >
        </span>
        <pre class="max-h-[30rem] overflow-scroll">{JSON.stringify(
            data,
            null,
            2
          )}</pre>
      </div>
    </div>
  {/if}
</div>
