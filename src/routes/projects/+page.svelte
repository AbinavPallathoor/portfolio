<script lang="ts">
    import { spring } from "svelte/motion";
    import { fade, scale } from "svelte/transition";

    import yas4x42024Img from "$lib/assets/4x42024.png";
    import axis02Img from "$lib/assets/axis02.png";
    import palImg from "$lib/assets/pal.png";
    import smartwatchImg from "$lib/assets/smartwatch.png";
    import yas4x42025Img from "$lib/assets/4x42025.png";
    import dualsImage from "$lib/assets/duals.png";

    // --- NEW IMPORTS ---
    import projectFileIcon from "$lib/assets/projectFile.svg";
    import yas4x42024pdf from "$lib/assets/4x42024Report.pdf";
    import palpdf from "$lib/assets/palReport.pdf";
    import yas4x42025pdf from "$lib/assets/4x42025Report.pdf";

    const projects = [
        {
            title: "Axis 02",
            role: "Robotics",
            year: "2023",
            image: axis02Img,
            desc: "Telecommunication Tower Repair Robot",
            details:
                "Axis 02 is a telecommunication tower surveying and repair robot. It has a unique climbing mechanism and is equipped with a robotic arm.",
            techStack: ["Python", "OpenCV", "Arduino"],
            link: "https://www.youtube.com/watch?v=qbkLkFrB-6Y",
        },
        {
            title: "Axis 01",
            role: "4x4 in Schools",
            year: "2023",
            image: yas4x42024Img,
            desc: "4x4 Rockcrawler with Towing",
            details:
                "Axis 01 is a smart RC rockcrawler that is equipped with a moving towbar, which won the innovation award in the competition.",
            techStack: ["Arduino", "MIT App Inventor"],
            link: "https://youtu.be/WwxlESUxYFE?t=12367",
            file: yas4x42024pdf,
        },
        {
            title: "Pal",
            role: "Robotics",
            year: "2024",
            image: palImg,
            desc: "Personal Assistant",
            details:
                "Pal is a school receptionist robot that uses face detection, voice recognition, TTS and chatgpt to respond to guests.",
            techStack: [
                "Python",
                "OpenCV",
                "ChatGPT API",
                "TTS",
                "STT",
                "Pygame",
            ],
            link: "https://youtu.be/O13rowx4RRo?t=94",
            file: palpdf,
        },
        {
            title: "DIY Smartwatch",
            role: "IOT",
            year: "2024",
            image: smartwatchImg,
            desc: "Simple to Make Smartwatch",
            details:
                "It is a smartwatch made from scratch with a custom OS and 3d printed shell. It was made to be accessible and easy to make requiring no pcbs.",
            techStack: ["ESP32"],
            link: "https://youtu.be/02HUC12U0HY",
        },
        {
            title: "Axis X",
            role: "4x4 in Schools",
            year: "2025",
            image: yas4x42025Img,
            desc: "4x4 Car with AI & Automatic Self Leveling",
            details:
                "Axis X was our final attempt for 4x4 in schools. It featured auto self leveling, dual steering, and AI powered autonomous towbar. It won the national champion title.",
            techStack: ["Arduino", "Flutter", "OpenCV"],
            link: "https://youtu.be/NBg1xRWubq8?list=PLBlV5cAqxqW84xKpP_w8T56jFJNd2l-Wz&t=20726",
            file: yas4x42025pdf,
        },
        {
            title: "DUALS",
            role: "Game Dev",
            year: "2025",
            image: dualsImage,
            desc: "Splitscreen 1v1 Shooter",
            details:
                "Duals is a splitscreen shooter that stands out by having two players playing on the same device. The game features many maps, weapons and powerups.",
            techStack: ["Unity", "Blender"],
            link: "https://play.google.com/store/apps/details?id=com.abinavpallathoor.duals",
        },
    ];

    let activeIndex = spring(2, { stiffness: 0.05, damping: 0.5 });
    let dragStartX = 0;
    let isDragging = false;

    let selectedProject = $state(null);

    function snap() {
        activeIndex.update((n) => Math.round(n));
    }

    function handleWheel(e: WheelEvent) {
        if (selectedProject) return;
        const delta = e.deltaY * 0.005;
        updateIndex(delta);
        clearTimeout((window as any).snapTimeout);
        (window as any).snapTimeout = setTimeout(snap, 100);
    }

    function handleMouseDown(e: MouseEvent) {
        if (selectedProject) return;
        isDragging = true;
        dragStartX = e.clientX;
    }

    function handleMouseMove(e: MouseEvent) {
        if (!isDragging) return;
        const diff = (dragStartX - e.clientX) * 0.003;
        updateIndex(diff);
        dragStartX = e.clientX;
    }

    function handleMouseUp() {
        isDragging = false;
        snap();
    }

    function updateIndex(diff: number) {
        activeIndex.update((n) =>
            Math.max(0, Math.min(projects.length - 1, n + diff)),
        );
    }

    function select(index: number) {
        if (Math.round($activeIndex) === index) {
            selectedProject = projects[index];
        } else {
            activeIndex.set(index);
        }
    }

    function closePanel() {
        selectedProject = null;
    }

    function prev() {
        if (selectedProject) return;
        activeIndex.update((n) => Math.max(0, Math.round(n) - 1));
    }
    function next() {
        if (selectedProject) return;
        activeIndex.update((n) =>
            Math.min(projects.length - 1, Math.round(n) + 1),
        );
    }
