<script>
	import { onMount } from 'svelte';
	
	let stats = {
		totalObservations: 0,
		normalCount: 0,
		anomalyCount: 0,
		anomalyPercentage: 0,
		lowVisibility: 0,
		highWind: 0,
		heavyPrecip: 0
	};

	onMount(async () => {
		// Load and parse the detected anomalies CSV
		try {
			const response = await fetch('/Results/detected_anomalies.csv');
			const text = await response.text();
			const lines = text.split('\n').filter(line => line.trim());
			
			stats.anomalyCount = lines.length - 1; // Subtract header
			
			// Parse for specific conditions
			lines.slice(1).forEach(line => {
				const values = line.split(',');
				const vsby = parseFloat(values[10]); // Visibility (column index 10)
				const sknt = parseFloat(values[6]); // Wind speed (column index 6)
				const p01i = parseFloat(values[7]); // Precipitation (column index 7)
				
				if (!isNaN(vsby) && vsby < 3) stats.lowVisibility++;
				if (!isNaN(sknt) && sknt > 20) stats.highWind++;
				if (!isNaN(p01i) && p01i > 0.1) stats.heavyPrecip++;
			});
			
			// Load full dataset to get total count
			const fullResponse = await fetch('/Results/metar_with_anomalies.csv');
			const fullText = await fullResponse.text();
			const fullLines = fullText.split('\n').filter(line => line.trim());
			stats.totalObservations = fullLines.length - 1;
			stats.normalCount = stats.totalObservations - stats.anomalyCount;
			stats.anomalyPercentage = ((stats.anomalyCount / stats.totalObservations) * 100).toFixed(2);
		} catch (error) {
			console.error('Error loading data:', error);
		}
	});

	const scrollToSection = (id) => {
		document.getElementById(id)?.scrollIntoView({ behavior: 'smooth' });
	};
</script>

<svelte:head>
	<title>Weather Anomaly Detection - Aviation Insight</title>
</svelte:head>

