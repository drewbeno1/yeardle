<script setup>
import { ref, defineProps } from "vue";

const props = defineProps({
    isOpen: Boolean,
})

const isOpen = ref(false)

</script>

<template>
    <div class="flex items-center w-20 justify-start">
        <!-- class= menu-left -->
        <button class="px-1" @click="isOpen = true" title="Help">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8  " fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    d="M8.228 9c.549-1.165 2.03-2 3.772-2 2.21 0 4 1.343 4 3 0 1.4-1.278 2.575-3.006 2.907-.542.104-.994.54-.994 1.093m0 3h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                />
            </svg>
        </button>
        <div
            v-show="isOpen"
            class="
                overflow-x-hidden
                overflow-y-auto
                fixed
                inset-0
                z-50
                bg-gray-700 bg-opacity-50
                outline-none
                focus:outline-none
                justify-center
                items-center
                md:flex
                
            "
        >
            <div class="relative w-auto my-6 mx-auto max-w-6xl">
                <!-- content -->
                <div class="border-0 rounded-lg shadow-lg relative md:flex md:flex-col w-full bg-white outline-none focus:outline-none">
                    <!-- header -->
                    <div class="flex items-start justify-between p-5 border-b border-solid border-slate-800 rounded-t">
                        <h3 class="text-3xl font-semibold">How to Play</h3>
                        <svg
                            @click="isOpen = false"
                            xmlns="http://www.w3.org/2000/svg"
                            class="w-8 h-8 text-red-900 cursor-pointer  "
                            fill="none"
                            viewBox="0 0 24 24"
                            stroke="currentColor"
                        >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                stroke-width="2"
                                d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z"
                            />
                        </svg>
                    </div>
                    <!-- body -->
                    <div class="relative p-6 flex-auto">
                        <p class="my-4 text-lg leading-relaxed text-slate-800">
                            Guess the Year the photo was taken in 5 tries! <br>
                            <br>
                            Each guess must be a 4-number year. Press Enter to submit your guess.<br>
                            The number tiles will change color depending on the accuracy of your guess!<br>
                            <br>
                            Created by DBreezy, inspired by JHippy
                        </p>
                    </div>
                    <!-- footer -->
                    <div class="flex items-center justify-between p-6 border-t border-solid border-slate-200 rounded-b">
                        <a href="https://github.com/drewbeno1" target="_blank"><img alt="Source Code" class="w-11 h-11 object-left  " title="View Source Code on GitHub" src="../assets/git.png"></a>
                        <button class="text-green-600 bg-transparent border border-solid border-green-600 hover:bg-green-600 hover:text-white active:bg-green-700 font-bold uppercase text-sm px-6 py-3 rounded outline-none focus:outline-none mr-1 mb-1 ease-linear transition-all duration-150" @click="isOpen = false">
                            Ok, let's play!
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="text-4xl font-bold tracking-wide ">
         Yeardle
    </div>

    <div class="flex w-20 justify-end">
        <button class="px-1 bg-white text-white" title="Toggle Dark Mode" disabled>
            <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z"
                />
            </svg>
        </button>
    </div>
</template>


<style>
</style>


