<script lang="ts">
    import { base64Decode } from "../utils/base64Parser";

    let currentStepId = $state("0");
    let loverResponseLog = $state(["0"]);
    let isMuted = $state(false);

    const currentStep = $derived.by(
        () => steps.find((s) => s.id === currentStepId) || steps[0],
    );
    const loversNameFromQuery = $derived.by(() => {
        //     I'm making an assumption that a base64 encoded name of the lover is passed as the query params q
        const params = new URLSearchParams(window.location.search);
        const query = params.get("q");
        if (!query) return "Mi Amore";
        const loversName = base64Decode(query);
        if (loversName !== "") return loversName;
        return "Mi Amore";
    });
    const steps = $derived.by(() => {
        return [
            {
                id: "0",
                questionText: `Hey ${loversNameFromQuery}! You know what time it is 😉`,
                questionImage: "/1.gif",
                showActionButtons: true,
                yesButtonText: "Yes, my love ☺️",
                yesButtonGotoStep: "closer",
                noButtonText: "I don't give a f*#k",
                noButtonGotoStep: "1-no",
            },
            {
                id: "1-no",
                questionText:
                    "I know i've been a bad boy, but is still want you",
                questionImage: "/2.gif",
                showActionButtons: true,
                yesButtonText: "i forgive you baby",
                yesButtonGotoStep: "2-yes",
                noButtonText: "me i don't want you",
                noButtonGotoStep: "2-no",
            },
            {
                id: "2-yes",
                questionText:
                    "Awwn, thanks babe! You tick all the boxes of all i ever want in a lover",
                questionImage: "/6.gif",
                showActionButtons: true,
                yesButtonText: "I'm blushing",
                yesButtonGotoStep: "semi-final",
                noButtonText: "me i don't want you",
                noButtonGotoStep: "1-no",
            },
            {
                id: "2-no",
                questionText:
                    "But, you and me make a great couple, you're the best thing that's happened to me, don't take that away",
                questionImage: "/5.gif",
                showActionButtons: true,
                yesButtonText: "I'm blushing",
                yesButtonGotoStep: "closer",
                noButtonText: "me i don't want you",
                noButtonGotoStep: "1-no",
            },
            {
                id: "closer",
                questionText: `I want to use this lover's season to let you know how special you are to me, Everyday is valentine's day with you 🥰${loversNameFromQuery}🥰`,
                questionImage: "/6.gif",
                showActionButtons: true,
                yesButtonText: "Awwn, that's sweet baby",
                yesButtonGotoStep: "semi-final",
                noButtonText: "Just that?",
                noButtonGotoStep: "final-no",
            },
            {
                id: "semi-final",
                questionText: `Will you be my valentine ${loversNameFromQuery}? 🥺🥺🥺`,
                questionImage: "/3.gif",
                showActionButtons: true,
                yesButtonText: "yes, and always",
                yesButtonGotoStep: "final-yes",
                noButtonText: "you wish",
                noButtonGotoStep: "final-no",
            },
            {
                id: "final-yes",
                questionText: `I love you ❣️${loversNameFromQuery}❣️, Happy Valentines!`,
                questionImage: "/4.gif",
                showActionButtons: false,
            },
            {
                id: "final-no",
                questionText: `I knew you'd say yes, Happy Valentines ${loversNameFromQuery}!`,
                questionImage: "/5.gif",
                showActionButtons: false,
            },
        ];
    });

    $effect(() => {
        if (currentStepId !== "final-yes" && currentStepId !== "final-no")
            return;
        const defaults = {
            spread: 360,
            ticks: 100,
            gravity: 0,
            decay: 0.94,
            startVelocity: 30,
            shapes: ["heart"],
            colors: ["FFC0CB", "FF69B4", "FF1493", "C71585"],
        };
        confetti({
            ...defaults,
            particleCount: 50,
            scalar: 2,
        });

        confetti({
            ...defaults,
            particleCount: 25,
            scalar: 3,
        });

        confetti({
            ...defaults,
            particleCount: 10,
            scalar: 4,
        });

        const key = window.crypto.randomUUID();
        localStorage.setItem(
            key,
            JSON.stringify({
                timestamp: Date.now(),
                conversation: buildConversationFromResponse(),
                loverName: loversNameFromQuery,
            }),
        );
        localStorage.setItem("lastSaved", key);
    });

    const gotoStep = (id: string) => {
        loverResponseLog.push(id);
        currentStepId = id;
    };
    const playAudioOnAccept = (node: HTMLAudioElement) => {
        if (currentStepId !== "final-yes" && currentStepId !== "final-no")
            return;

        // Setup the audio source first
        setTimeout(() => {
            const trackNumber = Math.floor(Math.random() * 3);
            node.src = `/serenade-${trackNumber}.m4a`;
            // Only play if not muted
            if (!isMuted) {
                node.play().catch(() => {
                    // Autoplay prevented
                });
            }
        }, 1000);

        // We also need to react to isMuted changes dynamically if the audio is already playing
        // But since this action is one-off on step change, we'll handle the reactive part via the button
        // or a separate effect if we wanted true continuous control.
        // For simplicity, binding `muted` attribute on the audio tag is easier.
        // Let's rely on svelte binding for mute status.
    };

    const buildConversationFromResponse = () => {
        let conversation = "";
        loverResponseLog.forEach((id, idx) => {
            const step = steps.find((s) => s.id === id);
            if (!step) return;
            conversation += `you: ${step.questionText}\n`;
            if (idx + 1 >= loverResponseLog.length) return;
            const nextStepId = loverResponseLog[idx + 1];
            const nextStep = steps.find((s) => s.id === nextStepId);
            if (!nextStep) return;
            if (nextStep.id === step.yesButtonGotoStep) {
                conversation += `${loversNameFromQuery}: ${step.yesButtonText}\n`;
            } else {
                conversation += `${loversNameFromQuery}: ${step.noButtonText}\n`;
            }
        });
        return conversation;
    };

    const shareResponse = () => {
        const conversation = buildConversationFromResponse();
        const text = encodeURIComponent(conversation);
        window.open(`https://wa.me/?text=${text}`, "_blank");
    };
