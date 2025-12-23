<script lang="ts">
    import myPhoto from "$lib/assets/me.jpg";
    import { spring } from "svelte/motion";
    import { fly } from "svelte/transition";

    // Physics-based springs
    let rotateX = spring(0, { stiffness: 0.05, damping: 0.3 });
    let rotateY = spring(0, { stiffness: 0.05, damping: 0.3 });
    let shineX = spring(50);
    let shineY = spring(50);

    function handleMouseMove(e: MouseEvent) {
        const card = e.currentTarget as HTMLElement;
        const rect = card.getBoundingClientRect();
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;

        rotateX.set(((y - rect.height / 2) / 25) * -1);
        rotateY.set((x - rect.width / 2) / 25);
        shineX.set((x / rect.width) * 100);
        shineY.set((y / rect.height) * 100);
    }

    function handleMouseLeave() {
        rotateX.set(0);
        rotateY.set(0);
        shineX.set(50);
        shineY.set(50);
    }
</script>

<div
    class="h-full flex justify-center pt-32 perspective-1000"
    in:fly={{ y: 50, duration: 600, delay: 100 }}
    out:fly={{ y: 50, duration: 300 }}
>
    <div
        class="metal-card etched-text w-[640px] h-[380px] relative rounded-3xl p-10 flex gap-10 items-center font-bold"
        onmousemove={handleMouseMove}
        onmouseleave={handleMouseLeave}
        style="transform: rotateX({$rotateX}deg) rotateY({$rotateY}deg);"
        role="img"
        aria-label="Profile Card"
    >
        <div
            class="absolute inset-0 rounded-3xl pointer-events-none z-50 mix-blend-overlay opacity-80"
            style="background: radial-gradient(circle at {$shineX}% {$shineY}%, rgba(255,255,255,0.7) 0%, transparent 55%);"
        ></div>

        <div class="w-48 h-56 overflow-hidden shrink-0 relative rounded-lg">
            {#if myPhoto}
                <img
                    src={myPhoto}
                    alt="Me"
                    class="w-full h-full object-cover grayscale opacity-80 mix-blend-multiply"
                />
                <div
                    class="absolute inset-0 shadow-[inset_2px_2px_5px_rgba(0,0,0,0.4),inset_-1px_-1px_2px_rgba(255,255,255,0.5)] pointer-events-none rounded-lg"
                ></div>
            {:else}
                <div
                    class="w-full h-full flex items-center justify-center border border-[#999] rounded-lg text-sm"
                >
                    No Image
                </div>
            {/if}
        </div>

        <div class="flex-1 z-10 text-xl leading-snug">
            <p class="mb-2">
                Name: <span class="font-bold">Abinav Pallathoor</span>
            </p>

            <p class="mb-2">
                Place of Origin: <span class="font-bold">India</span>
            </p>

            <div>
                Skills:
                <ol
                    class="list-decimal list-inside font-bold mt-1 space-y-0.5 ml-1"
                >
                    <li>Robotics Engineer</li>
                    <li>Game Dev</li>
                    <li>Web Dev</li>
                </ol>
            </div>
        </div>
    </div>
</div>

<style>
    .perspective-1000 {
        perspective: 1000px;
    }

    .metal-card {
        background: linear-gradient(
            135deg,
            #cfcfcf 0%,
            #f2f2f2 50%,
            #ababab 100%
        );
        box-shadow:
            0 15px 35px -5px rgba(0, 0, 0, 0.4),
            inset 0 1px 0 rgba(255, 255, 255, 0.9),
            inset 0 -1px 0 rgba(0, 0, 0, 0.2);
        border: 1px solid #999;
        transform-style: preserve-3d;
        will-change: transform;
    }

    .etched-text {
        color: #606060;
        text-shadow:
            1px 1px 0px rgba(255, 255, 255, 0.7),
            -1px -1px 1px rgba(0, 0, 0, 0.2);
    }
</style>
