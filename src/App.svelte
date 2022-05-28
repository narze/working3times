<script lang="ts">
  import { saveAs } from "file-saver"
  import { copyImageToClipboard } from "copy-image-clipboard"

  import * as htmlToImage from "html-to-image"
  import Kofi from "./lib/Kofi.svelte"
  import Social from "./lib/Social.svelte"
  import dragElement from "./lib/dragElement"
  import { afterUpdate, onMount, tick } from "svelte"
  import pasteImage from "paste-image"
  import GraphemeSplitter from "grapheme-splitter"
  import CircleType from "circletype"
  import Moveable from "svelte-moveable"
  import { Color, ColorInput } from "color-picker-svelte"

  let textColor = new Color("#71CC00")
  let bgColor = new Color("#000000")

  // import Moveable, { OnScale } from "moveable"

  let title = "ทำงาน"
  let url = "https://working3times.narze.live/"

  const curve1 = "M 30, 10 C 130,-25 160,-30 270,-25"
  const curve2 = "M 30, 10 C 130,-30 160,-35 270,-25"
  const curve3 = "M 30, 10 C 130,-35 160,-40 270,-25"
  const underline = "M0,-5 Q60,-20 120,-20 L122,0 Q60,0 3,14 L0,-5"

  const fonts = [
    "Layiji TarMineTine",
    // "Layiji LenRoonRang",
    "FC Catalyst",
    "SOV_RongNang",
    "FCGimmick-Bold",
  ]

  let selectedFont = fonts[0]
  let target

  let frame = {
    matrix: [1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1],
    translate: [0, 0],
    rotate: 0,
    transformOrigin: "50% 50%",
  }

  let avatar,
    fileinput,
    node,
    textNode,
    imageWidth,
    avatarElm,
    svgNode,
    container

  let descriptionSize = 180
  let spacingMicro = 150
  $: spacing = spacingMicro / 10
  let isCopy = false
  let saving = false
  let imageZoom = 90
  // let circleType
  let el: HTMLElement
  const splitter = new GraphemeSplitter()

  const onAvatarLoad = () => {
    imageWidth = avatarElm.width
    // console.dir(avatarElm)
  }

  onMount(async () => {
    dragElement(avatarElm)

    // const moveable = new Moveable(document.body, {
    //   target: document.querySelector(".element") as HTMLElement,
    //   // If the container is null, the position is fixed. (default: parentElement(document.body))
    //   container: document.body,
    //   draggable: true,
    //   resizable: true,
    //   scalable: true,
    //   rotatable: true,
    //   warpable: true,
    //   // Enabling pinchable lets you use events that
    //   // can be used in draggable, resizable, scalable, and rotateable.
    //   pinchable: true, // ["resizable", "scalable", "rotatable"]
    //   origin: true,
    //   keepRatio: true,
    //   // Resize, Scale Events at edges.
    //   edge: false,
    //   throttleDrag: 0,
    //   throttleResize: 0,
    //   throttleScale: 0,
    //   throttleRotate: 0,
    // })

    // const moveable = new Moveable(document.body, {
    //   // If you want to use a group, set multiple targets(type: Array<HTMLElement | SVGElement>).
    //   target: document.querySelector(".element") as HTMLElement,
    //   resizable: true,
    //   draggable: true,
    //   keepRatio: false,
    //   throttleResize: 1,
    //   renderDirections: ["nw", "n", "ne", "w", "e", "sw", "s", "se"],
    //   edge: true,
    //   zoom: 1,
    //   origin: true,
    //   padding: { left: 0, top: 0, right: 0, bottom: 0 },
    // })

    // /* draggable */
    // moveable
    //   .on("dragStart", ({ target, clientX, clientY }) => {
    //     console.log("onDragStart", target)
    //   })
    //   .on(
    //     "drag",
    //     ({
    //       target,
    //       transform,
    //       left,
    //       top,
    //       right,
    //       bottom,
    //       beforeDelta,
    //       beforeDist,
    //       delta,
    //       dist,
    //       clientX,
    //       clientY,
    //     }) => {
    //       console.log("onDrag left, top", left, top)
    //       target!.style.left = `${left}px`
    //       target!.style.top = `${top}px`
    //       // console.log("onDrag translate", dist);
    //       // target!.style.transform = transform;
    //     }
    //   )
    //   .on("dragEnd", ({ target, isDrag, clientX, clientY }) => {
    //     console.log("onDragEnd", target, isDrag)
    //   })

    // /* resizable */
    // moveable
    //   .on("resizeStart", ({ target, clientX, clientY }) => {
    //     console.log("onResizeStart", target)
    //   })
    //   .on(
    //     "resize",
    //     ({ target, width, height, dist, delta, clientX, clientY }) => {
    //       console.log("onResize", target)
    //       delta[0] && (target!.style.width = `${width}px`)
    //       delta[1] && (target!.style.height = `${height}px`)
    //     }
    //   )
    //   .on("resizeEnd", ({ target, isDrag, clientX, clientY }) => {
    //     console.log("onResizeEnd", target, isDrag)
    //   })

    // /* scalable */
    // moveable
    //   .on("scaleStart", ({ target, clientX, clientY }) => {
    //     console.log("onScaleStart", target)
    //   })
    //   .on(
    //     "scale",
    //     ({
    //       target,
    //       scale,
    //       dist,
    //       delta,
    //       transform,
    //       clientX,
    //       clientY,
    //     }: OnScale) => {
    //       console.log("onScale scale", scale)
    //       target!.style.transform = transform
    //     }
    //   )
    //   .on("scaleEnd", ({ target, isDrag, clientX, clientY }) => {
    //     console.log("onScaleEnd", target, isDrag)
    //   })

    // /* rotatable */
    // moveable
    //   .on("rotateStart", ({ target, clientX, clientY }) => {
    //     console.log("onRotateStart", target)
    //   })
    //   .on(
    //     "rotate",
    //     ({ target, beforeDelta, delta, dist, transform, clientX, clientY }) => {
    //       console.log("onRotate", dist)
    //       target!.style.transform = transform
    //     }
    //   )
    //   .on("rotateEnd", ({ target, isDrag, clientX, clientY }) => {
    //     console.log("onRotateEnd", target, isDrag)
    //   })
  })

  let first = true

  $: if (textNode) {
    if (first) {
      first = false
      setTimeout(() => {
        updateCircleText(title)
      })
    } else {
      updateCircleText(title)
    }
  }

  function updateCircleText(text: string) {
    textNode.innerText = text

    const circleType = new CircleType(
      textNode,
      splitter.splitGraphemes.bind(splitter)
    )
    circleType.radius(400)
    // circleType.forceWidth(true)

    const firstLetter: HTMLElement =
      circleType.element.querySelector("span:first-child")

    if (firstLetter) {
      // const style: CSSStyleDeclaration = firstLetter.style
      // console.log(style.getPropertyValue("transform"))
      const container: HTMLElement = circleType.container
      // container.style["visibility"] = "hidden"
      setTimeout(() => {
        if (firstLetter.attributes["style"]) {
          const style = firstLetter.attributes["style"].value
          const rotation = style.match(/rotate\((.+)\)/)[1]

          container.style["transform"] = `rotate(${rotation})`
          container.style["transform-origin"] = `right bottom`
        }
        container.style["visibility"] = "visible"
        el = circleType.container.cloneNode(true).outerHTML
      }, 50)
    }
  }

  //   // console.log("seese")

  //   // circleType = new CircleType(
  //   //   textNode,
  //   //   splitter.splitGraphemes.bind(splitter)
  //   // )
  //   // circleType.radius(200).dir(-1)
  //   circleType.destroy()
  //   textNode.innerText = e.target.value
  //   console.log(textNode)

  //   circleType = new CircleType(
  //     textNode,
  //     splitter.splitGraphemes.bind(splitter)
  //   )
  //   circleType.radius(2000)
  // }

  // pasteImage.on("paste-image", function (image) {
  //   avatar = image.src
  //   setTimeout(() => onAvatarLoad(), 500)
  // })

  function onFileSelected(e) {
    let image = e.target.files[0]
    let reader = new FileReader()
    reader.readAsDataURL(image)
    reader.onload = (e) => {
      avatar = e.target.result
      setTimeout(() => onAvatarLoad(), 500)
    }
  }

  function downloadImage() {
    saving = true
    htmlToImage
      .toPng(node)
      .then(function (blob) {
        saveAs(blob, `${title}.png`)
        saving = false
      })
      .catch(function (error) {
        console.error("oops, something went wrong!", error)
      })
  }

  async function copyImage() {
    const fontEmbedCSS = await htmlToImage.getFontEmbedCSS(node)

    saving = true
    htmlToImage
      .toPng(node, { fontEmbedCSS })
      .then(function (dataUrl) {
        let img = new Image()
        img.src = dataUrl
        copyImageToClipboard(img.src)
        saving = false
      })
      .catch(function (error) {
        console.error("oops, something went wrong!", error)
      })
    isCopy = !isCopy
    setTimeout(() => {
      isCopy = !isCopy
    }, 5000)
  }
