<script>
	let newUsername = '';
	let newPassword = '';
	let confirmPassword = '';
	let message = '';
	let messageType = '';

	function createUser() {
		message = '';
		messageType = '';

		if (!newUsername || !newPassword || !confirmPassword) {
			message = 'Udfyld alle felter.';
			messageType = 'error';
			return;
		}

		if (newPassword !== confirmPassword) {
			message = 'De to passwords er ikke ens.';
			messageType = 'error';
			return;
		}

		const users = JSON.parse(localStorage.getItem('users') || '[]');

		const userExists = users.some((user) => user.username === newUsername);

		if (userExists || newUsername === 'patient' || newUsername === 'doctor') {
			message = 'Brugernavnet findes allerede.';
			messageType = 'error';
			return;
		}

		const newUser = {
			username: newUsername,
			password: newPassword,
			role: 'patient'
		};

		users.push(newUser);
		localStorage.setItem('users', JSON.stringify(users));

		message = 'Patientbruger oprettet. Du sendes tilbage til login.';
		messageType = 'success';

		newUsername = '';
		newPassword = '';
		confirmPassword = '';

		setTimeout(() => {
			window.location.href = '/';
		}, 1200);
	}
</script>

<main class="page">
	<section class="card">
		<div class="heart">♥</div>

		<h1>Opret patientbruger</h1>
		<p class="subtitle">
			Opret en patientbruger til HeartLink-prototypen
		</p>

		<div class="form">
			<label>Brugernavn</label>
			<input bind:value={newUsername} placeholder="Fx anna eller patient2" />

			<label>Password</label>
			<input bind:value={newPassword} type="password" placeholder="Vælg password" />

			<label>Gentag password</label>
			<input bind:value={confirmPassword} type="password" placeholder="Gentag password" />

			<button onclick={createUser}>Opret patientbruger</button>
		</div>

		<a class="secondary-button" href="/">Tilbage til login</a>

		{#if message}
			<p class={messageType}>{message}</p>
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
		background: linear-gradient(135deg, #dbeafe, #ffedd5);
		font-family: Arial, sans-serif;
	}

	.card {
		width: 100%;
		max-width: 460px;
		background: rgba(255, 255, 255, 0.94);
		border-radius: 28px;
		padding: 38px;
		box-shadow: 0 20px 50px rgba(37, 99, 235, 0.16);
		text-align: center;
		border: 1px solid rgba(255, 255, 255, 0.85);
	}

	.heart {
		font-size: 72px;
		color: #f97316;
		animation: heartbeat 1.2s infinite;
		margin-bottom: 10px;
	}

	@keyframes heartbeat {
		0% { transform: scale(1); }
		15% { transform: scale(1.18); }
		30% { transform: scale(1); }
		45% { transform: scale(1.12); }
		60% { transform: scale(1); }
		100% { transform: scale(1); }
	}

	h1 {
		font-size: 34px;
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
		background: #f8fbff;
	}

	input:focus {
		outline: none;
		border-color: #2563eb;
		box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.18);
	}

	button,
	.secondary-button {
		display: block;
		width: 100%;
		box-sizing: border-box;
		text-align: center;
		padding: 14px;
		border: none;
		border-radius: 16px;
		font-weight: bold;
		font-size: 16px;
		cursor: pointer;
		margin-top: 6px;
		text-decoration: none;
	}

	button {
		background: linear-gradient(135deg, #2563eb, #f97316);
		color: white;
	}

	.secondary-button {
		background: white;
		color: #334155;
		border: 1px solid #cbd5e1;
		margin-top: 14px;
	}

	button:hover,
	.secondary-button:hover {
		opacity: 0.9;
	}

	.error {
		color: #dc2626;
		margin-top: 16px;
		font-weight: bold;
		text-align: center;
	}

	.success {
		color: #16a34a;
		margin-top: 16px;
		font-weight: bold;
		text-align: center;
	}
</style>