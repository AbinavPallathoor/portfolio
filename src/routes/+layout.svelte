<script lang="ts">
    import "./layout.css";
    import favicon from "$lib/assets/favicon.svg";

    // Icons
    import githubIcon from "$lib/assets/githubLogo.svg";
    import itchIcon from "$lib/assets/itchLogo.svg";
    import linkedinIcon from "$lib/assets/linkedinLogo.svg";
    import youtubeIcon from "$lib/assets/youtubeLogo.svg";

    import { goto } from "$app/navigation";
    import { page } from "$app/state";
    import { Tabs } from "@skeletonlabs/skeleton-svelte";

    let { children } = $props();
    let currentTab = $derived(page.url.pathname);

    function onTabChange(e: { value: string }) {
        goto(e.value);
    }

    // --- SIMON GAME LOGIC ---
    let gameActive = $state(false);
    let score = $state(0);
    let sequence: number[] = $state([]);
    let playerStep = $state(0);
    let isComputerTurn = $state(false);
    let activeBtn = $state<number | null>(null); // Controls visual press
    let gameOver = $state(false); // Controls flash effect

    // Audio Context (Initialized on first interaction)
    let audioCtx: AudioContext;

    function initAudio() {
        if (!audioCtx && typeof window !== "undefined") {
            audioCtx = new (window.AudioContext ||
                (window as any).webkitAudioContext)();
        }
    }

    function playTone(
        freq: number,
        type: "sine" | "sawtooth" | "square" = "sine",
        duration = 300,
    ) {
        initAudio();
        if (!audioCtx) return;

        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();

        osc.type = type;
        osc.frequency.setValueAtTime(freq, audioCtx.currentTime);

        gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(
            0.00001,
            audioCtx.currentTime + duration / 1000,
        );

        osc.connect(gain);
        gain.connect(audioCtx.destination);

        osc.start();
        osc.stop(audioCtx.currentTime + duration / 1000);
    }

    // Frequencies for Red, Yellow, Green
    const TONES = [261.63, 329.63, 392.0]; // C4, E4, G4

    async function activateButton(index: number) {
        activeBtn = index;
        playTone(TONES[index]);
        await new Promise((r) => setTimeout(r, 300));
        activeBtn = null;
    }

    async function playSequence() {
        isComputerTurn = true;
        await new Promise((r) => setTimeout(r, 600)); // Initial pause

        for (const index of sequence) {
            await activateButton(index);
            await new Promise((r) => setTimeout(r, 200)); // Gap between notes
        }
        isComputerTurn = false;
    }

    async function handleBtnClick(index: number) {
        // Init audio on first click
        initAudio();
        if (audioCtx?.state === "suspended") audioCtx.resume();

        // 1. IF GAME NOT STARTED: Start it with this button
        if (!gameActive) {
            gameActive = true;
            score = 0;
            gameOver = false;
            sequence = [index]; // First button pressed is first in sequence
            await activateButton(index); // Play user's first note immediately
            // Wait a beat, then play the full sequence (which is just this note for now)
            setTimeout(playSequence, 500);
            return;
        }

        // 2. IF COMPUTER IS SHOWING SEQUENCE: Ignore clicks
        if (isComputerTurn) return;

        // 3. GAMEPLAY LOGIC
        activateButton(index); // Visual/Audio feedback immediately

        if (index === sequence[playerStep]) {
            // Correct Move
            playerStep++;
            if (playerStep === sequence.length) {
                // Round Complete
                score++;
                playerStep = 0;
                // Add new random step (0, 1, or 2)
                sequence.push(Math.floor(Math.random() * 3));
                playSequence();
            }
        } else {
            // Wrong Move - Game Over
            playTone(150, "sawtooth", 600); // Error buzz
            gameOver = true; // Triggers CSS flash
            gameActive = false;
            playerStep = 0;
            sequence = [];

            // Reset flash class after animation plays
            setTimeout(() => {
                gameOver = false;
            }, 500);
        }
    }
</script>

<svelte:head><link rel="icon" href={favicon} /></svelte:head>

<div class="h-screen flex flex-col text-black">
    <header
        class="metal-bar flex items-center justify-between px-6 py-5 shrink-0 relative z-20"
        class:flash-red={gameOver}
    >
        <div class="flex gap-3">
            <button
                class="win-btn win-btn-red w-6 h-6 rounded-full"
                class:simon-active={activeBtn === 0}
                onmousedown={() => handleBtnClick(0)}
                aria-label="Red Button"
            ></button>
            <button
                class="win-btn win-btn-yellow w-6 h-6 rounded-full"
                class:simon-active={activeBtn === 1}
                onmousedown={() => handleBtnClick(1)}
                aria-label="Yellow Button"
            ></button>
            <button
                class="win-btn win-btn-green w-6 h-6 rounded-full"
                class:simon-active={activeBtn === 2}
                onmousedown={() => handleBtnClick(2)}
                aria-label="Green Button"
            ></button>
        </div>

        <div
            class="absolute inset-0 flex items-center justify-center pointer-events-none"
        >
            <span class="window-title text-3xl font-bold tracking-wide mt-1">
                {#if gameActive || score > 0}
                    Score: {score}
                {:else}
                    Abinav Pallathoor
                {/if}
            </span>
        </div>

        <div class="flex gap-5 items-center">
            <a
                href="https://github.com"
                target="_blank"
                class="block"
                title="GitHub"
            >
                <img
                    src={githubIcon}
                    alt="GitHub"
                    class="icon-gray w-12 h-12"
                />
            </a>
            <a
                href="https://itch.io"
                target="_blank"
                class="block"
                title="Itch.io"
            >
                <img src={itchIcon} alt="Itch.io" class="icon-gray w-12 h-12" />
            </a>
            <a
                href="https://www.linkedin.com/in/abinav-pallathoor-b65785353/"
                target="_blank"
                class="block"
                title="Linkedin"
            >
                <img
                    src={linkedinIcon}
                    alt="Linkedin"
                    class="icon-gray w-12 h-12"
                />
            </a>
            <a
                href="https://youtube.com"
                target="_blank"
                class="block"
                title="YouTube"
            >
                <img
                    src={youtubeIcon}
                    alt="YouTube"
                    class="icon-gray w-12 h-12"
                />
            </a>
        </div>
    </header>

    <div class="bg-white pt-4 px-4 pb-0 z-10 relative">
        <div class="w-full">
            <Tabs value={currentTab} onValueChange={onTabChange}>
                <Tabs.List class="w-full flex justify-between gap-4">
                    <Tabs.Trigger class="tab-trigger flex-1 text-2xl" value="/"
                        >Home</Tabs.Trigger
                    >
                    <Tabs.Trigger
                        class="tab-trigger flex-1 text-2xl"
                        value="/about">About</Tabs.Trigger
                    >
                    <Tabs.Trigger
                        class="tab-trigger flex-1 text-2xl"
                        value="/projects">Projects</Tabs.Trigger
                    >
                    <Tabs.Trigger
                        class="tab-trigger flex-1 text-2xl"
                        value="/contact">Contact</Tabs.Trigger
                    >
                    <Tabs.Indicator class="tab-indicator" />
                </Tabs.List>
            </Tabs>
        </div>
    </div>

    <main class="flex-1 grid w-full relative overflow-hidden bg-white pt-4">
        {@render children()}
    </main>
</div>

<style>
    main > :global(*) {
        grid-area: 1 / 1;
        width: 100%;
        height: 100%;
    }
</style>
