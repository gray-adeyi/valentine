<script lang="ts">
    import QuestionaireCard from "./lib/components/QuestionaireCard.svelte";
    import ConversationModal from "./lib/components/ConversationModal.svelte";
    import { base64Encode } from "./lib/utils/base64Parser";
    import type { LocalState } from "./types";

    const images = ["/1.gif", "/2.gif", "/3.gif", "/4.gif", "/5.gif", "/6.gif"];

    images.forEach((src) => {
        const img = new Image();
        img.src = src;
    });

    let isModalOpen = $state(false);
    let conversationLog = $state("");

    const encodeAndRedirect = (pathname: string) => {
        const name = pathname.replace("encode", "");
        const encodedName = base64Encode(name);
        window.location.href = `${window.location.origin}?q=${encodedName}`;
    };

    const params = new URLSearchParams(window.location.search);
    let query = params.get("q");
    if (!query) {
        query = "";
    }
    if (query.startsWith("encode")) {
        encodeAndRedirect(query);
    }

    const errMsg = "no conversations found"; // Simple error handling for now

    if (query.startsWith("decode")) {
        try {
            const key = localStorage.getItem("lastSaved");
            if (!key) throw new Error(errMsg);

            const dataRaw = localStorage.getItem(key);
            if (!dataRaw) throw new Error(errMsg);

            const data: LocalState | null = JSON.parse(dataRaw.toString());
            if (!data) throw new Error(errMsg);

            conversationLog = data.conversation;
            isModalOpen = true;
            // distinct from original: we don't redirect immediately so they can see the modal
        } catch (e) {
            console.error(e);
            alert(errMsg); // Fallback for error only
        }
    }
</script>

<main>
    <QuestionaireCard />
    <ConversationModal
        isOpen={isModalOpen}
        {conversationLog}
        onClose={() => (isModalOpen = false)}
    />
</main>

<style>
    main {
        display: grid;
        place-content: center;
        background: url("/bg.jpg");
        width: 100%;
        min-height: 100dvh;
        padding-block: 60px;
    }
</style>
