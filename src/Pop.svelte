<script>
  import { Howl } from 'howler'
  import { currentWaifu } from './waifu/CurrentWaifu'
  import { userPopCount } from './pop/UserPopCount'
  import { formatNumber } from './utils/formatter'

  const NORMAL = 'normal'
  const POP = 'pop'
  let showing = NORMAL

  const preloadImage = new Image()
  $: if (preloadImage.src !== $currentWaifu.imgPopUrl) preloadImage.src = $currentWaifu.imgPopUrl

  let popAudioSrc = ''
  let normalAudioSrc = ''
  $: if (popAudioSrc !== $currentWaifu.audioPopUrl) popAudioSrc = $currentWaifu.audioPopUrl
  $: if (normalAudioSrc !== $currentWaifu.audioNormalUrl) normalAudioSrc = $currentWaifu.audioNormalUrl
  $: popAudioPlayer = new Howl({ src: [popAudioSrc], loop: false, autoplay: false, preload: true })
  $: normalAudioPlayer = new Howl({ src: [normalAudioSrc], loop: false, autoplay: false, preload: true })
  function playPopAudio () {
    if (popAudioPlayer.state() !== 'loaded') return
    popAudioPlayer.play()
  }
  function playNormalAudio () {
    if (normalAudioPlayer.state() !== 'loaded') return
    normalAudioPlayer.play()
  }

  function handleInputDown () {
    if (showing === POP) return
    showing = POP

    playPopAudio()
    userPopCount.add(1)
  }
  function handleInputUp () {
    if (showing === NORMAL) return
    showing = NORMAL

    playNormalAudio()
  }
</script>

<svelte:window on:keydown={handleInputDown} on:keyup={handleInputUp} />
<div class="d-flex justify-content-center pop"
  on:mousedown={handleInputDown}
  on:mouseup={handleInputUp}
  on:touchstart={(event) => {
    handleInputDown()
    event.preventDefault()
  }}
  on:touchend={(event) => {
    handleInputUp()
    event.preventDefault()
  }}
>
  <div class="pop-count-num">
    {formatNumber($userPopCount)}
  </div>
  <div class="top-div"></div>
  {#if showing === NORMAL}
    <img src="{$currentWaifu.imgNormalUrl}" alt="normal img" />
  {:else if showing === POP}
    <img src="{$currentWaifu.imgPopUrl}" alt="pop img" />
  {/if}
</div>

<style>
  .pop {
    position: relative;
    height: 100vh;
    padding-top: 52px;
  }

  .pop img {
    height: 100%;
  }

  .pop-count-num {
    position: absolute;
    font-size: 6rem;
    font-weight: 900;
    text-align: center;
    color: #fff;
    -webkit-text-stroke-width: 0.3rem;
    -webkit-text-stroke-color: #000;
    font-family: "Nunito";
    word-break: break-word;
    user-select: none;
  }

  .top-div {
    position: absolute;
    width: 100%;
    height: 100vh;
  }
</style>