</script>

<svelte:window
    on:mouseup={handleMouseUp}
    on:keydown={(e) => e.key === "Escape" && closePanel()}
/>

<div
    class="h-full w-full overflow-hidden flex flex-col items-center justify-start bg-white perspective-container pb-0"
    in:fade={{ duration: 300 }}
    out:fade={{ duration: 300 }}
    onwheel={handleWheel}
    onmousedown={handleMouseDown}
    onmousemove={handleMouseMove}
    role="region"
    aria-label="Project Gallery"
>
    <div
        class="relative w-full h-[800px] mt-[-100px] flex items-center justify-center preserve-3d"
        class:pointer-events-none={selectedProject}
    >
        {#each projects as p, i}
            {@const offset = i - $activeIndex}
            {@const absOffset = Math.abs(offset)}
            {@const zIndex = 100 - Math.round(absOffset)}

            <div
                class="absolute w-[450px] h-[450px] cursor-pointer album-card will-change-transform"
                style="
					z-index: {zIndex};
					transform: 
						translateX({offset * 260}px) 
						translateZ({absOffset * -400}px) 
						rotateY({Math.max(-50, Math.min(50, offset * -90))}deg);
					opacity: 1;
				"
                onclick={() => select(i)}
                onkeydown={(e) => e.key === "Enter" && select(i)}
                role="button"
                tabindex="0"
            >
                <img
                    src={p.image}
                    alt={p.title}
                    class="w-full h-full object-cover shadow-2xl bg-gray-200 select-none"
                    draggable="false"
                />

                <div
                    class="absolute top-full left-0 w-full h-full transform scale-y-[-1] opacity-50 mask-reflection pointer-events-none"
                >
                    <img
                        src={p.image}
                        alt=""
                        class="w-full h-full object-cover"
                    />
                </div>
            </div>
        {/each}
    </div>

    <div
        class="absolute bottom-10 w-full max-w-4xl flex justify-between items-center px-10 z-50 transition-opacity duration-300"
        class:opacity-0={selectedProject}
        class:pointer-events-none={selectedProject}
    >
        <button
            onclick={prev}
            class="pointer-events-auto text-[#999] hover:text-[#333] transition-colors p-4 rounded-full hover:bg-gray-100 transform hover:scale-110 active:scale-95"
            aria-label="Previous"
        >
            <svg
                xmlns="http://www.w3.org/2000/svg"
                width="40"
                height="40"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"><path d="m15 18-6-6 6-6" /></svg
            >
        </button>

        <div
            class="flex-1 text-center relative h-32 flex flex-col justify-end pb-2"
        >
            {#each projects as p, i}
                {#if Math.abs(i - $activeIndex) < 0.4}
                    <div
                        in:fade={{ duration: 300 }}
                        class="absolute bottom-0 w-full left-0 right-0"
                    >
                        <h2 class="text-4xl font-bold text-[#333] mb-2">
                            {p.title}
                        </h2>
                        <p
                            class="text-[#767676] text-xl font-medium tracking-wide"
                        >
                            {p.role} <span class="text-[#ccc] mx-2">|</span>
                            {p.year}
                        </p>
                        <p class="text-[#999] text-sm mt-2">{p.desc}</p>
                    </div>
                {/if}
            {/each}
        </div>

        <button
            onclick={next}
            class="pointer-events-auto text-[#999] hover:text-[#333] transition-colors p-4 rounded-full hover:bg-gray-100 transform hover:scale-110 active:scale-95"
            aria-label="Next"
        >
            <svg
                xmlns="http://www.w3.org/2000/svg"
                width="40"
                height="40"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"><path d="m9 18 6-6-6-6" /></svg
            >
        </button>
    </div>

    {#if selectedProject}
        <div
            class="fixed inset-0 z-[100] flex items-center justify-center bg-black/60 backdrop-blur-sm"
            transition:fade={{ duration: 200 }}
            onclick={closePanel}
        >
            <div
                class="bg-white relative w-[800px] h-[500px] rounded-3xl p-8 flex gap-8 items-start shadow-2xl"
                transition:scale={{ start: 0.95, duration: 200 }}
                onclick={(e) => e.stopPropagation()}
                role="dialog"
            >
                <button
                    onclick={closePanel}
                    class="absolute top-6 right-6 text-gray-400 hover:text-gray-800 transition-colors p-2 z-10"
                >
                    <svg
                        xmlns="http://www.w3.org/2000/svg"
                        width="24"
                        height="24"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="3"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        ><path d="M18 6 6 18" /><path d="m6 6 12 12" /></svg
                    >
                </button>

                <div
                    class="w-1/2 h-full rounded-xl overflow-hidden shadow-md relative shrink-0"
                >
                    <img
                        src={selectedProject.image}
                        alt={selectedProject.title}
                        class="w-full h-full object-cover"
                    />
                </div>

                <div class="w-1/2 h-full flex flex-col text-gray-700">
                    <div class="mb-4">
                        <h2 class="text-3xl font-bold mb-1 text-black">
                            {selectedProject.title}
                        </h2>
                        <p class="text-lg font-medium text-gray-500">
                            {selectedProject.role}
                            <span class="mx-1 text-gray-300">|</span>
                            {selectedProject.year}
                        </p>
                    </div>

                    <div class="flex-1 overflow-y-auto pr-2 custom-scrollbar">
                        <p class="text-lg leading-relaxed text-gray-600">
                            {selectedProject.details}
                        </p>

                        <div class="mt-6">
                            <h4
                                class="uppercase tracking-widest text-xs font-bold mb-2 text-gray-400"
                            >
                                Tech Stack
                            </h4>
                            <div class="flex flex-wrap gap-2">
                                {#each selectedProject.techStack as tech}
                                    <span
                                        class="px-3 py-1 bg-gray-100 rounded-md text-sm font-semibold border border-gray-200 text-gray-600"
                                    >
                                        {tech}
                                    </span>
                                {/each}
                            </div>
                        </div>
                    </div>

                    <div class="mt-6 flex gap-3 w-full">
                        <a
                            href={selectedProject.link}
                            target="_blank"
                            rel="noopener noreferrer"
                            class="flex-1 text-center py-3 bg-black text-white rounded-lg font-bold shadow-lg hover:bg-gray-800 active:scale-95 transition-all"
                        >
                            View Project
                        </a>

                        {#if selectedProject.file}
                            <a
                                href={selectedProject.file}
                                target="_blank"
                                rel="noopener noreferrer"
                                class="w-14 flex items-center justify-center bg-gray-100 border border-gray-300 rounded-lg shadow-md hover:bg-gray-200 active:scale-95 transition-all"
                                title="View Documentation"
                            >
                                <img
                                    src={projectFileIcon}
                                    alt="PDF"
                                    class="w-7 h-7 opacity-80"
                                />
                            </a>
                        {/if}
                    </div>
                </div>
            </div>
        </div>
    {/if}
</div>

<style>
    .perspective-container {
        perspective: 2000px;
    }

    .preserve-3d {
        transform-style: preserve-3d;
    }

    .mask-reflection {
        mask-image: linear-gradient(to top, black 0%, transparent 25%);
        -webkit-mask-image: linear-gradient(to top, black 0%, transparent 25%);
    }

    .album-card img {
        box-shadow: 0 25px 50px rgba(0, 0, 0, 0.2);
    }

    /* Optional: Custom Scrollbar for the panel content */
    .custom-scrollbar::-webkit-scrollbar {
        width: 6px;
    }
    .custom-scrollbar::-webkit-scrollbar-track {
        background: transparent;
    }
    .custom-scrollbar::-webkit-scrollbar-thumb {
        background-color: #e5e7eb;
        border-radius: 20px;
    }
</style>
