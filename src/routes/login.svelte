<script>
	import { TextInput, PasswordInput } from 'carbon-components-svelte';

	import { base } from '$app/paths';
	import { loginApi } from '../api/userApi';
	import { user } from '../stores/userStore';
	import { browser } from '$app/env';
	import { goto } from '$app/navigation';
	// import { onMount } from 'svelte';
	import { toast } from '@zerodevx/svelte-toast';

	let sid;
	let password;

	function handleLogin() {
		loginApi(sid, password).then((response) => {
			if (response.status == 200) {
				toast.push('成功登录');
				const newUser = {
					sid: response.data.sid,
					name: response.data.name,
					password: response.data.password,
					info: response.data.info
				};
				user.set(newUser);
				if (browser) {
					localStorage.setItem('user', JSON.stringify(newUser));
				}
				goto(`/`);
			} else {
				toast.push(response.data.detail, {
					theme: {
						'--toastBackground': '#F56565',
						'--toastBarBackground': '#C53030'
					}
				});
			}
		});
	}
</script>

<svelte:head>
	<meta charset="UTF-8" />
	<meta name="viewport" content="width=device-width, initial-scale=1">
	<title>登录</title>
</svelte:head>

<body class="flex flex-col items-center justify-center mt-8">
	<!-- Component Start -->
	<h1 class="font-bold text-2xl">Sign In</h1>
	<form
		class="flex flex-col bg-white rounded shadow-lg p-12 mt-12"
		on:submit|preventDefault={handleLogin}
	>
		<TextInput
			labelText="学号"
			placeholder="Enter your student id..."
			required
			bind:value={sid}
		/>
		<div class="mt-4">
			<PasswordInput
				required
				type="password"
				labelText="密码"
				placeholder="Enter password..."
				bind:value={password}
			/>
		</div>
		<button
			class="flex items-center justify-center h-10 px-6 w-72 bg-blue-600 mt-8 font-semibold text-sm text-blue-100 hover:bg-blue-700"
			type="submit">Login</button
		>
		<div class="flex mt-6 justify-center text-xs">
			<span class="mx-2 text-gray-300">Don't have an account yet?</span>
			<a class="text-blue-400 hover:text-blue-500" href="{base}/register">Sign Up</a>
		</div>
	</form>
	<!-- Component End  -->
</body>
