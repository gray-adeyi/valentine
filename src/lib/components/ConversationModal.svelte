<script lang="ts">
    interface Props {
        isOpen: boolean;
        conversationLog: string;
        onClose: () => void;
    }

    let { isOpen, conversationLog, onClose }: Props = $props();
</script>

{#if isOpen}
    <div
        class="modal-overlay"
        onclick={onClose}
        role="button"
        tabindex="0"
        onkeydown={(e) => e.key === "Escape" && onClose()}
    >
        <div
            class="modal-content"
            onclick={(e) => e.stopPropagation()}
            role="document"
            tabindex="0"
            onkeydown={(e) => e.key === "Escape" && onClose()}
        >
            <h2 class="courgette-regular">Our Love Story</h2>
            <div class="log-container funnel-sans-400">
                {#each conversationLog.split("\n") as line}
                    <p>{line}</p>
                {/each}
            </div>
            <button class="close-btn courgette-regular" onclick={onClose}
                >Close</button
            >
        </div>
    </div>
{/if}

<style>
    .modal-overlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100vw;
        height: 100dvh;
        background: rgba(0, 0, 0, 0.7);
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 1000;
        backdrop-filter: blur(5px);
    }

    .modal-content {
        background: #fff0f5;
        padding: 2rem;
        border-radius: 15px;
        width: 90%;
        max-width: 500px;
        max-height: 80dvh;
        display: flex;
        flex-direction: column;
        gap: 1rem;
        box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
        animation: popIn 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }

    h2 {
        color: #d12e68;
        text-align: center;
        margin: 0;
    }

    .log-container {
        flex: 1;
        overflow-y: auto;
        background: #fff;
        padding: 1rem;
        border-radius: 8px;
        border: 2px solid #ffb6c1;
        white-space: pre-wrap;
        line-height: 1.5;
        color: #555;
    }

    p {
        margin-bottom: 0.5rem;
    }

    .close-btn {
        background: #d12e68;
        color: white;
        border: none;
        padding: 0.8rem;
        border-radius: 8px;
        font-size: 1.1rem;
        cursor: pointer;
        transition: transform 0.1s;
    }

    .close-btn:active {
        transform: scale(0.98);
    }

    @keyframes popIn {
        from {
            opacity: 0;
            transform: scale(0.8);
        }
        to {
            opacity: 1;
            transform: scale(1);
        }
    }
</style>