</script>

<!-- <svelte:head>
  <link
    href="https://fonts.googleapis.com/css2?family=Prompt&display=swap"
    rel="stylesheet"
  />
</svelte:head> -->

<!-- <Kofi name="narze" label="Support Me" /> -->
<!-- <Social {url} {title} description={""} /> -->

<main class="p-12 min-h-screen grid place-content-center gap-4">
  <div class="flex flex-col">
    <h1
      class="text-6xl mb-4 font-bold text-transparent text-center uppercase bg-clip-text bg-gradient-to-br text-[#71CC00]"
    >
      Working Working Working
    </h1>
  </div>

  <div
    bind:this={node}
    class="flex items-center justify-center relative h-[600px] w-[600px] overflow-hidden mx-auto"
    style={`font-family: "${selectedFont}"; background-color: ${bgColor.toHex()}`}
  >
    <!-- <div class="target text-white p-20 bg-green-400" bind:this={target}>
      Target
    </div>
    <Moveable
      {target}
      origin={true}
      edge={true}
      draggable={true}
      on:dragStart={({ detail: e }) => {
        e.set(frame.translate)
      }}
      on:drag={({ detail: e }) => {
        frame.translate = e.beforeTranslate
        e.target.style.transform = `translate(${e.beforeTranslate[0]}px, ${e.beforeTranslate[1]}px)`
      }}
    /> -->
    <div class="absolute text-transparent select-none top-[9999px]">
      <div bind:this={textNode} />
    </div>
    <div
      class="relative"
      style={`transform: scale(${descriptionSize / 70}) rotate(-2deg)`}
    >
      <div
        class={`relative text-[#71CC00] text-6xl`}
        style={`transform: translateY(${-spacing}px); color: ${textColor.toHex()}`}
        bind:this={container}
      >
        <div
          class="element -my-16 select-none"
          style={`transform: translateY(${
            spacing * 0
          }px) rotate(0deg); font-family: "${selectedFont}";`}
        >
          {@html el}
        </div>

        <div
          class="element -my-16 select-none"
          style={`transform: translateY(${
            spacing * 1
          }px) rotate(-0.5deg); font-family: "${selectedFont}";`}
        >
          {@html el}
        </div>

        <div
          class="element -my-16 select-none"
          style={`transform: translateY(${
            spacing * 2
          }px) rotate(-0.9deg); font-family: "${selectedFont}";`}
        >
          {@html el}
        </div>

        <div
          class="relative element -my-16 select-none"
          style={`transform: translateY(${
            spacing * 2
          }px) rotate(-0.9deg); font-family: "${selectedFont}";`}
        >
          <span class="invisible">
            {title}
          </span>
          <svg
            viewBox={`0 0 130 40`}
            preserveAspectRatio="none"
            class="absolute top-12 inset-x-0 h-12 w-[105%] overflow-visible"
            style={`transform: translateY(${
              spacing * 2 - 36
            }px); transform-box: fill-box; transform-origin: bottom;`}
          >
            <path d={underline} style={`fill: ${textColor.toHex()}`} />
          </svg>
        </div>

        <!-- <div class="target bg-[#71CC00] w-60 h-20" bind:this={target} />

        <Moveable
          {target}
          warpable={true}
          renderDirections={["nw", "n", "ne", "w", "e", "sw", "s", "se"]}
          edge={false}
          zoom={1}
          origin={false}
          padding={{ left: 0, top: 0, right: 0, bottom: 0 }}
          on:warpStart={({ detail: e }) => {
            e.set(frame.matrix)
          }}
          on:warp={({ detail: e }) => {
            frame.matrix = e.matrix
            e.target.style.transform = `translate(${frame.translate[0]}px, ${
              frame.translate[1]
            }px) matrix3d(${frame.matrix.join(",")})`
          }}
          draggable={true}
          on:dragStart={({ detail: e }) => {
            e.set(frame.translate)
          }}
          on:drag={({ detail: e }) => {
            frame.translate = e.beforeTranslate
            e.target.style.transform = `translate(${frame.translate[0]}px, ${
              frame.translate[1]
            }px) matrix3d(${frame.matrix.join(",")})`
          }}
        /> -->
      </div>
    </div>
  </div>
  <div
    class="flex flex-col gap-3 h-1/4 bottom-[2.5%] left-[2.5%] right-[2.5%] text-white mx-4"
  >
    <!-- <h1
        class="text-4xl mb-2 font-bold text-transparent bg-clip-text bg-gradient-to-br from-[#6215f1] 71CC00-[#1b3efa]"71CC33>
        {title}
      </h1> -->
    <!-- <h1 class="text-4xl mb-2 font-bold text-[#6215f1]">
        {title}
      </h1> -->

    <!-- svelte-ignore a11y-autofocus -->
    <input
      class={`${
        saving
          ? "border-2 border-transparent resize-none"
          : "border-2 border-[#71CC0088] rounded-xl"
      } caret-white text-center -top-2 bg-transparent text-4xl font-bold text-[#71CC00] appearance-none rounded w-full leading-tight focus:outline-none focus:border-[#6215f1]`}
      type="text"
      autofocus={true}
      bind:value={title}
    />

    <!-- Font selections -->
    <select
      bind:value={selectedFont}
      class="text-black px-4 py-2 border rounded text-center mx-auto"
    >
      {#each fonts as font}
        <option value={font}>
          {font}
        </option>
      {/each}
    </select>
  </div>

  <!-- <p class="text-white text-center text-sm">
    วิธีใช้: แก้ไขคำตามใจชอบ เลื่อนภาพด้วยการคลิกที่ภาพแล้วลาก<br
    />อัปโหลดภาพหรือ Paste ภาพจากคลิปบอร์ด
  </p>

  <div class="flex gap-8 overflow-hidden text-white items-center">
    <p class="text-xl w-1/3">ขนาดภาพ</p>
    <input
      type="range"
      min="50"
      max="200"
      bind:value={imageZoom}
      class="slider"
    />
  </div> -->

  <div class="flex gap-8 overflow-hidden text-white items-center">
    <p class="text-xl w-1/3">ขนาดตัวหนังสือ</p>
    <input
      type="range"
      min="50"
      max="240"
      bind:value={descriptionSize}
      class="slider"
    />
  </div>

  <div class="flex gap-8 overflow-hidden text-white items-center">
    <p class="text-xl w-1/3">ระยะบรรทัด</p>
    <input
      type="range"
      min="30"
      max="200"
      bind:value={spacingMicro}
      class="slider"
    />
  </div>

  <div class="flex flex-row gap-2">
    <ColorInput bind:color={textColor} title="สีตัวหนังสือ" />
    <ColorInput bind:color={bgColor} title="สีพื้นหลัง" />
  </div>

  <div class="flex flex-row gap-2">
    <input
      style="display:none"
      type="file"
      accept=".jpg, .jpeg, .png"
      on:change={(e) => onFileSelected(e)}
      bind:this={fileinput}
    />
    <button
      class="text-white text-center text-xl border-2 border-slate-400 rounded px-2 py-4 basis-full bg-gradient-to-r from-[#71CC00] to-[#71CC33] hover:border-[#6215f1]"
      on:click={() => {
        downloadImage()
      }}
    >
      ดาวน์โหลด
    </button>
    <button
      class="text-white text-center text-xl border-2 border-slate-400 rounded px-2 py-4 basis-full bg-gradient-to-r from-[#71CC00] to-[#71CC33] hover:border-[#6215f1]"
      on:click={copyImage}
    >
      {isCopy ? "Copied" : "คัดลอก"}
    </button>
  </div>
</main>

<!-- Bottom links -->
<div class="fixed inset-x-0 bottom-16 sm:bottom-4 text-center">
  <a
    href="https://github.com/narze/working3times"
    target="_blank"
    class="text-white text-sm bg-gradient-to-r from-[#71CC00] to-[#71CC33] px-2 py-1 rounded-md mx-1"
    >Github</a
  >
  <a
    href="https://twitch.narze.live"
    target="_blank"
    class="text-white text-sm bg-gradient-to-r from-[#71CC00] to-[#71CC33] px-2 py-1 rounded-md mx-1"
    >Twitch</a
  >
  <a
    href="https://f0nt.com"
    target="_blank"
    class="text-white text-sm bg-gradient-to-r from-[#71CC00] to-[#71CC33] px-2 py-1 rounded-md mx-1"
    >ฟ้อนต์จาก f0nt.com</a
  >
  <!-- <a
    href="https://www.youtube.com/watch?v=nlQJHIfpaYo"
    target="_blank"
    class="text-white text-sm bg-gradient-to-r from-[#71CC00] to-[#71CC33] px-2 py-1 rounded-md mx-1"
    >Live Code VOD</a
  > -->
</div>

<style lang="postcss">
  :root {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
      Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
    @apply bg-gray-800;
  }

  .bg {
    /* background: rgb(98, 21, 241);
    background: linear-gradient(113deg, #6215f1 0%, #1b3efa 100%); */
    background-color: black;
  }

  .bg-gradient {
    background: rgb(0, 0, 0);
    background: linear-gradient(
      0deg,
      rgba(0, 0, 0, 1) 70%,
      rgba(0, 0, 0, 0) 100%
    );
  }

  /* main {
    font-family: "Prompt", sans-serif;
  } */

  input[type="range"] {
    width: 100%;
    margin: 15px 0;
    background-color: transparent;
    user-select: none;
    -webkit-appearance: none;
    -webkit-tap-highlight-color: transparent;
  }

  input[type="range"]::-webkit-slider-runnable-track {
    background-image: linear-gradient(to right, #9084a7, #6671af);
    border-radius: 8px;
    width: 100%;
    height: 8px;
    cursor: pointer;
  }

  input[type="range"]::-webkit-slider-thumb {
    margin-top: -15px;
    width: 35px;
    height: 35px;
    background: #eeeeee;
    box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.3);
    border-radius: 30px;
    cursor: pointer;
    -webkit-appearance: none;
    user-select: none;
  }

  /*TODO: Use one of the selectors from https://stackoverflow.com/a/20541859/7077589 and figure out
how to remove the virtical space around the range input in IE*/
  @supports (-ms-ime-align: auto) {
    /* Pre-Chromium Edge only styles, selector taken from hhttps://stackoverflow.com/a/32202953/7077589 */
    input[type="range"] {
      margin: 0;
      /*Edge starts the margin from the thumb, not the track as other browsers do*/
    }
  }
</style>
