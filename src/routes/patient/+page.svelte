<script>
	import { onMount } from 'svelte';

	let weight = '';
	let previousWeight = '';
	let pulse = '';
	let systolic = '';
	let diastolic = '';
	let breathlessness = 'none';
	let swollenLegs = 'no';
	let medicationTaken = 'yes';
	let comment = '';

	let triageStatus = '';
	let triageMessage = '';
	let action = '';

	onMount(() => {
		const role = localStorage.getItem('role');

		if (role !== 'patient') {
			window.location.href = '/';
		}
	});

	function calculateTriage() {
		let score = 0;

		const weightNow = Number(weight);
		const weightBefore = Number(previousWeight);
		const pulseValue = Number(pulse);
		const systolicValue = Number(systolic);

		if (weightNow && weightBefore && weightNow - weightBefore >= 2) score += 2;
		if (pulseValue > 100) score += 1;
		if (systolicValue > 160 || systolicValue < 90) score += 1;
		if (breathlessness === 'mild') score += 1;
		if (breathlessness === 'moderate') score += 2;
		if (breathlessness === 'severe') score += 3;
		if (swollenLegs === 'yes') score += 1;
		if (medicationTaken === 'no') score += 1;

		if (score >= 5) {
			triageStatus = 'Rød';
			triageMessage = 'Der er tegn på mulig alvorlig forværring.';
			action =
				'Kontakt klinikken hurtigst muligt. Ved svær åndenød eller brystsmerter bør der søges akut hjælp.';
		} else if (score >= 2) {
			triageStatus = 'Gul';
			triageMessage = 'Der er tegn på mulig begyndende forværring.';
			action = 'Klinikken bør følge op, og du bør gentage målingerne i morgen.';
		} else {
			triageStatus = 'Grøn';
			triageMessage = 'Målingerne virker stabile.';
			action = 'Fortsæt vanlig behandling og daglig monitorering.';
		}

		const measurement = {
			patientName: localStorage.getItem('username') || 'Demo Patient',
			date: new Date().toLocaleString('da-DK'),
			weight,
			previousWeight,
			pulse,
			systolic,
			diastolic,
			breathlessness,
			swollenLegs,
			medicationTaken,
			comment,
			triageStatus,
			triageMessage,
			action
		};

		localStorage.setItem('latestMeasurement', JSON.stringify(measurement));
	}

	function getStatusClass(status) {
		if (status === 'Rød') return 'red';
		if (status === 'Gul') return 'yellow';
		if (status === 'Grøn') return 'green';
		return 'neutral';
	}

	function logout() {
		localStorage.removeItem('role');
		localStorage.removeItem('username');
		window.location.href = '/';
	}
</script>