<div class="anomaly-container">
	<!-- Hero Section -->
	<section class="hero">
		<div class="hero-content">
			<h1> Aviation Weather Anomaly Detection</h1>
			<p class="subtitle">Identifying Unusual Weather Patterns for Enhanced Flight Safety</p>
			<div class="hero-badge">
				<span class="badge-icon">🤖</span>
				<span>Powered by Isolation Forest ML</span>
			</div>
		</div>
	</section>

	<!-- Quick Stats -->
	<section class="stats-grid">
		<div class="stat-card primary">
			<div class="stat-icon">📊</div>
			<div class="stat-value">{stats.totalObservations.toLocaleString()}</div>
			<div class="stat-label">Total Observations</div>
		</div>
		<div class="stat-card success">
			<div class="stat-icon">✅</div>
			<div class="stat-value">{stats.normalCount.toLocaleString()}</div>
			<div class="stat-label">Normal Conditions</div>
		</div>
		<div class="stat-card warning">
			<div class="stat-icon">⚠️</div>
			<div class="stat-value">{stats.anomalyCount.toLocaleString()}</div>
			<div class="stat-label">Anomalies Detected</div>
		</div>
		<div class="stat-card info">
			<div class="stat-icon">📈</div>
			<div class="stat-value">{stats.anomalyPercentage}%</div>
			<div class="stat-label">Anomaly Rate</div>
		</div>
	</section>

	<!-- Navigation Tabs -->
	<nav class="section-nav">
		<button on:click={() => scrollToSection('overview')}>Overview</button>
		<button on:click={() => scrollToSection('visualizations')}>Visualizations</button>
		<button on:click={() => scrollToSection('insights')}>Key Insights</button>
		<button on:click={() => scrollToSection('safety')}>Safety Metrics</button>
	</nav>


	<!-- Visualizations Section -->
	<section id="visualizations" class="content-section">
		<h2> Data Visualizations</h2>
		
		<!-- Anomaly Scores -->
		<div class="viz-block">
			<h3>Anomaly Score Distribution</h3>
			<p class="viz-description">This chart shows how anomaly scores are distributed between normal and anomalous weather conditions. Lower scores indicate greater deviation from normal patterns.</p>
			<div class="image-container">
				<img src="/Results/anomaly_scores.png" alt="Anomaly Score Distribution" />
			</div>
		</div>

		<!-- PCA Visualization -->
		<div class="viz-block">
			<h3>Principal Component Analysis (PCA) Visualization</h3>
			<p class="viz-description">2D projection of high-dimensional weather data showing clear separation between normal conditions (green) and detected anomalies (red crosses).</p>
			<div class="image-container">
				<img src="/Results/anomaly_pca_visualization.png" alt="PCA Visualization" />
			</div>
		</div>

		<!-- Feature Comparison -->
		<div class="viz-block">
			<h3>Feature Comparison: Normal vs Anomalous Weather</h3>
			<p class="viz-description">Box plots comparing all weather parameters between normal and anomalous conditions, revealing distinct patterns in extreme weather.</p>
			<div class="image-container">
				<img src="/Results/feature_comparison.png" alt="Feature Comparison" />
			</div>
		</div>

		<!-- Temperature vs Visibility -->
		<div class="viz-block">
			<h3>Temperature vs Visibility Analysis</h3>
			<p class="viz-description">Critical aviation parameters showing how anomalies often occur at extreme temperatures or reduced visibility conditions.</p>
			<div class="image-container">
				<img src="/Results/temp_vs_visibility.png" alt="Temperature vs Visibility" />
			</div>
		</div>

		<!-- Wind vs Precipitation -->
		<div class="viz-block">
			<h3>Wind Speed vs Precipitation</h3>
			<p class="viz-description">Relationship between wind conditions and precipitation, highlighting severe weather events marked as anomalies.</p>
			<div class="image-container">
				<img src="/Results/wind_vs_precipitation.png" alt="Wind vs Precipitation" />
			</div>
		</div>

		<!-- Correlation Heatmap -->
		<div class="viz-block">
			<h3>Feature Correlation Heatmap</h3>
			<p class="viz-description">Understanding relationships between weather parameters. Strong correlations (red) and negative correlations (blue) help identify interconnected weather phenomena.</p>
			<div class="image-container">
				<img src="/Results/correlation_heatmap.png" alt="Correlation Heatmap" />
			</div>
		</div>

		<!-- Feature Distributions -->
		<div class="viz-block">
			<h3>Individual Feature Distributions</h3>
			<p class="viz-description">Histograms showing the distribution of each weather parameter across all observations in the dataset.</p>
			<div class="image-container">
				<img src="/Results/feature_distributions.png" alt="Feature Distributions" />
			</div>
		</div>
	</section>

	<!-- Key Insights Section -->
	<section id="insights" class="content-section">
		<h2>💡 Key Insights & Findings</h2>
		<div class="insights-grid">
			<div class="insight-card">
				<div class="insight-icon">🎯</div>
				<h3>Detection Accuracy</h3>
				<p>The Isolation Forest model successfully identified {stats.anomalyPercentage}% of observations as anomalous, aligning with expected rare weather event frequencies in aviation meteorology.</p>
			</div>
			<div class="insight-card">
				<div class="insight-icon">🌡️</div>
				<h3>Temperature Extremes</h3>
				<p>Anomalies frequently occur at temperature extremes, both hot and cold, which can affect aircraft performance and require special operational procedures.</p>
			</div>
			<div class="insight-card">
				<div class="insight-icon">👁️</div>
				<h3>Visibility Concerns</h3>
				<p>Low visibility conditions (&lt; 3 miles) account for a significant portion of detected anomalies, critical for instrument flight rules (IFR) operations.</p>
			</div>
			<div class="insight-card">
				<div class="insight-icon">💨</div>
				<h3>Wind Patterns</h3>
				<p>High wind speeds and variable wind directions correlate strongly with anomalous conditions, impacting takeoff/landing safety margins.</p>
			</div>
			<div class="insight-card">
				<div class="insight-icon">🔗</div>
				<h3>Multi-Parameter Events</h3>
				<p>Most severe anomalies involve multiple concurrent extreme conditions rather than single-parameter deviations.</p>
			</div>
			<div class="insight-card">
				<div class="insight-icon">📊</div>
				<h3>PCA Separation</h3>
				<p>Clear clustering in PCA space demonstrates that anomalous weather forms distinct patterns, validating the model's effectiveness.</p>
			</div>
		</div>
	</section>

	<!-- Safety Metrics Section -->
	<section id="safety" class="content-section">
		<h2>⚠️ Aviation Safety Metrics</h2>
		<div class="safety-grid">
			<div class="safety-card red">
				<div class="safety-header">
					<span class="safety-icon">🌫️</span>
					<h3>Low Visibility Events</h3>
				</div>
				<div class="safety-value">{stats.lowVisibility}</div>
				<p>Anomalies with visibility &lt; 3 miles</p>
				<div class="safety-impact">
					<strong>Impact:</strong> Requires IFR conditions, potential delays, enhanced pilot workload
				</div>
			</div>
			<div class="safety-card orange">
				<div class="safety-header">
					<span class="safety-icon">💨</span>
					<h3>High Wind Events</h3>
				</div>
				<div class="safety-value">{stats.highWind}</div>
				<p>Anomalies with wind speed &gt; 20 knots</p>
				<div class="safety-impact">
					<strong>Impact:</strong> Crosswind landing challenges, turbulence risk, go-around potential
				</div>
			</div>
			<div class="safety-card purple">
				<div class="safety-header">
					<span class="safety-icon">💧</span>
					<h3>Heavy Precipitation</h3>
				</div>
				<div class="safety-value">{stats.heavyPrecip}</div>
				<p>Anomalies with precipitation &gt; 0.1 inches</p>
				<div class="safety-impact">
					<strong>Impact:</strong> Reduced braking action, visibility degradation, icing risk
				</div>
			</div>
		</div>

		<div class="applications">
			<h3>Practical Applications</h3>
			<div class="application-list">
				<div class="application-item">
					<span class="app-number">1</span>
					<div>
						<h4>Pre-flight Planning</h4>
						<p>Identify potentially hazardous weather conditions before departure, enabling route modifications and fuel planning adjustments.</p>
					</div>
				</div>
				<div class="application-item">
					<span class="app-number">2</span>
					<div>
						<h4>Real-time Alerts</h4>
						<p>Flag unusual weather patterns for immediate pilot and dispatcher awareness during flight operations.</p>
					</div>
				</div>
				<div class="application-item">
					<span class="app-number">3</span>
					<div>
						<h4>Route Optimization</h4>
						<p>Dynamically avoid areas with anomalous weather to maintain schedule reliability and passenger comfort.</p>
					</div>
				</div>
				<div class="application-item">
					<span class="app-number">4</span>
					<div>
						<h4>Maintenance Scheduling</h4>
						<p>Track extreme conditions affecting aircraft systems to optimize preventive maintenance intervals.</p>
					</div>
				</div>
			</div>
		</div>
	</section>

	<!-- Technical Details -->
	<section class="content-section technical">
		<h2>🔧 Technical Implementation</h2>
		<div class="tech-grid">
			<div class="tech-card">
				<h4>Algorithm</h4>
				<p><strong>Isolation Forest</strong></p>
				<ul>
					<li>Unsupervised learning</li>
					<li>100 decision trees</li>
					<li>5% contamination rate</li>
				</ul>
			</div>
			<div class="tech-card">
				<h4>Data Processing</h4>
				<p><strong>Feature Engineering</strong></p>
				<ul>
					<li>Standard scaling</li>
					<li>Median imputation</li>
					<li>8 weather parameters</li>
				</ul>
			</div>
			<div class="tech-card">
				<h4>Visualization</h4>
				<p><strong>Dimensionality Reduction</strong></p>
				<ul>
					<li>PCA (2 components)</li>
					<li>~60% variance explained</li>
					<li>Clear cluster separation</li>
				</ul>
			</div>
			<div class="tech-card">
				<h4>Output</h4>
				<p><strong>Deliverables</strong></p>
				<ul>
					<li>Anomaly scores</li>
					<li>Binary classifications</li>
					<li>CSV exports</li>
				</ul>
			</div>
		</div>
	</section>

	<!-- Footer CTA -->
	<section class="cta-section">
		<h2>Ready to Explore More?</h2>
		<p>Dive deeper into our aviation weather analysis tools and predictive models</p>
		<div class="cta-buttons">
			<a href="/api/metar" class="btn-primary">METAR Analysis</a>
			<a href="/vis-pred" class="btn-secondary">Visibility Prediction</a>
			<a href="/weather-summaries" class="btn-secondary">Weather Summaries</a>
		</div>
	</section>
