<script>
	let username = '';
	let password = '';
	let error = '';

	function login() {
		if (username === 'patient' && password === '1234') {
			localStorage.setItem('role', 'patient');
			window.location.href = '/patient';
		} else if (username === 'doctor' && password === '1234') {
			localStorage.setItem('role', 'clinician');
			window.location.href = '/clinician';
		} else {
			error = 'Forkert brugernavn eller password';
		}
	}
</script>

<main class="page">
	<section class="card">
		<div class="heart">♥</div>

		<h1>HeartLink</h1>
		<p class="subtitle">
			Telemedicinsk prototype til hjemmemonitorering af hjertesvigtpatienter
		</p>

		<div class="form">
			<label>Brugernavn</label>
			<input bind:value={username} placeholder="Indtast brugernavn" />

			<label>Password</label>
			<input bind:value={password} type="password" placeholder="Indtast password" />

			<button onclick={login}>Login</button>
		</div>

		{#if error}
			<p class="error">{error}</p>
		{/if}
	</section>
</main>

<style>
	.page {
		min-height: 100vh;
		display: flex;
		align-items: center;
		justify-content: center;
		padding: 24px;
		background: linear-gradient(135deg, #ffe4ef, #dff5ff);
		font-family: Arial, sans-serif;
	}

	.card {
		width: 100%;
		max-width: 430px;
		background: rgba(255, 255, 255, 0.92);
		border-radius: 28px;
		padding: 38px;
		box-shadow: 0 20px 50px rgba(80, 80, 120, 0.18);
		text-align: center;
		border: 1px solid rgba(255, 255, 255, 0.8);
	}

	.heart {
		font-size: 72px;
		color: #ec5c8a;
		animation: heartbeat 1.2s infinite;
		margin-bottom: 10px;
	}

	@keyframes heartbeat {
		0% {
			transform: scale(1);
		}
		15% {
			transform: scale(1.18);
		}
		30% {
			transform: scale(1);
		}
		45% {
			transform: scale(1.12);
		}
		60% {
			transform: scale(1);
		}
		100% {
			transform: scale(1);
		}
	}

	h1 {
		font-size: 38px;
		margin: 0;
		color: #1e293b;
	}

	.subtitle {
		color: #64748b;
		margin-bottom: 28px;
		line-height: 1.5;
	}

	.form {
		text-align: left;
	}

	label {
		display: block;
		margin-bottom: 6px;
		font-weight: bold;
		color: #334155;
	}

	input {
		width: 100%;
		box-sizing: border-box;
		padding: 13px;
		border-radius: 14px;
		border: 1px solid #cbd5e1;
		margin-bottom: 16px;
		font-size: 16px;
		background: #f7fbff;
	}

	input:focus {
		outline: none;
		border-color: #ec5c8a;
		box-shadow: 0 0 0 3px rgba(236, 92, 138, 0.2);
	}

	button {
		width: 100%;
		padding: 14px;
		border: none;
		border-radius: 16px;
		background: linear-gradient(135deg, #ec5c8a, #60a5fa);
		color: white;
		font-weight: bold;
		font-size: 16px;
		cursor: pointer;
		margin-top: 6px;
	}

	button:hover {
		opacity: 0.9;
	}

	.error {
		color: #dc2626;
		margin-top: 16px;
		font-weight: bold;
		text-align: center;
	}
</style>
<p>Visit <a href="https://svelte.dev/docs/kit">svelte.dev/docs/kit</a> to read the documentation</p>