<main class="page">
	<section class="wrapper">
		<div class="header-card">
			<div class="heart">♥</div>
			<div>
				<h1>Patient Dashboard</h1>
				<p>Daglig hjemmemonitorering for hjertesvigtpatienter</p>
			</div>
		</div>

		<div class="grid">
			<div class="card">
				<h2>Dagens målinger</h2>

				<label>Nuværende vægt (kg)</label>
				<input type="number" bind:value={weight} />

				<label>Tidligere vægt (kg)</label>
				<input type="number" bind:value={previousWeight} />

				<label>Puls</label>
				<input type="number" bind:value={pulse} />

				<label>Systolisk blodtryk</label>
				<input type="number" bind:value={systolic} />

				<label>Diastolisk blodtryk</label>
				<input type="number" bind:value={diastolic} />

				<label>Åndenød</label>
				<select bind:value={breathlessness}>
					<option value="none">Ingen</option>
					<option value="mild">Let</option>
					<option value="moderate">Moderat</option>
					<option value="severe">Svær</option>
				</select>

				<label>Hævede ben/ankler</label>
				<select bind:value={swollenLegs}>
					<option value="no">Nej</option>
					<option value="yes">Ja</option>
				</select>

				<label>Medicin taget i dag?</label>
				<select bind:value={medicationTaken}>
					<option value="yes">Ja</option>
					<option value="no">Nej</option>
				</select>

				<label>Kommentar til klinikken</label>
				<textarea
					rows="3"
					bind:value={comment}
					placeholder="Fx mere træt end normalt, hoste eller svimmelhed"
				></textarea>

				<button onclick={calculateTriage}>Gem måling og beregn status</button>
			</div>

			<div class="card">
				<h2>Automatisk vurdering</h2>

				{#if triageStatus}
					<div class="status-box">
						<div class="status-circle {getStatusClass(triageStatus)}"></div>
						<p class="status-title">Status beregnet</p>
						<p class="status-message">{triageMessage}</p>
						<p class="status-action">{action}</p>
					</div>
				{:else}
					<div class="status-box">
						<div class="status-circle neutral"></div>
						<p class="status-title">Ingen status endnu</p>
						<p class="status-message">
							Udfyld målingerne og tryk på knappen for at beregne patientens status.
						</p>
					</div>
				{/if}

				<div class="info-box">
					<h3>Prototype-logik</h3>
					<p>
						Systemet vurderer patientens tilstand ud fra vægtstigning, puls, blodtryk,
						åndenød, hævede ben og medicinstatus.
					</p>
				</div>

				<button class="logout-button" onclick={logout}>Log ud</button>
			</div>
		</div>
	</section>
</main>

<style>
	.page {
		min-height: 100vh;
		padding: 30px 20px;
		background: linear-gradient(135deg, #dbeafe, #ffedd5);
		font-family: Arial, sans-serif;
	}

	.wrapper {
		max-width: 1200px;
		margin: 0 auto;
	}

	.header-card {
		background: rgba(255, 255, 255, 0.94);
		border-radius: 28px;
		padding: 28px;
		box-shadow: 0 20px 50px rgba(37, 99, 235, 0.14);
		display: flex;
		align-items: center;
		gap: 18px;
		margin-bottom: 24px;
	}

	.heart {
		font-size: 54px;
		color: #f97316;
		animation: heartbeat 1.2s infinite;
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
		margin: 0;
		font-size: 34px;
		color: #1e293b;
	}

	.header-card p {
		margin: 6px 0 0;
		color: #64748b;
		font-size: 18px;
	}

	.grid {
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: 24px;
	}

	.card {
		background: rgba(255, 255, 255, 0.94);
		border-radius: 28px;
		padding: 30px;
		box-shadow: 0 20px 50px rgba(37, 99, 235, 0.14);
	}

	h2 {
		margin-top: 0;
		margin-bottom: 22px;
		font-size: 28px;
		color: #1e293b;
	}

	label {
		display: block;
		margin-bottom: 8px;
		font-weight: bold;
		color: #334155;
	}

	input,
	select,
	textarea {
		width: 100%;
		box-sizing: border-box;
		padding: 13px;
		border-radius: 16px;
		border: 1px solid #cbd5e1;
		margin-bottom: 18px;
		font-size: 16px;
		background: #f8fbff;
	}

	input:focus,
	select:focus,
	textarea:focus {
		outline: none;
		border-color: #2563eb;
		box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.18);
	}

	button {
		display: inline-block;
		width: 100%;
		text-align: center;
		box-sizing: border-box;
		padding: 14px;
		border: none;
		border-radius: 18px;
		background: linear-gradient(135deg, #2563eb, #f97316);
		color: white;
		font-weight: bold;
		font-size: 16px;
		cursor: pointer;
		text-decoration: none;
		margin-top: 6px;
	}

	button:hover {
		opacity: 0.9;
	}

	.status-box {
		background: #f8fbff;
		border-radius: 24px;
		padding: 28px;
		text-align: center;
		margin-bottom: 22px;
		border: 1px solid #dbeafe;
	}

	.status-circle {
		width: 84px;
		height: 84px;
		border-radius: 50%;
		margin: 0 auto 18px;
		box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
	}

	.status-circle.green {
		background: #22c55e;
	}

	.status-circle.yellow {
		background: #facc15;
	}

	.status-circle.red {
		background: #ef4444;
	}

	.status-circle.neutral {
		background: #cbd5e1;
	}

	.status-title {
		font-size: 22px;
		font-weight: bold;
		color: #1e293b;
		margin-bottom: 8px;
	}

	.status-message {
		color: #475569;
		margin-bottom: 10px;
		line-height: 1.5;
	}

	.status-action {
		color: #334155;
		font-weight: 600;
		line-height: 1.5;
	}

	.info-box {
		background: #fff7ed;
		border-radius: 20px;
		padding: 20px;
		margin-bottom: 18px;
		border: 1px solid #fed7aa;
	}

	.info-box h3 {
		margin-top: 0;
		color: #1e293b;
	}

	.info-box p {
		color: #475569;
		line-height: 1.5;
		margin-bottom: 0;
	}

	.logout-button {
		margin-top: 14px;
	}

	@media (max-width: 900px) {
		.grid {
			grid-template-columns: 1fr;
		}
	}
</style>