</script>

<div class="container">
    <!-- Bind muted property to isMuted state -->
    <audio
        {@attach playAudioOnAccept}
        src="/i-love-you.mp3"
        class="hidden"
        bind:muted={isMuted}
    ></audio>

    <button
        class="audio-control"
        onclick={() => (isMuted = !isMuted)}
        aria-label={isMuted ? "Unmute" : "Mute"}
    >
        {#if isMuted}
            🔇
        {:else}
            🔊
        {/if}
    </button>

    <img class="img" src="/calendar.png" alt="valentine calendar" />
    <div class="component-container">
        <p class="question-text courgette-regular">
            {currentStep.questionText}
        </p>
        <img
            class="question-img"
            src={currentStep.questionImage}
            alt="it's about time"
        />
    </div>
    {#if currentStep.showActionButtons}
        <div class="action-button-container">
            <button
                onclick={() => gotoStep(currentStep.yesButtonGotoStep)}
                class="btn yes-btn courgette-regular"
                >{currentStep.yesButtonText}</button
            >
            <button
                onclick={() => gotoStep(currentStep.noButtonGotoStep)}
                class="btn no-btn courgette-regular"
                >{currentStep.noButtonText}</button
            >
        </div>
    {/if}

    {#if currentStepId === "final-yes"}
        <div class="action-button-container">
            <button
                class="btn share-btn courgette-regular"
                onclick={shareResponse}
            >
                Send My Reply 💌
            </button>
        </div>
    {/if}
</div>

<style>
    .container {
        display: flex;
        flex-direction: column;
        width: 90vw;
        height: auto;
        min-height: 60dvh; /* Changed to dvh */
        background: #c475a5;
        border-radius: 10px;
        box-shadow: 0 0 22px 8px rgba(0 0 0 / 0.1);
        position: relative;
        padding: 10px;
        padding-bottom: 2rem;
    }

    .hidden {
        display: none;
    }

    .audio-control {
        position: absolute;
        top: 10px;
        left: 10px;
        background: none;
        border: none;
        font-size: 1.5rem;
        cursor: pointer;
        z-index: 10;
        filter: drop-shadow(0 0 2px rgba(255, 255, 255, 0.7));
    }

    .component-container {
        padding-block-start: 70px;
        flex: 1;
    }

    .img {
        position: absolute;
        width: 100px;
        top: -15px;
        right: -5px;
        animation: pulse 2s infinite;
    }

    .question-text {
        color: #60123f;
        font-size: 1.3rem;
        padding-block-end: 0.75rem;
    }
    .question-img {
        display: block;
        object-fit: contain;
        max-width: 100%;
        border-radius: 5px;
        margin-inline: auto;
    }

    .action-button-container {
        display: flex;
        justify-content: center;
        margin-block-start: 10px;
        gap: 2rem;
        flex-wrap: wrap; /* Good for small screens */
    }

    .btn {
        padding: 5px 8px;
        border-radius: 5px;
        border-style: none;
        font-size: 1.3rem;
        font-weight: bold;
        color: #fff;
        cursor: pointer;
    }

    .yes-btn {
        background: green;
    }

    .no-btn {
        background: red;
    }

    .share-btn {
        background: #25d366; /* WhatsApp Green */
        box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
    }

    @keyframes pulse {
        50% {
            transform: scale(1.1);
        }
    }
</style>
