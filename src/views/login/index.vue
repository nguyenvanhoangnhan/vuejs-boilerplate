<script setup lang="ts">
import { useAuthStore } from '@/stores/auth'
import { useMutation } from '@tanstack/vue-query'
import { reactive } from 'vue'
import { Icon } from '@iconify/vue'
import { Form, FormItem, Input, Button } from 'ant-design-vue'
import type { LoginRequest } from '@/api/auth/auth.dto.ts'
import GotoRegisterPageButton from './GotoRegisterPageButton.vue'
import GotoForgotPasswordButton from './GotoForgotPasswordButton.vue'
import SSOButtons from './SSOButtons.vue'
import router from '@/router'

const auth = useAuthStore()

const loginFormState = reactive<LoginRequest>({
    email: '',
    password: ''
})

if (auth.isLoggedIn) router.push('/')

const { mutate, isPending } = useMutation({
    mutationKey: ['login'],
    mutationFn: async () => {
        await auth.login(loginFormState)
        router.push(auth.returnUrl || '/')
    }
})

const handleLoginFailed = (errInfo: any) => {
    console.log('Failed:', errInfo)
}
</script>

<template>
    <div id="login" class="my-auto">
        <!-- Title -->
        <h1 class="login__title font-black text-5xl mb-16">{{ $t('login.big_title') }}</h1>

        <!-- Login form -->
        <Form
            class="login__form"
            :model="loginFormState"
            layout="vertical"
            @finish="mutate"
            @finishFailed="handleLoginFailed"
        >
            <!-- ---- -->
            <FormItem
                name="email"
                :rules="[{ required: true, message: $t('login.form.validation.email.required') }]"
            >
                <Input
                    type="text"
                    size="large"
                    v-model:value="loginFormState.email"
                    :placeholder="$t('login.form.email')"
                    auto-complete="off"
                >
                    <template #prefix> <Icon icon="ph:envelope-simple-bold" /> </template
                ></Input>
            </FormItem>

            <FormItem
                name="password"
                :rules="[
                    { required: true, message: $t('login.form.validation.password.required') }
                ]"
            >
                <Input
                    type="password"
                    size="large"
                    v-model:value="loginFormState.password"
                    :placeholder="$t('login.form.password')"
                    auto-complete="off"
                >
                    <template #prefix> <Icon icon="ph:lock-simple-bold" /> </template
                ></Input>
            </FormItem>

            <!-- ---- -->
            <div class="forgot-password mb-2 flex justify-end">
                <GotoForgotPasswordButton />
            </div>
            <FormItem>
                <Button
                    :loading="isPending"
                    size="large"
                    class="font-bold"
                    type="primary"
                    html-type="submit"
                    block
                >
                    {{ $t('login.form.submit_btn') }}
                </Button>
            </FormItem>
        </Form>

        <!-- Suggest create new account -->
        <div class="login__suggest-register flex flex-col mt-20">
            <span class="text-center text-[16px] mb-2 cursor-default">
                {{ $t('login.register_reminder') }}
            </span>
            <GotoRegisterPageButton />
        </div>

        <div class="login__sso">
            <SSOButtons />
        </div>
    </div>
</template>

<style lang="less" scoped></style>