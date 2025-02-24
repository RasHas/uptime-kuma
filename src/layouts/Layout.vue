<template>
    <div :class="classes">
        <div v-if="! $root.socket.connected && ! $root.socket.firstConnect" class="lost-connection">
            <div class="container-fluid">
                {{ $root.connectionErrorMsg }}
            </div>
        </div>

        <!-- Desktop header -->
        <header v-if="! $root.isMobile" class="d-flex flex-wrap justify-content-center py-3 mb-3 border-bottom">
            <router-link to="/dashboard" class="d-flex align-items-center mb-3 mb-md-0 me-md-auto text-dark text-decoration-none">
                <object class="bi me-2 ms-4" width="40" height="40" data="/icon.svg" />
                <span class="fs-4 title">Uptime Kuma</span>
            </router-link>

            <a v-if="hasNewVersion" target="_blank" href="https://github.com/louislam/uptime-kuma/releases" class="btn btn-info me-3">
                <font-awesome-icon icon="arrow-alt-circle-up" /> {{ $t("New Update") }}
            </a>

            <ul class="nav nav-pills">
                <li class="nav-item">
                    <router-link to="/dashboard" class="nav-link">
                        <font-awesome-icon icon="tachometer-alt" /> {{ $t("Dashboard") }}
                    </router-link>
                </li>
                <li class="nav-item">
                    <router-link to="/settings" class="nav-link">
                        <font-awesome-icon icon="cog" /> {{ $t("Settings") }}
                    </router-link>
                </li>
            </ul>
        </header>

        <!-- Mobile header -->
        <header v-else class="d-flex flex-wrap justify-content-center pt-2 pb-2 mb-3">
            <router-link to="/dashboard" class="d-flex align-items-center text-dark text-decoration-none">
                <object class="bi" width="40" height="40" data="/icon.svg" />
                <span class="fs-4 title ms-2">Uptime Kuma</span>
            </router-link>
        </header>

        <main>
            <!-- Add :key to disable vue router re-use the same component -->
            <router-view v-if="$root.loggedIn" :key="$route.fullPath" />
            <Login v-if="! $root.loggedIn && $root.allowLoginDialog" />
        </main>

        <footer>
            <div class="container-fluid">
                Uptime Kuma -
                {{ $t("Version") }}: {{ $root.info.version }} -
                <a href="https://github.com/louislam/uptime-kuma/releases" target="_blank" rel="noopener">{{ $t("Check Update On GitHub") }}</a>
            </div>
        </footer>

        <!-- Mobile Only -->
        <div v-if="$root.isMobile" style="width: 100%; height: 60px;" />
        <nav v-if="$root.isMobile" class="bottom-nav">
            <router-link to="/dashboard" class="nav-link">
                <div><font-awesome-icon icon="tachometer-alt" /></div>
                {{ $t("Dashboard") }}
            </router-link>

            <router-link to="/list" class="nav-link">
                <div><font-awesome-icon icon="list" /></div>
                {{ $t("List") }}
            </router-link>

            <router-link to="/add" class="nav-link">
                <div><font-awesome-icon icon="plus" /></div>
                {{ $t("Add") }}
            </router-link>

            <router-link to="/settings" class="nav-link">
                <div><font-awesome-icon icon="cog" /></div>
                {{ $t("Settings") }}
            </router-link>
        </nav>
    </div>
</template>

<script>
import Login from "../components/Login.vue";
import compareVersions from "compare-versions";

export default {

    components: {
        Login,
    },

    data() {
        return {}
    },

    computed: {

        // Theme or Mobile
        classes() {
            const classes = {};
            classes[this.$root.theme] = true;
            classes["mobile"] = this.$root.isMobile;
            return classes;
        },

        hasNewVersion() {
            if (this.$root.info.latestVersion && this.$root.info.version) {
                return compareVersions(this.$root.info.latestVersion, this.$root.info.version) >= 1;
            } else {
                return false;
            }
        },

    },

    watch: {
        $route(to, from) {
            this.init();
        },
    },

    mounted() {
        this.init();
    },

    methods: {
        init() {
            if (this.$route.name === "root") {
                this.$router.push("/dashboard")
            }
        },
    },

}
</script>

<style lang="scss" scoped>
@import "../assets/vars.scss";

.bottom-nav {
    z-index: 1000;
    position: fixed;
    bottom: 0;
    height: 60px;
    width: 100%;
    left: 0;
    background-color: #fff;
    box-shadow: 0 15px 47px 0 rgba(0, 0, 0, 0.05), 0 5px 14px 0 rgba(0, 0, 0, 0.05);
    text-align: center;
    white-space: nowrap;
    padding: 0 10px;

    a {
        text-align: center;
        width: 25%;
        display: inline-block;
        height: 100%;
        padding: 8px 10px 0;
        font-size: 13px;
        color: #c1c1c1;
        overflow: hidden;
        text-decoration: none;

        &.router-link-exact-active {
            color: $primary;
            font-weight: bold;
        }

        div {
            font-size: 20px;
        }
    }
}

main {
    min-height: calc(100vh - 160px);
}

.title {
    font-weight: bold;
}

.nav {
    margin-right: 25px;
}

.lost-connection {
    padding: 5px;
    background-color: crimson;
    color: white;
}

footer {
    font-size: 13px;
    margin-top: 10px;
    padding-bottom: 30px;
    margin-left: 10px;
    text-align: center;
}

footer {
    color: #aaa;
}

.dark {
    header {
        background-color: #161b22;
        border-bottom-color: #161b22 !important;

        span {
            color: #f0f6fc;
        }
    }

    .bottom-nav {
        background-color: $dark-bg;
    }
}

</style>
