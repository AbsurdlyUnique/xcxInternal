<script setup>
import { ref } from 'vue';
import { Head, Link, router } from '@inertiajs/vue3';
import ApplicationMark from '@/Components/ApplicationMark.vue';
import {
    Sheet,
    SheetContent,
    SheetTrigger,
} from "@/components/ui/sheet";
import {
    DropdownMenu,
    DropdownMenuContent,
    DropdownMenuItem,
    DropdownMenuLabel,
    DropdownMenuSeparator,
    DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";
import { Button } from "@/components/ui/button";
import { ScrollArea } from "@/components/ui/scroll-area";
import { Avatar, AvatarImage, AvatarFallback } from "@/components/ui/avatar";
import {
    LayoutDashboard,
    Settings,
    Users,
    LogOut,
    Menu,
    ChevronDown,
    Building2
} from 'lucide-vue-next';

defineProps({
    title: String,
});

const showMobileSidebar = ref(false);

const switchToTeam = (team) => {
    router.put(route('current-team.update'), {
        team_id: team.id,
    }, {
        preserveState: false,
    });
};

const logout = () => {
    router.post(route('logout'));
};
</script>

<template>
    <div>
        <Head :title="title" />

        <div class="flex min-h-screen">
            <!-- Desktop Sidebar -->
            <div class="hidden lg:flex lg:flex-col lg:w-64 lg:fixed lg:inset-y-0">
                <div class="flex flex-col flex-grow border-r bg-white dark:bg-gray-800">
                    <!-- Logo -->
                    <div class="flex items-center h-16 px-4 border-b">
                        <Link :href="route('dashboard')" class="flex items-center">
                            <ApplicationMark class="h-8 w-8" />
                            <span class="ml-2 text-lg font-semibold">Dashboard</span>
                        </Link>
                    </div>

                    <!-- Sidebar Content -->
                    <ScrollArea class="flex-1">
                        <nav class="space-y-1 px-2 py-4">
                            <!-- Dashboard Link -->
                            <Link
                                :href="route('dashboard')"
                                class="flex items-center px-3 py-2 text-sm rounded-md"
                                :class="route().current('dashboard') ? 'bg-gray-100 dark:bg-gray-700 text-gray-900 dark:text-white' : 'text-gray-600 hover:bg-gray-50 dark:hover:bg-gray-700'"
                            >
                                <LayoutDashboard class="mr-2 h-4 w-4" />
                                Dashboard
                            </Link>

                            <!-- Team Section (if team features enabled) -->
                            <div v-if="$page.props.jetstream.hasTeamFeatures" class="py-4">
                                <div class="px-3 text-xs font-semibold text-gray-500 uppercase tracking-wider">
                                    Teams
                                </div>

                                <Link
                                    :href="route('teams.show', $page.props.auth.user.current_team)"
                                    class="flex items-center px-3 py-2 mt-2 text-sm rounded-md"
                                    :class="route().current('teams.show') ? 'bg-gray-100 dark:bg-gray-700 text-gray-900 dark:text-white' : 'text-gray-600 hover:bg-gray-50 dark:hover:bg-gray-700'"
                                >
                                    <Users class="mr-2 h-4 w-4" />
                                    Team Settings
                                </Link>
                            </div>
                        </nav>
                    </ScrollArea>

                    <!-- User Menu -->
                    <div class="border-t p-4">
                        <DropdownMenu>
                            <DropdownMenuTrigger as="div">
                                <Button variant="ghost" class="w-full justify-start">
                                    <div class="flex items-center">
                                        <Avatar class="h-8 w-8">
                                            <AvatarImage
                                                v-if="$page.props.jetstream.managesProfilePhotos"
                                                :src="$page.props.auth.user.profile_photo_url"
                                                :alt="$page.props.auth.user.name"
                                            />
                                            <AvatarFallback>
                                                {{ $page.props.auth.user.name.charAt(0) }}
                                            </AvatarFallback>
                                        </Avatar>
                                        <span class="ml-2 flex-1 text-left">{{ $page.props.auth.user.name }}</span>
                                        <ChevronDown class="ml-2 h-4 w-4" />
                                    </div>
                                </Button>
                            </DropdownMenuTrigger>
                            <DropdownMenuContent class="w-56">
                                <DropdownMenuLabel>My Account</DropdownMenuLabel>
                                <DropdownMenuItem @click="router.visit(route('profile.show'))">
                                    <Settings class="mr-2 h-4 w-4" />
                                    Profile
                                </DropdownMenuItem>
                                <DropdownMenuItem v-if="$page.props.jetstream.hasApiFeatures" @click="router.visit(route('api-tokens.index'))">
                                    API Tokens
                                </DropdownMenuItem>
                                <DropdownMenuSeparator />
                                <DropdownMenuItem @click="logout">
                                    <LogOut class="mr-2 h-4 w-4" />
                                    Log Out
                                </DropdownMenuItem>
                            </DropdownMenuContent>
                        </DropdownMenu>
                    </div>
                </div>
            </div>

            <!-- Mobile Navigation -->
            <Sheet v-model:open="showMobileSidebar">
                <SheetTrigger asChild>
                    <Button variant="ghost" class="lg:hidden px-4">
                        <Menu class="h-6 w-6" />
                    </Button>
                </SheetTrigger>
                <SheetContent side="left" class="w-64 p-0">
                    <div class="flex flex-col h-full">
                        <!-- Mobile Logo -->
                        <div class="flex items-center h-16 px-4 border-b">
                            <ApplicationMark class="h-8 w-8" />
                            <span class="ml-2 text-lg font-semibold">Dashboard</span>
                        </div>

                        <!-- Mobile Navigation Items -->
                        <ScrollArea class="flex-1">
                            <nav class="space-y-1 px-2 py-4">
                                <Link
                                    :href="route('dashboard')"
                                    class="flex items-center px-3 py-2 text-sm rounded-md"
                                    :class="route().current('dashboard') ? 'bg-gray-100 dark:bg-gray-700 text-gray-900 dark:text-white' : 'text-gray-600 hover:bg-gray-50 dark:hover:bg-gray-700'"
                                >
                                    <LayoutDashboard class="mr-2 h-4 w-4" />
                                    Dashboard
                                </Link>

                                <!-- Mobile Team Section -->
                                <div v-if="$page.props.jetstream.hasTeamFeatures" class="py-4">
                                    <div class="px-3 text-xs font-semibold text-gray-500 uppercase tracking-wider">
                                        Teams
                                    </div>

                                    <Link
                                        :href="route('teams.show', $page.props.auth.user.current_team)"
                                        class="flex items-center px-3 py-2 mt-2 text-sm rounded-md"
                                        :class="route().current('teams.show') ? 'bg-gray-100 dark:bg-gray-700 text-gray-900 dark:text-white' : 'text-gray-600 hover:bg-gray-50 dark:hover:bg-gray-700'"
                                    >
                                        <Users class="mr-2 h-4 w-4" />
                                        Team Settings
                                    </Link>
                                </div>
                            </nav>
                        </ScrollArea>
                    </div>
                </SheetContent>
            </Sheet>

            <!-- Main Content -->
            <div class="lg:pl-64 flex flex-col flex-1">
                <!-- Page Header -->
                <header v-if="$slots.header" class="bg-white dark:bg-gray-800 shadow">
                    <div class="px-4 sm:px-6 lg:px-8 py-6">
                        <slot name="header" />
                    </div>
                </header>

                <!-- Main Content Area -->
                <main class="flex-1 p-4 sm:p-6 lg:p-8">
                    <slot />
                </main>
            </div>
        </div>
    </div>
</template>
