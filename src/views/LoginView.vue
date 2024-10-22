<script setup lang="ts">
import { ref, reactive } from 'vue';
import { useRouter } from 'vue-router';
import { z } from 'zod'; // 假設使用 zod 做表單驗證

// 定義表單數據類型
interface FormData {
  email: string;
  password: string;
  remember: boolean;
}

// 定義錯誤類型
interface FormErrors {
  email?: string;
  password?: string;
}

// 表單驗證 schema
const loginSchema = z.object({
  email: z.string().email('請輸入有效的電子郵件格式'),
  password: z.string().min(6, '密碼長度至少需要6個字元'),
});

const router = useRouter();

// 表單數據
const formData = reactive<FormData>({
  email: '',
  password: '',
  remember: false,
});

// 錯誤訊息
const errors = reactive<FormErrors>({});

// 狀態管理
const isLoading = ref<boolean>(false);
const loginError = ref<string>('');

// 表單驗證
const validateForm = (): boolean => {
  try {
    loginSchema.parse({
      email: formData.email,
      password: formData.password,
    });

    errors.email = undefined;
    errors.password = undefined;

    return true;
  } catch (error) {
    if (error instanceof z.ZodError) {
      error.errors.forEach(err => {
        if (err.path[0] === 'email') {
          errors.email = err.message;
        }
        if (err.path[0] === 'password') {
          errors.password = err.message;
        }
      });
    }
    return false;
  }
};

// 登入處理
const handleSubmit = async () => {
  if (!validateForm()) return;

  try {
    isLoading.value = true;
    loginError.value = '';

    // 模擬 API 呼叫
    await new Promise(resolve => setTimeout(resolve, 1500));

    // 實際專案中的 API 呼叫
    // const response = await api.login(formData)

    if (formData.remember) {
      localStorage.setItem('user-email', formData.email);
    }

    router.push('/dashboard');
  } catch (error) {
    console.error(error);
    loginError.value = '登入失敗，請檢查您的帳號密碼是否正確';
  } finally {
    isLoading.value = false;
  }
};
</script>

<template>
  <div class="bg-base-200 flex items-center justify-center min-h-screen">
    <div class="card w-96 bg-base-100 shadow-xl">
      <div class="card-body">
        <h2 class="card-title text-2xl font-bold mb-6">Login</h2>
        <form>
          <div class="form-control">
            <label class="label">
              <span class="label-text">Email</span>
            </label>
            <label class="input input-bordered flex items-center gap-2">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 16 16"
                fill="currentColor"
                class="w-4 h-4 opacity-70"
              >
                <path
                  d="M2.5 3A1.5 1.5 0 0 0 1 4.5v.793c.026.009.051.02.076.032L7.674 8.51c.206.1.446.1.652 0l6.598-3.185A.755.755 0 0 1 15 5.293V4.5A1.5 1.5 0 0 0 13.5 3h-11Z"
                />
                <path
                  d="M15 6.954 8.978 9.86a2.25 2.25 0 0 1-1.956 0L1 6.954V11.5A1.5 1.5 0 0 0 2.5 13h11a1.5 1.5 0 0 0 1.5-1.5V6.954Z"
                />
              </svg>
              <input
                type="email"
                class="grow"
                placeholder="email@example.com"
              />
            </label>
          </div>
          <div class="form-control mt-4">
            <label class="label">
              <span class="label-text">Password</span>
            </label>
            <label class="input input-bordered flex items-center gap-2">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 16 16"
                fill="currentColor"
                class="w-4 h-4 opacity-70"
              >
                <path
                  fill-rule="evenodd"
                  d="M14 6a4 4 0 0 1-4.899 3.899l-1.955 1.955a.5.5 0 0 1-.353.146H5v1.5a.5.5 0 0 1-.5.5h-2a.5.5 0 0 1-.5-.5v-2.293a.5.5 0 0 1 .146-.353l3.955-3.955A4 4 0 1 1 14 6Zm-4-2a.75.75 0 0 0 0 1.5.5.5 0 0 1 .5.5.75.75 0 0 0 1.5 0 2 2 0 0 0-2-2Z"
                  clip-rule="evenodd"
                />
              </svg>
              <input
                type="password"
                class="grow"
                placeholder="Enter password"
              />
            </label>
            <label class="label">
              <a href="#" class="label-text-alt link link-hover"
                >Forgot password?</a
              >
            </label>
          </div>
          <div class="form-control mt-6">
            <button class="btn btn-primary" @click="handleSubmit">登入</button>
          </div>
        </form>
        <div class="divider">OR</div>
        <div class="text-center">
          <p>Don't have an account?</p>
          <a href="#" class="link link-primary">Sign up now</a>
        </div>
      </div>
    </div>
  </div>
</template>
