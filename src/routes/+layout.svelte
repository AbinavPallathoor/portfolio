<script lang="ts">
    import "./layout.css";
    import favicon from "$lib/assets/favicon.svg";

    // Icons
    import githubIcon from "$lib/assets/githubLogo.svg";
    import itchIcon from "$lib/assets/itchLogo.svg";
    import youtubeIcon from "$lib/assets/youtubeLogo.svg";
    import linkedinLogo from "$lib/assets/linkedinLogo.svg";

    import { goto } from "$app/navigation";
    import { page } from "$app/state";
    import { Tabs } from "@skeletonlabs/skeleton-svelte";

    let { children } = $props();
    let currentTab = $derived(page.url.pathname);

    function onTabChange(e: { value: string }) {
        goto(e.value);
    }
</script>

<svelte:head><link rel="icon" href={favicon} /></svelte:head>

<div class="h-screen flex flex-col text-black">
    <header
        class="metal-bar flex items-center justify-between px-6 py-5 shrink-0 relative z-20"
    >
        <div class="flex gap-3">
            <button class="win-btn win-btn-red w-6 h-6 rounded-full"></button>
            <button class="win-btn win-btn-yellow w-6 h-6 rounded-full"
            ></button>
            <button class="win-btn win-btn-green w-6 h-6 rounded-full"></button>
        </div>

        <div
            class="absolute inset-0 flex items-center justify-center pointer-events-none"
        >
            <span class="window-title text-3xl font-bold tracking-wide mt-1">
                Abinav Pallathoor
            </span>
        </div>

        <div class="flex gap-5 items-center">
            <a
                href="https://github.com/AbinavPallathoor"
                target="_blank"
                class="block"
                title="GitHub"
            >
                <img src={githubIcon} alt="GitHub" class="icon-gray w-7 h-7" />
            </a>
            <a
                href="https://abinav-pallathoor.itch.io"
                target="_blank"
                class="block"
                title="Itch.io"
            >
                <img src={itchIcon} alt="Itch.io" class="icon-gray w-7 h-7" />
            </a>
            <a
                href="https://www.linkedin.com/in/abinav-pallathoor-b65785353/"
                target="_blank"
                class="block"
                title="Linkedin"
            >
                <img
                    src={linkedinLogo}
                    alt="Linkedin"
                    class="icon-gray w-7 h-7"
                />
            </a>
            <a
                href="https://www.youtube.com/@Abinav.P"
                target="_blank"
                class="block"
                title="YouTube"
            >
                <img
                    src={youtubeIcon}
                    alt="YouTube"
                    class="icon-gray w-7 h-7"
                />
            </a>
        </div>
    </header>

    <div class="bg-white pt-4 px-4 pb-0">
        <div class="w-full">
            <Tabs value={currentTab} onValueChange={onTabChange}>
                <Tabs.List class="w-full flex justify-between gap-4">
                    <Tabs.Trigger
                        class="tab-trigger flex-1 text-2xl font-bold"
                        value="/">Home</Tabs.Trigger
                    >
                    <Tabs.Trigger
                        class="tab-trigger flex-1 text-2xl font-bold"
                        value="/about">About</Tabs.Trigger
                    >
                    <Tabs.Trigger
                        class="tab-trigger flex-1 text-2xl font-bold"
                        value="/projects">Projects</Tabs.Trigger
                    >
                    <Tabs.Trigger
                        class="tab-trigger flex-1 text-2xl font-bold"
                        value="/contact">Contact</Tabs.Trigger
                    >

                    <Tabs.Indicator class="tab-indicator" />
                </Tabs.List>
            </Tabs>
        </div>
    </div>

    <main class="flex-1 overflow-y-auto p-8 bg-white">
        {@render children()}
    </main>
</div>
