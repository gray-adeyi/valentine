<script lang="ts">
    import {base64Decode} from "../utils/base64Parser";

    let currentStepId = $state("0");
    let loverResponseLog = $state(["0"]);

    const currentStep = $derived.by(
        () => steps.find((s) => s.id === currentStepId) || steps[0],
    );
    const loversNameFromPath = $derived.by(() => {
    //     I'm making an assumption that a base64 encoded name of the lover is passed as the path
        let loversName = window.location.pathname.replace(/^\//, "");
        loversName = base64Decode(loversName)
        if (loversName !== "") return loversName;
        return "Mi Amore";
    })
    const steps = $derived.by(() => {
        return [
            {
                id: "0",
                questionText: `Hey ${loversNameFromPath}! You know what time it is 😉`,
                questionImage: "/1.gif",
                showActionButtons: true,
                yesButtonText: "Yes, my love ☺️",
                yesButtonGotoStep: "closer",
                noButtonText: "I don't give a f*#k",
                noButtonGotoStep: "1-no",
            },
            {
                id: "1-no",
                questionText: "I know i've been a bad boy, but is still want you",
                questionImage: "/2.gif",
                showActionButtons: true,
                yesButtonText: "i forgive you baby",
                yesButtonGotoStep: "2-yes",
                noButtonText: "me i don't want you",
                noButtonGotoStep: "2-no",
            },
            {
                id: "2-yes",
                questionText: "Awwn, thanks babe! You tick all the boxes of all i ever want in a lover",
                questionImage: "/6.gif",
                showActionButtons: true,
                yesButtonText: "I'm blushing",
                yesButtonGotoStep: "semi-final",
                noButtonText: "me i don't want you",
                noButtonGotoStep: "1-no",
            },
            {
                id: "2-no",
                questionText: "But, you and me make a great couple, you're the best thing that's happened to me, don't take that away",
                questionImage: "/5.gif",
                showActionButtons: true,
                yesButtonText: "I'm blushing",
                yesButtonGotoStep: "closer",
                noButtonText: "me i don't want you",
                noButtonGotoStep: "1-no",
            },
            {
                id: "closer",
                questionText: `I want to use this lover's season to let you know how special you are to me, Everyday is valentine's day with you 🥰${loversNameFromPath}🥰`,
                questionImage: "/6.gif",
                showActionButtons: true,
                yesButtonText: "Awwn, that's sweet baby",
                yesButtonGotoStep: "semi-final",
                noButtonText: "Just that?",
                noButtonGotoStep: "final-no",
            },
            {
                id: "semi-final",
                questionText: `Will you be my valentine ${loversNameFromPath}? 🥺🥺🥺`,
                questionImage: "/3.gif",
                showActionButtons: true,
                yesButtonText: "yes, and always",
                yesButtonGotoStep: "final-yes",
                noButtonText: "you wish",
                noButtonGotoStep: "final-no",
            },
            {
                id: "final-yes",
                questionText: `I love you ❣️${loversNameFromPath}❣️, Happy Valentines!`,
                questionImage: "/4.gif",
                showActionButtons: false,
            },
            {
                id: "final-no",
                questionText: `I knew you'd say yes, Happy Valentines ${loversNameFromPath}!`,
                questionImage: "/5.gif",
                showActionButtons: false,
            },
        ]
    });
    
    $effect(() => {
      if((currentStepId !== 'final-yes') && (currentStepId !== 'final-no')) return
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
      localStorage.setItem(key, JSON.stringify({
          timestamp: Date.now(),
          conversation: buildConversationFromResponse(),
          loverName: loversNameFromPath,
      }));
      localStorage.setItem('lastSaved', key)
    })

    const gotoStep = (id: string) => {
        loverResponseLog.push(id);
        currentStepId = id}
    ;

    const playAudioOnAccept = (node: HTMLAudioElement) => {
        if (currentStepId !== 'final-yes' && currentStepId !== 'final-no') return;
        if(node.paused) node.play();

        setTimeout(() => {
            const trackNumber = Math.floor(Math.random() * 3);
            node.src = `/serenade-${trackNumber}.m4a`;
            node.play();
        }, 1000);

        return () => {
            if(!node.paused) node.pause()
        }
    }

    const buildConversationFromResponse = () => {
        let conversation = ""
        loverResponseLog.forEach((id, idx) =>{
            const step = steps.find((s) => s.id === id);
            if(!step) return
            conversation += `you: ${step.questionText}\n`
            if (idx + 1 >= loverResponseLog.length) return
            const nextStepId = loverResponseLog[idx + 1];
            const nextStep = steps.find((s) => s.id === nextStepId);
            if(!nextStep) return
            if(nextStep.id === step.yesButtonGotoStep) {
                conversation += `${loversNameFromPath}: ${step.yesButtonText}\n`
            } else {
                conversation += `${loversNameFromPath}: ${step.noButtonText}\n`
            }
        })
        return conversation
    }
</script>

<div class="container">
    <audio {@attach playAudioOnAccept} src="/i-love-you.mp3" class="hidden"></audio>
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
</div>

<style>
    .container {
        display: flex;
        flex-direction: column;
        margin-block-start: -15vh;
        width: 90vw;
        min-height: 60vh;
        background: #c475a5;
        border-radius: 10px;
        box-shadow: 0 0 22px 8px rgba(0 0 0 / 0.1);
        position: relative;
        padding: 10px;
    }

    .hidden {
        display: none;
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
    }

    .btn {
        padding: 5px 8px;
        border-radius: 5px;
        border-style: none;
        font-size: 1.3rem;
        font-weight: bold;
        color: #fff;
    }

    .yes-btn {
        background: green;
    }

    .no-btn {
        background: red;
    }

    @keyframes pulse {
        50% {
            transform: scale(1.1);
        }
    }
</style>
