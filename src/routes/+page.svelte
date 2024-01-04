<script>
  import imageCompression from "browser-image-compression";
  import { ProgressRadial } from "@skeletonlabs/skeleton";
	import JSZip from 'jszip'

  let files = [];
  let imageSize;
  let imageHeight;
  let imageWidth;
  let optionSelect = "Size"; // Set a default value
  let currentProcessingIndex = 0;
	let zipper = new JSZip();

  async function compress_image() {
    // Implement your image compression logic here
    currentProcessingIndex = 0;

    let allFiles = [];

    let allBlobs = [];

    for (const file of files) {
      console.log(file);
      // imageCompressorFunction(file)
      allFiles.push(file);
    }

    while (currentProcessingIndex < allFiles.length) {
      // Process 10 files at a time
      for (let i = 0; i < 10 && currentProcessingIndex < allFiles.length; i++) {
        let Fileblob = await imageCompressorFunction(
          allFiles[currentProcessingIndex]
        );

        allBlobs.push(Fileblob);

        console.log(i);
        console.log(currentProcessingIndex);
        currentProcessingIndex++;
      }
    }
		
    BlobToFileCreator(allBlobs);

    BlobToLinkCreator(allBlobs);

  }

  function updateOptionSelect(value) {
    optionSelect = value;
  }

  async function imageCompressorFunction(image) {
    document.querySelector(".images").innerHTML = ``;
    imageSize = imageSize ? imageSize : 1024;

    const imageFile = image;
    console.log("originalFile instanceof Blob", imageFile instanceof Blob); // true
    console.log(`originalFile size ${imageFile.size / 1024 / 1024} MB`);

    const options = {
      maxSizeMB: imageSize / 1024,
      // maxWidthOrHeight: 1920,
      useWebWorker: true,
    };
    try {
      const compressedFile = await imageCompression(imageFile, options);
      console.log(
        "compressedFile instanceof Blob",
        compressedFile instanceof Blob
      ); // true
      console.log(
        `compressedFile size ${compressedFile.size / 1024 / 1024} MB`
      ); // smaller than maxSizeMB

      return await compressedFile;
    } catch (error) {
      console.log(error);
    }
  }

  function BlobToLinkCreator(blobArray) {
    blobArray.forEach((Fileblob) => {
      let myBlob = Fileblob;

      // Create a link element
      let downloadLink = document.createElement("a");

      // Create a URL for the Blob
      let blobUrl = URL.createObjectURL(myBlob);

      // Set the href attribute of the link to the Blob URL
      downloadLink.href = blobUrl;

      // Set the download attribute with the desired file name
      downloadLink.download = myBlob.name; // You can set any desired file name with the appropriate file extension

      downloadLink.innerText = `download - ${myBlob.name}`;

      document.querySelector(".images").appendChild(downloadLink);
    });
  }

  function BlobToFileCreator(blobArray) {
    let imageArray = [];
    blobArray.forEach((myBlob) => {
      let imageFile = new File([myBlob], myBlob.name, { type: myBlob.type });

      imageArray.push(imageFile);
    });

    console.log(imageArray);

		zipCreator(imageArray)
  }

	async function zipCreator(imageArray){
		let imageZipFolder = zipper.folder(`all_${currentProcessingIndex}_images`);
		imageArray.forEach(image=>{
			imageZipFolder.file(image.name,image);
		})

		let zip_file = await imageZipFolder.generateAsync({type:"blob"});

		      // Create a link element
					let downloadLink = document.createElement("a");

					// Create a URL for the Blob
					let blobUrl = URL.createObjectURL(zip_file);

					// Set the href attribute of the link to the Blob URL
					downloadLink.href = blobUrl;

					// Set the download attribute with the desired file name
					downloadLink.download = `all_${currentProcessingIndex}_images.zip`; 

					downloadLink.innerText = `DOWNLOAD all in ZIP`;

					document.querySelector(".images").insertBefore(downloadLink,document.querySelector(".images").children[0]);


	}
</script>

<main class="py-5">
  <div class="h-full w-full grid grid-cols-1 gap-5 justify-items-center">
    <input
      type="file"
      name=""
      class="border rounded-full cursor-pointer"
      accept="image/*"
      multiple
      bind:files
    />
    <div class="space-y-2">
      <label class="flex items-center space-x-2">
        <!-- <input class="radio" type="radio" name="radio-direct" value="Size" bind:group={optionSelect} on:change={() => updateOptionSelect("Size")} /> -->
        <input
          type="number"
          placeholder="FileSize"
          class="rounded-full disabled:variant-filled-surface px-3 py-1 w-24 text-slate-900"
          disabled={optionSelect !== "Size"}
          bind:value={imageSize}
        />
        <span>KB</span>
      </label>
      <!-- <label class="flex items-center space-x-2">
        <input class="radio" type="radio" name="radio-direct" value="heightWidth" bind:group={optionSelect} on:change={() => updateOptionSelect("heightWidth")} />
        <div class="flex gap-2 items-center">
          <input type="number" name="" id="" class="rounded-full disabled:variant-filled-surface px-3 py-1 w-28 text-slate-900" placeholder="Height in px" disabled="{optionSelect !== 'heightWidth'}" bind:value={imageHeight}>
          <span>X</span>
          <input type="number" name="" id="" class="rounded-full disabled:variant-filled-surface px-3 py-1 w-28 text-slate-900" placeholder="Width in px" disabled="{optionSelect !== 'heightWidth'}" bind:value={imageWidth}>
        </div>
      </label> -->
    </div>
    <div>
      <button
        type="button"
        class="btn variant-glass-primary"
        on:click={() => compress_image()}>Compress</button
      >
    </div>
  </div>

  <div class="ps-5">
    <p>number of images processed : {currentProcessingIndex}</p>
    <ProgressRadial
      value={((currentProcessingIndex / files.length) * 100).toFixed(2)}
      stroke={100}
      meter="stroke-primary-500"
      track="stroke-primary-500/30"
      strokeLinecap="round"
      >{((currentProcessingIndex / files.length) * 100).toFixed(
        2
      )}%</ProgressRadial
    >
  </div>

  <div class="images flex flex-col w-50 items-center mt-12"></div>
</main>

<style>
  input::-webkit-outer-spin-button,
  input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
  }

  input[type="number"] {
    -moz-appearance: textfield;
    appearance: textfield;
  }
</style>
