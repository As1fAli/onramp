<template>
    <div>
        <menu-dropdown :toggle-text="language" class="hidden lg:block">
            <menu-dropdown-item
                v-for="(lang, slug) in languages"
                :key="slug"
                :text="lang"
                @clicked="choose(slug)"
            ></menu-dropdown-item>
        </menu-dropdown>

        <div class="relative z-50 mb-6 lg:hidden">
            <button
                v-if="isOpen"
                @click="close"
                tabindex="-1"
                class="fixed inset-0 hidden w-full h-full cursor-default"
                aria-label="close language switcher"
            ></button>

            <div class="px-6 py-3 lg:p-0">
                <label
                    for="language-switcher"
                    class="flex items-center -ml-2 text-white focus:outline-none "
                >
                    <button
                        @click="toggle"
                        id="language-switcher"
                        tabindex="1"
                        class="mr-3 font-bold text-body focus:outline-none focus:border-white"
                    >
                        {{ language }}
                    </button>

                    <svg
                        class="w-3 h-auto transition duration-300 ease-in-out stroke-current text-mint"
                        :class="{ 'transform -scale-y-100': isOpen }"
                        xmlns="http://www.w3.org/2000/svg"
                        viewBox="0 0 16 11"
                    >
                        <path
                            d="M2 2l6 6 6-6"
                            stroke-width="3"
                            fill="none"
                            fill-rule="evenodd"
                            stroke-linecap="round"
                        />
                    </svg>
                </label>
            </div>

            <transition name="slide">
                <div
                    v-if="isOpen"
                    class="overflow-hidden bg-steel lg:absolute lg:mt-12 lg:shadow-xl lg:left-0 lg:top-0"
                >
                    <button
                        v-for="(lang, slug) in languages"
                        :key="slug"
                        :text="lang"
                        @clicked="choose(slug)"
                        class="block w-full px-6 py-2 text-base font-normal text-left text-white transition-colors duration-200 ease-in-out focus:outline-none hover:bg-purple"
                    >
                        {{ lang }}
                    </button>
                </div>
            </transition>
        </div>
    </div>
</template>

<script>
import toggle from "../mixins/toggle";

export default {
    mixins: [toggle],

    props: {
        language: {
            type: String,
            required: true
        },

        languages: {
            type: Object
        }
    },

    data() {
        return {
            domLocation: window.location
        };
    },

    methods: {
        choose(value) {
            axios
                .patch(route("user.preferences.update", { locale: "en" }), {
                    locale: value
                })
                .then(() => {
                    let segments = this.domLocation.pathname.split("/");
                    segments[1] = value;
                    window.location = `${
                        this.domLocation.origin
                    }${segments.join("/")}`;
                })
                .catch(error => {
                    alert("Error!");
                });
        }
    }
};
</script>

<style scoped>
.slide-enter-active,
.slide-leave-active {
    transition: all 500ms ease-in-out;
}

.slide-enter-to,
.slide-leave {
    max-height: 1000px;
    overflow: hidden;
}

.slide-enter,
.slide-leave-to {
    overflow: hidden;
    max-height: 0;
}
</style>
