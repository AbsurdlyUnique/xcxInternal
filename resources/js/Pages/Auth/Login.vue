<script setup>
import { Head, Link, useForm } from '@inertiajs/vue3';
import { Button } from '@/Components/ui/button';
import { Card, CardContent, CardHeader, CardFooter } from '@/Components/ui/card';
import { Input } from '@/Components/ui/input';
import { Checkbox } from '@/Components/ui/checkbox';

defineProps({
    canResetPassword: Boolean,
    status: String,
});

const form = useForm({
    email: '',
    password: '',
    remember: false,
});

const submit = () => {
    form.transform(data => ({
        ...data,
        remember: form.remember ? 'on' : '',
    })).post(route('login'), {
        onFinish: () => form.reset('password'),
    });
};
</script>

<template>
    <Head title="Log in" />

    <div class="flex items-center justify-center min-h-screen bg-gray-50">
        <Card class="w-full max-w-md p-6 shadow-lg border rounded-lg">
            <CardHeader>
                <h2 class="text-lg font-semibold text-center">Log in</h2>
            </CardHeader>

            <CardContent>
                <div v-if="status" class="mb-4 text-sm text-green-600 dark:text-green-400">
                    {{ status }}
                </div>

                <form @submit.prevent="submit">
                    <div class="mb-4">
                        <label for="email" class="block text-sm font-medium text-gray-700 dark:text-gray-300">
                            Email
                        </label>
                        <Input
                            id="email"
                            v-model="form.email"
                            type="email"
                            placeholder="Enter your email"
                            class="mt-1 block w-full"
                            required
                            autofocus
                            autocomplete="username"
                        />
                        <p v-if="form.errors.email" class="mt-2 text-sm text-red-600">{{ form.errors.email }}</p>
                    </div>

                    <div class="mb-4">
                        <label for="password" class="block text-sm font-medium text-gray-700 dark:text-gray-300">
                            Password
                        </label>
                        <Input
                            id="password"
                            v-model="form.password"
                            type="password"
                            placeholder="Enter your password"
                            class="mt-1 block w-full"
                            required
                            autocomplete="current-password"
                        />
                        <p v-if="form.errors.password" class="mt-2 text-sm text-red-600">{{ form.errors.password }}</p>
                    </div>

                    <div class="flex items-center mb-4">
                        <Checkbox v-model="form.remember" id="remember" />
                        <label for="remember" class="ml-2 text-sm text-gray-600 dark:text-gray-400">
                            Remember me
                        </label>
                    </div>

                    <div class="flex items-center justify-between">
                        <Link v-if="canResetPassword" :href="route('password.request')" class="text-sm text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-gray-100">
                            Forgot your password?
                        </Link>
                    </div>
                </form>
            </CardContent>

            <CardFooter class="flex justify-end">
                <Button @click="submit" :disabled="form.processing" :loading="form.processing">
                    Log in
                </Button>
            </CardFooter>
        </Card>
    </div>
</template>