</div>

<style>
	:global(body) {
		margin: 0;
		font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
		background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
	}

	.anomaly-container {
		min-height: 100vh;
		padding-bottom: 4rem;
	}

	/* Hero Section */
	.hero {
		background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
		color: white;
		padding: 5rem 2rem;
		text-align: center;
		position: relative;
		overflow: hidden;
	}

	.hero::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: url("data:image/svg+xml,%3Csvg width='60' height='60' viewBox='0 0 60 60' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%23ffffff' fill-opacity='0.05'%3E%3Cpath d='M36 34v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4h-4v2h4v4h2V6h4V4h-4zM6 34v-4H4v4H0v2h4v4h2v-4h4v-2H6zM6 4V0H4v4H0v2h4v4h2V6h4V4H6z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
		opacity: 0.3;
	}

	.hero-content {
		position: relative;
		z-index: 1;
	}

	.hero h1 {
		font-size: 3.5rem;
		margin: 0 0 1rem 0;
		font-weight: 700;
		text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
	}

	.subtitle {
		font-size: 1.3rem;
		margin: 0 0 2rem 0;
		opacity: 0.95;
		font-weight: 300;
	}

	.hero-badge {
		display: inline-flex;
		align-items: center;
		gap: 0.5rem;
		background: rgba(255,255,255,0.2);
		backdrop-filter: blur(10px);
		padding: 0.75rem 1.5rem;
		border-radius: 50px;
		font-weight: 500;
		border: 2px solid rgba(255,255,255,0.3);
	}

	.badge-icon {
		font-size: 1.5rem;
	}

	/* Stats Grid */
	.stats-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
		gap: 1.5rem;
		max-width: 1200px;
		margin: -3rem auto 3rem;
		padding: 0 2rem;
		position: relative;
		z-index: 2;
	}

	.stat-card {
		background: white;
		padding: 2rem;
		border-radius: 16px;
		box-shadow: 0 10px 30px rgba(0,0,0,0.15);
		text-align: center;
		transition: transform 0.3s ease, box-shadow 0.3s ease;
	}

	.stat-card:hover {
		transform: translateY(-5px);
		box-shadow: 0 15px 40px rgba(0,0,0,0.2);
	}

	.stat-icon {
		font-size: 3rem;
		margin-bottom: 1rem;
	}

	.stat-value {
		font-size: 2.5rem;
		font-weight: 700;
		margin-bottom: 0.5rem;
	}

	.stat-label {
		color: #666;
		font-size: 0.95rem;
		text-transform: uppercase;
		letter-spacing: 1px;
		font-weight: 600;
	}

	.stat-card.primary { border-top: 4px solid #667eea; }
	.stat-card.primary .stat-value { color: #667eea; }
	
	.stat-card.success { border-top: 4px solid #10b981; }
	.stat-card.success .stat-value { color: #10b981; }
	
	.stat-card.warning { border-top: 4px solid #f59e0b; }
	.stat-card.warning .stat-value { color: #f59e0b; }
	
	.stat-card.info { border-top: 4px solid #3b82f6; }
	.stat-card.info .stat-value { color: #3b82f6; }

	/* Navigation */
	.section-nav {
		display: flex;
		justify-content: center;
		gap: 1rem;
		flex-wrap: wrap;
		padding: 2rem;
		background: rgba(255,255,255,0.1);
		backdrop-filter: blur(10px);
		position: sticky;
		top: 0;
		z-index: 100;
	}

	.section-nav button {
		background: white;
		border: none;
		padding: 0.75rem 1.5rem;
		border-radius: 50px;
		cursor: pointer;
		font-weight: 600;
		transition: all 0.3s ease;
		box-shadow: 0 4px 6px rgba(0,0,0,0.1);
	}

	.section-nav button:hover {
		transform: translateY(-2px);
		box-shadow: 0 6px 12px rgba(0,0,0,0.15);
		background: #667eea;
		color: white;
	}

	/* Content Sections */
	.content-section {
		max-width: 1200px;
		margin: 3rem auto;
		padding: 3rem 2rem;
		background: white;
		border-radius: 20px;
		box-shadow: 0 10px 40px rgba(0,0,0,0.1);
	}

	.content-section h2 {
		font-size: 2.5rem;
		margin-bottom: 2rem;
		color: #1e3c72;
		text-align: center;
	}

	/* Overview Grid */
	.overview-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
		gap: 1.5rem;
		margin-top: 2rem;
	}

	.overview-card {
		background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
		padding: 2rem;
		border-radius: 12px;
		border-left: 4px solid #667eea;
	}

	.overview-card h3 {
		color: #1e3c72;
		margin-top: 0;
		margin-bottom: 1rem;
		font-size: 1.3rem;
	}

	.overview-card ul {
		list-style: none;
		padding: 0;
		margin: 0.5rem 0;
	}

	.overview-card li {
		padding: 0.3rem 0;
		color: #374151;
	}

	/* Visualizations */
	.viz-block {
		margin: 3rem 0;
		padding: 2rem;
		background: #f9fafb;
		border-radius: 12px;
	}

	.viz-block h3 {
		color: #1e3c72;
		font-size: 1.8rem;
		margin-top: 0;
		margin-bottom: 1rem;
	}

	.viz-description {
		color: #6b7280;
		font-size: 1.05rem;
		line-height: 1.6;
		margin-bottom: 1.5rem;
	}

	.image-container {
		background: white;
		padding: 1rem;
		border-radius: 8px;
		box-shadow: 0 4px 6px rgba(0,0,0,0.1);
		overflow: hidden;
	}

	.image-container img {
		width: 100%;
		height: auto;
		display: block;
		border-radius: 4px;
	}

	/* Insights Grid */
	.insights-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
		gap: 1.5rem;
		margin-top: 2rem;
	}

	.insight-card {
		background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
		color: white;
		padding: 2rem;
		border-radius: 12px;
		box-shadow: 0 8px 20px rgba(0,0,0,0.15);
		transition: transform 0.3s ease;
	}

	.insight-card:hover {
		transform: translateY(-5px);
	}

	.insight-icon {
		font-size: 3rem;
		margin-bottom: 1rem;
	}

	.insight-card h3 {
		margin: 0 0 1rem 0;
		font-size: 1.4rem;
	}

	.insight-card p {
		margin: 0;
		opacity: 0.95;
		line-height: 1.6;
	}

	/* Safety Metrics */
	.safety-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
		gap: 2rem;
		margin: 2rem 0;
	}

	.safety-card {
		padding: 2rem;
		border-radius: 12px;
		color: white;
		box-shadow: 0 8px 20px rgba(0,0,0,0.2);
	}

	.safety-card.red {
		background: linear-gradient(135deg, #dc2626 0%, #991b1b 100%);
	}

	.safety-card.orange {
		background: linear-gradient(135deg, #f59e0b 0%, #d97706 100%);
	}

	.safety-card.purple {
		background: linear-gradient(135deg, #8b5cf6 0%, #6d28d9 100%);
	}

	.safety-header {
		display: flex;
		align-items: center;
		gap: 1rem;
		margin-bottom: 1rem;
	}

	.safety-icon {
		font-size: 2.5rem;
	}

	.safety-header h3 {
		margin: 0;
		font-size: 1.3rem;
	}

	.safety-value {
		font-size: 3.5rem;
		font-weight: 700;
		margin: 1rem 0;
	}

	.safety-impact {
		margin-top: 1rem;
		padding-top: 1rem;
		border-top: 2px solid rgba(255,255,255,0.3);
		font-size: 0.95rem;
		line-height: 1.5;
	}

	/* Applications */
	.applications {
		margin-top: 3rem;
		padding: 2rem;
		background: #f0f9ff;
		border-radius: 12px;
	}

	.applications h3 {
		color: #1e3c72;
		font-size: 1.8rem;
		margin-top: 0;
		margin-bottom: 1.5rem;
	}

	.application-list {
		display: grid;
		gap: 1.5rem;
	}

	.application-item {
		display: flex;
		gap: 1.5rem;
		align-items: start;
		background: white;
		padding: 1.5rem;
		border-radius: 8px;
		box-shadow: 0 2px 4px rgba(0,0,0,0.1);
	}

	.app-number {
		flex-shrink: 0;
		width: 40px;
		height: 40px;
		background: #667eea;
		color: white;
		border-radius: 50%;
		display: flex;
		align-items: center;
		justify-content: center;
		font-weight: 700;
		font-size: 1.2rem;
	}

	.application-item h4 {
		margin: 0 0 0.5rem 0;
		color: #1e3c72;
		font-size: 1.2rem;
	}

	.application-item p {
		margin: 0;
		color: #6b7280;
		line-height: 1.6;
	}

	/* Technical Section */
	.technical {
		background: #1f2937;
		color: white;
	}

	.technical h2 {
		color: white;
	}

	.tech-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
		gap: 1.5rem;
	}

	.tech-card {
		background: rgba(255,255,255,0.1);
		padding: 1.5rem;
		border-radius: 8px;
		border: 1px solid rgba(255,255,255,0.2);
	}

	.tech-card h4 {
		color: #60a5fa;
		margin-top: 0;
		margin-bottom: 0.5rem;
		font-size: 1.1rem;
	}

	.tech-card p {
		margin: 0.5rem 0;
		font-weight: 600;
	}

	.tech-card ul {
		list-style: none;
		padding: 0;
		margin: 1rem 0 0 0;
	}

	.tech-card li {
		padding: 0.3rem 0;
		color: rgba(255,255,255,0.9);
		font-size: 0.95rem;
	}

	.tech-card li::before {
		content: '▸ ';
		color: #60a5fa;
	}

	/* CTA Section */
	.cta-section {
		max-width: 1200px;
		margin: 3rem auto;
		padding: 4rem 2rem;
		background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
		border-radius: 20px;
		text-align: center;
		color: white;
		box-shadow: 0 10px 40px rgba(0,0,0,0.2);
	}

	.cta-section h2 {
		font-size: 2.5rem;
		margin: 0 0 1rem 0;
		color: white;
	}

	.cta-section p {
		font-size: 1.2rem;
		margin: 0 0 2rem 0;
		opacity: 0.9;
	}

	.cta-buttons {
		display: flex;
		justify-content: center;
		gap: 1rem;
		flex-wrap: wrap;
	}

	.btn-primary, .btn-secondary {
		padding: 1rem 2rem;
		border-radius: 50px;
		text-decoration: none;
		font-weight: 600;
		transition: all 0.3s ease;
		box-shadow: 0 4px 6px rgba(0,0,0,0.1);
	}

	.btn-primary {
		background: white;
		color: #1e3c72;
	}

	.btn-primary:hover {
		transform: translateY(-2px);
		box-shadow: 0 6px 12px rgba(0,0,0,0.2);
	}

	.btn-secondary {
		background: rgba(255,255,255,0.2);
		color: white;
		border: 2px solid rgba(255,255,255,0.5);
	}

	.btn-secondary:hover {
		background: rgba(255,255,255,0.3);
		transform: translateY(-2px);
		box-shadow: 0 6px 12px rgba(0,0,0,0.2);
	}

	/* Responsive Design */
	@media (max-width: 768px) {
		.hero h1 {
			font-size: 2.5rem;
		}

		.subtitle {
			font-size: 1.1rem;
		}

		.stats-grid {
			grid-template-columns: 1fr;
		}

		.content-section {
			padding: 2rem 1rem;
			margin: 2rem 1rem;
		}

		.content-section h2 {
			font-size: 2rem;
		}

		.section-nav {
			padding: 1rem;
		}

		.section-nav button {
			font-size: 0.9rem;
			padding: 0.6rem 1.2rem;
		}

		.cta-buttons {
			flex-direction: column;
			align-items: stretch;
		}
	}
</style>
