<script>
	import { onMount } from 'svelte';

	let latestMeasurement = null;

	let demoPatients = [
		{
			name: 'Anna Jensen',
			age: 74,
			status: 'Rød',
			latest: 'Vægt +2,4 kg, svær åndenød og hævede ben',
			action: 'Ring til patient samme dag'
		},
		{
			name: 'Peter Hansen',
			age: 69,
			status: 'Gul',
			latest: 'Let åndenød og høj puls',
			action: 'Opfølgning inden for 24-48 timer'
		},
		{
			name: 'Fatima Ali',
			age: 77,
			status: 'Grøn',
			latest: 'Stabile målinger',
			action: 'Ingen akut handling'
		}
	];

	onMount(() => {
		const role = localStorage.getItem('role');

		if (role !== 'clinician') {
			window.location.href = '/';
			return;
		}

		const stored = localStorage.getItem('latestMeasurement');
		if (stored) {
			latestMeasurement = JSON.parse(stored);
		}
	});

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
				<h1>Kliniker Dashboard</h1>
				<p>Overblik over hjertesvigtpatienter og deres seneste status</p>
			</div>
		</div>

		<div class="legend-card">
			<div class="legend-item">
				<div class="mini-circle green"></div>
				<span>Stabil</span>
			</div>
			<div class="legend-item">
				<div class="mini-circle yellow"></div>
				<span>Kræver opfølgning</span>
			</div>
			<div class="legend-item">
				<div class="mini-circle red"></div>
				<span>Høj prioritet</span>
			</div>
		</div>

		{#if latestMeasurement}
			<div class="card latest-card">
				<h2>Seneste indsendte måling</h2>

				<div class="latest-top">
					<div class="status-circle {getStatusClass(latestMeasurement.triageStatus)}"></div>

					<div>
						<p class="small-label">Patient</p>
						<p class="big-text">{latestMeasurement.patientName}</p>

						<p class="small-label">Tidspunkt</p>
						<p class="normal-text">{latestMeasurement.date}</p>
					</div>
				</div>

				<div class="info-grid">
					<div class="info-pill">
						<span>Vægt</span>
						<strong>{latestMeasurement.weight} kg</strong>
					</div>
					<div class="info-pill">
						<span>Puls</span>
						<strong>{latestMeasurement.pulse}</strong>
					</div>
					<div class="info-pill">
						<span>Blodtryk</span>
						<strong>{latestMeasurement.systolic}/{latestMeasurement.diastolic}</strong>
					</div>
					<div class="info-pill">
						<span>Åndenød</span>
						<strong>{latestMeasurement.breathlessness}</strong>
					</div>
				</div>

				<div class="message-box">
					<p><strong>Vurdering:</strong> {latestMeasurement.triageMessage}</p>
					<p><strong>Anbefalet handling:</strong> {latestMeasurement.action}</p>
					{#if latestMeasurement.comment}
						<p><strong>Patientkommentar:</strong> {latestMeasurement.comment}</p>
					{/if}
				</div>
			</div>
		{/if}

		<div class="card">
			<h2>Patientoversigt</h2>

			<div class="table-wrap">
				<table>
					<thead>
						<tr>
							<th>Status</th>
							<th>Patient</th>
							<th>Alder</th>
							<th>Seneste observation</th>
							<th>Handling</th>
						</tr>
					</thead>
					<tbody>
						{#each demoPatients as patient}
							<tr>
								<td>
									<div class="mini-circle {getStatusClass(patient.status)}"></div>
								</td>
								<td><strong>{patient.name}</strong></td>
								<td>{patient.age}</td>
								<td>{patient.latest}</td>
								<td>{patient.action}</td>
							</tr>
						{/each}

						{#if latestMeasurement}
							<tr>
								<td>
									<div class="mini-circle {getStatusClass(latestMeasurement.triageStatus)}"></div>
								</td>
								<td><strong>{latestMeasurement.patientName}</strong></td>
								<td>Demo</td>
								<td>
									Vægt {latestMeasurement.weight} kg, puls {latestMeasurement.pulse},
									åndenød: {latestMeasurement.breathlessness}
								</td>
								<td>{latestMeasurement.action}</td>
							</tr>
						{/if}
					</tbody>
				</table>
			</div>

			<button class="logout-button" onclick={logout}>Log ud</button>
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

	.header-card,
	.legend-card,
	.card {
		background: rgba(255, 255, 255, 0.94);
		border-radius: 28px;
		box-shadow: 0 20px 50px rgba(37, 99, 235, 0.14);
	}

	.header-card {
		padding: 28px;
		display: flex;
		align-items: center;
		gap: 18px;
		margin-bottom: 20px;
	}

	.legend-card {
		padding: 18px 24px;
		display: flex;
		gap: 30px;
		align-items: center;
		margin-bottom: 20px;
		flex-wrap: wrap;
	}

	.legend-item {
		display: flex;
		align-items: center;
		gap: 10px;
		color: #334155;
		font-weight: 600;
	}

	.card {
		padding: 30px;
		margin-bottom: 22px;
	}

	.latest-card {
		border-left: 8px solid #2563eb;
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

	h2 {
		margin-top: 0;
		margin-bottom: 20px;
		font-size: 28px;
		color: #1e293b;
	}

	.latest-top {
		display: flex;
		align-items: center;
		gap: 24px;
		margin-bottom: 22px;
		flex-wrap: wrap;
	}

	.status-circle {
		width: 90px;
		height: 90px;
		border-radius: 50%;
		box-shadow: 0 10px 20px rgba(0, 0, 0, 0.12);
	}

	.mini-circle {
		width: 20px;
		height: 20px;
		border-radius: 50%;
		box-shadow: 0 4px 10px rgba(0, 0, 0, 0.08);
	}

	.green {
		background: #22c55e;
	}

	.yellow {
		background: #facc15;
	}

	.red {
		background: #ef4444;
	}

	.neutral {
		background: #cbd5e1;
	}

	.small-label {
		color: #64748b;
		font-size: 14px;
		margin-bottom: 4px;
	}

	.big-text {
		font-size: 24px;
		font-weight: bold;
		color: #1e293b;
		margin: 0 0 10px 0;
	}

	.normal-text {
		margin: 0;
		color: #334155;
	}

	.info-grid {
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		gap: 14px;
		margin-bottom: 22px;
	}

	.info-pill {
		background: #f8fbff;
		border: 1px solid #dbeafe;
		border-radius: 18px;
		padding: 16px;
	}

	.info-pill span {
		display: block;
		font-size: 14px;
		color: #64748b;
		margin-bottom: 8px;
	}

	.info-pill strong {
		color: #1e293b;
		font-size: 18px;
	}

	.message-box {
		background: #fff7ed;
		border-radius: 20px;
		padding: 20px;
		border: 1px solid #fed7aa;
		color: #334155;
		line-height: 1.6;
	}

	.table-wrap {
		overflow-x: auto;
	}

	table {
		width: 100%;
		border-collapse: collapse;
	}

	th,
	td {
		text-align: left;
		padding: 16px;
		border-bottom: 1px solid #e2e8f0;
		vertical-align: middle;
	}

	th {
		color: #475569;
		font-size: 15px;
		background: #f8fbff;
	}

	td {
		color: #334155;
	}

	.logout-button {
		display: inline-block;
		margin-top: 24px;
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
		text-decoration: none;
		cursor: pointer;
	}

	.logout-button:hover {
		opacity: 0.9;
	}

	@media (max-width: 900px) {
		.info-grid {
			grid-template-columns: 1fr 1fr;
		}
	}

	@media (max-width: 600px) {
		.info-grid {
			grid-template-columns: 1fr;
		}
	}
</style>