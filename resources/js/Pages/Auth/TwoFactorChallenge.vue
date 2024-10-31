<script setup lang="ts">
import { Head, useForm } from '@inertiajs/vue3';
import {
    PinInput,
    PinInputGroup,
    PinInputInput,
    PinInputSeparator,
} from '@/components/ui/pin-input';
import { Button } from '@/components/ui/button';
import { Card, CardHeader, CardContent, CardFooter } from '@/components/ui/card';
import { ref, nextTick } from 'vue';

const recovery = ref(false);

const form = useForm({
    code: '', // Initialize code as a single string
    recovery_code: '',
});

const codeArray = ref<string[]>(Array(6).fill('')); // Use as v-model for PinInput
const recoveryCodeInput = ref<HTMLInputElement | null>(null);

const handleComplete = (code: string[]) => {
    form.code = code.join(''); // Join the code array to form a continuous string
    console.log("OTP Code Entered:", form.code); // Log the OTP code in the browser console
};

const toggleRecovery = async () => {
    recovery.value = !recovery.value;

    await nextTick();

    if (recovery.value) {
        recoveryCodeInput.value?.focus();
        codeArray.value = Array(6).fill(''); // Clear code array when toggling to recovery mode
        form.code = ''; // Clear form code to prevent validation issues
    } else {
        form.recovery_code = ''; // Clear recovery code when toggling back to OTP mode
    }
};

const submit = () => {
    // Ensure code is properly set before submitting
    form.code = codeArray.value.join(''); // Ensure code is a continuous string before submitting
    if (form.code.length !== 6) {
        form.setError('code', 'The code field must be exactly 6 digits.');
        return;
    }
    form.post(route('two-factor.login'));
};
</script>

<template>
    <Head title="Two-factor Confirmation" />

    <div class="flex items-center justify-center min-h-screen bg-gray-50 px-4">
        <Card class="w-full max-w-lg p-8 shadow-md border border-gray-200 rounded-lg bg-white"> <!-- Adjusted max-w-lg and p-8 for larger card -->
            <CardHeader>
                <h2 class="text-2xl font-semibold text-center text-gray-800">Two-factor Confirmation</h2>
            </CardHeader>

            <CardContent class="space-y-6">
                <div class="text-center text-gray-600 dark:text-gray-400">
                    <template v-if="!recovery">
                        <p>Please enter the authentication code from your authenticator app.</p>
                    </template>
                    <template v-else>
                        <p>Please enter one of your emergency recovery codes.</p>
                    </template>
                </div>

                <form @submit.prevent="submit" class="space-y-4">
                    <div v-if="!recovery" class="space-y-4">
                        <label for="code" class="block text-sm font-medium text-gray-700 dark:text-gray-300 text-center">
                            Authentication Code
                        </label>

                        <PinInput
                            id="pin-input"
                            v-model="codeArray"
                            placeholder="○"
                            @complete="handleComplete"
                            class="flex justify-center"
                        >
                            <PinInputGroup class="flex gap-2 max-w-sm mx-auto"> <!-- Adjusted max-w-sm for input group -->
                                <template v-for="(id, index) in 6" :key="index">
                                    <PinInputInput
                                        class="h-12 w-12 rounded-md border border-gray-300 text-lg text-center text-gray-700 focus:border-indigo-500 focus:ring-indigo-500 focus:outline-none"
                                        :index="index"
                                    />
                                    <template v-if="index !== 5">
                                        <PinInputSeparator class="text-gray-400">•</PinInputSeparator>
                                    </template>
                                </template>
                            </PinInputGroup>
                        </PinInput>

                        <p v-if="form.errors.code" class="mt-2 text-sm text-red-600 text-center">{{ form.errors.code }}</p>
                    </div>

                    <div v-else class="space-y-4">
                        <label for="recovery_code" class="block text-sm font-medium text-gray-700 dark:text-gray-300 text-center">
                            Recovery Code
                        </label>
                        <input
                            id="recovery_code"
                            ref="recoveryCodeInput"
                            v-model="form.recovery_code"
                            type="text"
                            class="mt-2 block w-full p-2 border border-gray-300 rounded-lg text-gray-700 focus:border-indigo-500 focus:ring-indigo-500 focus:outline-none"
                            autocomplete="one-time-code"
                            placeholder="Enter recovery code"
                        />
                        <p v-if="form.errors.recovery_code" class="mt-2 text-sm text-red-600 text-center">{{ form.errors.recovery_code }}</p>
                    </div>

                    <div class="flex items-center justify-center mt-4">
                        <button
                            type="button"
                            class="text-gray-600 dark:text-gray-400 hover:text-indigo-600 underline cursor-pointer"
                            @click.prevent="toggleRecovery"
                        >
                            <template v-if="!recovery">
                                Use a recovery code
                            </template>
                            <template v-else>
                                Use an authentication code
                            </template>
                        </button>
                    </div>
                </form>
            </CardContent>

            <CardFooter class="flex justify-center mt-6">
                <Button
                    @click="submit"
                    :disabled="form.processing"
                    class="w-full max-w-xs rounded-lg"
                    :loading="form.processing"
                >
                    Log in
                </Button>
            </CardFooter>
        </Card>
    </div>
</template>
