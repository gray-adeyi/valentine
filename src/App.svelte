<script lang="ts">
    import QuestionaireCard from "./lib/components/QuestionaireCard.svelte"
    import {base64Encode} from "./lib/utils/base64Parser";
    import type {LocalState} from "./types";

    const images = [
        "/1.gif",
        "/2.gif",
        "/3.gif",
        "/4.gif",
        "/5.gif",
        "/6.gif",
    ];

    images.forEach(src => {
        const img = new Image();
        img.src = src;
    });

    const encodeAndRedirect = (pathname: string) => {
        const name = pathname.replace("/encode", "");
        const encodedName = base64Encode(name);
        window.location.href = `${window.location.origin}/${encodedName}`
    }

    const {pathname} = window.location;
    if (pathname.startsWith("/encode")) {
        encodeAndRedirect(pathname);
    }
    const errMsg = 'no conversations found'
    if(pathname.startsWith("/decode")) {
        const key = localStorage.getItem('lastSaved') // retrieve the key for the last saved state
        if(!key) {
            alert(errMsg)
            throw new Error(errMsg)
        }
        const dataRaw = localStorage.getItem(key)
        if(!dataRaw){
            alert(errMsg)
            throw new Error(errMsg)
        }
        const data: LocalState | null = JSON.parse(dataRaw.toString())
        if(!data){
            alert(errMsg)
            throw new Error(errMsg)
        }
        alert(data.conversation)
        encodeAndRedirect(`/encode${data.loverName}`)
    }



</script>

<main>
    <QuestionaireCard />
</main>

<style>
main {
    display: grid;
    place-content: center;
    background: url('/bg.jpg');
    width: 100%;
    height: 100dvh;
}
</style>
