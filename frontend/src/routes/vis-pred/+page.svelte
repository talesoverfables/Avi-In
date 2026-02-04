<script>
	// Model performance metrics
	const models = [
		{
			name: 'Linear Regression',
			rmse: 1.8747,
			mae: 1.2826,
			r2: 0.3482,
			color: '#3b82f6',
			description: 'A simple baseline model that assumes linear relationships between features and visibility.',
			performance: 'Poor fit - explains only ~35% of variance with high prediction errors.'
		},
		{
			name: 'Random Forest',
			rmse: 0.9760,
			mae: 0.3749,
			r2: 0.8233,
			color: '#10b981',
			description: 'An ensemble model that builds multiple decision trees and averages their predictions.',
			performance: 'Excellent fit - best performing model with lowest errors and highest R² score.'
		},
		{
			name: 'Gradient Boosting',
			rmse: 0.9968,
			mae: 0.5046,
			r2: 0.8157,
			color: '#f59e0b',
			description: 'An advanced ensemble method that builds trees sequentially to correct previous errors.',
			performance: 'Very good fit - comparable to Random Forest with strong predictive power.'
		}
	];

	const features = [
		{ name: 'tmpf', fullName: 'Temperature (°F)', description: 'Air temperature in Fahrenheit' },
		{ name: 'dwpf', fullName: 'Dew Point (°F)', description: 'Dew point temperature - indicates moisture in air' },
		{ name: 'relh', fullName: 'Relative Humidity (%)', description: 'Percentage of moisture in the air' },
		{ name: 'drct', fullName: 'Wind Direction (°)', description: 'Direction from which wind is blowing' },
		{ name: 'sknt', fullName: 'Wind Speed (knots)', description: 'Wind speed in nautical miles per hour' },
		{ name: 'p01i', fullName: 'Precipitation (in)', description: 'One-hour precipitation amount' },
		{ name: 'alti', fullName: 'Altimeter (inHg)', description: 'Barometric pressure setting' },
		{ name: 'feel', fullName: 'Feels Like (°F)', description: 'Apparent temperature considering wind chill' }
	];

	const metrics = [
		{
			name: 'RMSE',
			fullName: 'Root Mean Squared Error',
			description: 'Measures how far predictions are from actual values on average. Lower is better.',
			interpretation: 'Penalizes large errors more heavily than small errors.'
		},
		{
			name: 'MAE',
			fullName: 'Mean Absolute Error',
			description: 'Average absolute difference between predicted and actual values. Easy to interpret.',
			interpretation: 'In miles - shows typical prediction error.'
		},
		{
			name: 'R² Score',
			fullName: 'Coefficient of Determination',
			description: 'Fraction of variance explained by the model (0-1 scale).',
			interpretation: '1 = perfect prediction, 0 = baseline mean, negative = worse than baseline.'
		}
	];
</script>

<div class="container">
	<!-- Header Section -->
	<header class="header">
		<div class="header-content">
			<h1 class="title"> Visibility Prediction Model</h1>
			<p class="subtitle">Machine Learning for Aviation Weather Forecasting</p>
			<div class="header-badge">
				<span class="badge">METAR Data Analysis</span>
				<span class="badge">Regression Models</span>
				<span class="badge">Scikit-Learn</span>
			</div>
		</div>
	</header>

	<!-- Overview Section -->
	<section class="section">
		<h2 class="section-title">Overview</h2>
		<div class="overview-grid">
			<div class="overview-card">
				<div class="icon">🎯</div>
				<h3>Objective</h3>
				<p>Predict visibility in statute miles using METAR weather observation data to assist pilots in flight planning and safety decisions.</p>
			</div>
			<div class="overview-card">
				<div class="icon">🔬</div>
				<h3>Methodology</h3>
				<p>Trained and compared three regression models: Linear Regression, Random Forest, and Gradient Boosting using 8 key weather features.</p>
			</div>
			<div class="overview-card">
				<div class="icon">🏆</div>
				<h3>Best Model</h3>
				<p>Random Forest achieved 82.33% variance explanation with RMSE of 0.976 miles and MAE of 0.375 miles.</p>
			</div>
		</div>
	</section>

	<!-- Data Distribution Section -->
	<section class="section">
		<h2 class="section-title"> Visibility Data Distribution</h2>
		<p class="section-description">
			Understanding the distribution of visibility values in the METAR dataset is crucial for model development. 
			The histogram shows the frequency of different visibility values, while the boxplot identifies outliers and quartiles.
		</p>
		<div class="image-container">
			<img src="/visib/visibility_distribution.png" alt="Visibility Distribution" class="chart-image" />
		</div>
		<div class="insight-box">
			<strong>Key Insight:</strong> Most observations show high visibility (>10 miles), with fewer instances of reduced visibility. 
			This imbalance affects model training and emphasizes the importance of capturing low-visibility conditions accurately.
		</div>
	</section>

	<!-- Features Section -->
	<section class="section">
		<h2 class="section-title"> Input Features</h2>
		<p class="section-description">
			The model uses 8 meteorological features from METAR reports to predict visibility:
		</p>
		<div class="features-grid">
			{#each features as feature}
				<div class="feature-card">
					<div class="feature-header">
						<span class="feature-code">{feature.name}</span>
					</div>
					<h4 class="feature-name">{feature.fullName}</h4>
					<p class="feature-desc">{feature.description}</p>
				</div>
			{/each}
		</div>
	</section>

	<!-- Correlation Analysis Section -->
	<section class="section">
		<h2 class="section-title"> Feature Correlation Analysis</h2>
		<p class="section-description">
			The correlation heatmap reveals relationships between weather features and visibility. 
			Stronger correlations (darker colors) indicate features that have more influence on visibility predictions.
		</p>
		<div class="image-container">
			<img src="/visib/correlation_heatmap.png" alt="Correlation Heatmap" class="chart-image" />
		</div>
		<div class="insight-box">
			<strong>Key Findings:</strong> Relative humidity (relh), dew point (dwpf), and precipitation (p01i) show strong negative 
			correlations with visibility, meaning higher moisture content typically reduces visibility. Temperature-related features 
			show moderate positive correlations.
		</div>
	</section>

	<!-- Model Comparison Section -->
	<section class="section">
		<h2 class="section-title"> Model Performance Comparison</h2>
		<p class="section-description">
			Three regression models were trained and evaluated. Each model uses different algorithms to learn 
			patterns in the weather data and predict visibility values.
		</p>
		
		<div class="models-grid">
			{#each models as model}
				<div class="model-card" style="border-top: 4px solid {model.color}">
					<h3 class="model-name">{model.name}</h3>
					<p class="model-description">{model.description}</p>
					
					<div class="metrics-grid">
						<div class="metric">
							<span class="metric-label">RMSE</span>
							<span class="metric-value" style="color: {model.color}">{model.rmse.toFixed(4)}</span>
						</div>
						<div class="metric">
							<span class="metric-label">MAE</span>
							<span class="metric-value" style="color: {model.color}">{model.mae.toFixed(4)}</span>
						</div>
						<div class="metric">
							<span class="metric-label">R² Score</span>
							<span class="metric-value" style="color: {model.color}">{model.r2.toFixed(4)}</span>
						</div>
					</div>
					
					<div class="performance-badge" style="background: {model.color}20; color: {model.color}">
						{model.performance}
					</div>
				</div>
			{/each}
		</div>
	</section>

	<!-- Metrics Explanation Section -->
	<section class="section">
		<h2 class="section-title"> Understanding the Metrics</h2>
		<div class="metrics-explanation">
			{#each metrics as metric}
				<div class="metric-explain-card">
					<div class="metric-explain-header">
						<span class="metric-badge">{metric.name}</span>
						<h4>{metric.fullName}</h4>
					</div>
					<p class="metric-explain-desc">{metric.description}</p>
					<p class="metric-interpret"><strong>Interpretation:</strong> {metric.interpretation}</p>
				</div>
			{/each}
		</div>
	</section>

	<!-- Model Performance Visualization -->
	<section class="section">
		<h2 class="section-title"> Predicted vs Actual Values</h2>
		<p class="section-description">
			Scatter plots comparing predicted visibility against actual observed visibility for each model. 
			Points closer to the red diagonal line indicate more accurate predictions.
		</p>
		<div class="image-container">
			<img src="/visib/model_performance.png" alt="Model Performance Comparison" class="chart-image" />
		</div>
		<div class="insight-box">
			<strong>Analysis:</strong> Random Forest and Gradient Boosting show tight clustering around the diagonal line, 
			indicating accurate predictions. Linear Regression shows more scatter, especially at higher visibility values, 
			confirming its poor fit for this non-linear problem.
		</div>
	</section>

	<!-- Feature Importance Section -->
	<section class="section">
		<h2 class="section-title"> Feature Importance</h2>
		<p class="section-description">
			The Random Forest model reveals which weather features contribute most to visibility predictions. 
			Features with higher importance scores have greater influence on the model's decisions.
		</p>
		<div class="image-container">
			<img src="/visib/feature_importance.png" alt="Feature Importance" class="chart-image" />
		</div>
		<div class="insight-box">
			<strong>Top Contributors:</strong> Relative humidity, dew point, and precipitation are the most important 
			features, aligning with meteorological understanding that moisture content is the primary driver of visibility reduction.
		</div>
	</section>

	<!-- Residual Analysis Section -->
	<section class="section">
		<h2 class="section-title"> Residual Analysis</h2>
		<p class="section-description">
			Residual plots help diagnose model performance by showing the differences between predicted and actual values. 
			Ideally, residuals should be randomly distributed around zero with no patterns.
		</p>
		<div class="image-container">
			<img src="/visib/residual_analysis.png" alt="Residual Analysis" class="chart-image" />
		</div>
		<div class="insight-box">
			<strong>Model Diagnostics:</strong> The residual plot shows relatively random scatter around zero, indicating 
			good model fit. The histogram shows a roughly normal distribution of errors, validating the model's reliability. 
			Some outliers exist but are minimal.
		</div>
	</section>

	<!-- Results Summary Section -->
	<section class="section results-section">
		<h2 class="section-title">🎯 Results Summary</h2>
		<div class="results-grid">
			<div class="result-card winner">
				<div class="result-icon">🏆</div>
				<h3>Winner: Random Forest</h3>
				<div class="result-stats">
					<div class="stat">
						<span class="stat-value">82.33%</span>
						<span class="stat-label">Variance Explained</span>
					</div>
					<div class="stat">
						<span class="stat-value">0.976</span>
						<span class="stat-label">RMSE (miles)</span>
					</div>
					<div class="stat">
						<span class="stat-value">0.375</span>
						<span class="stat-label">MAE (miles)</span>
					</div>
				</div>
				<p class="result-conclusion">
					Random Forest demonstrates excellent predictive capability, capturing complex non-linear 
					relationships between weather features and visibility with minimal error.
				</p>
			</div>

			<div class="result-card">
				<div class="result-icon">📌</div>
				<h3>Key Takeaways</h3>
				<ul class="takeaways-list">
					<li><strong>Non-linearity matters:</strong> Linear Regression underperforms significantly, proving visibility prediction requires capturing complex weather interactions.</li>
					<li><strong>Moisture is key:</strong> Relative humidity, dew point, and precipitation are the strongest predictors of visibility.</li>
					<li><strong>Production-ready:</strong> Random Forest achieves practical accuracy for aviation applications with average error under 0.4 miles.</li>
					<li><strong>Robust performance:</strong> Both ensemble models (RF & GB) show consistent results with ~82% variance explanation.</li>
				</ul>
			</div>
		</div>
	</section>

	<!-- Applications Section -->
	<section class="section">
		<h2 class="section-title">🚀 Real-World Applications</h2>
		<div class="applications-grid">
			<div class="app-card">
				<div class="app-icon">✈️</div>
				<h4>Flight Planning</h4>
				<p>Pilots can use visibility predictions to plan routes and alternate airports based on forecasted weather conditions.</p>
			</div>
			<div class="app-card">
				<div class="app-icon">🗼</div>
				<h4>Air Traffic Control</h4>
				<p>ATC can anticipate visibility changes to adjust spacing, approach procedures, and runway operations.</p>
			</div>
			<div class="app-card">
				<div class="app-icon">⚠️</div>
				<h4>Safety Alerts</h4>
				<p>Automated systems can trigger warnings when predicted visibility falls below safe minimums for specific operations.</p>
			</div>
			<div class="app-card">
				<div class="app-icon">📊</div>
				<h4>Weather Briefings</h4>
				<p>Enhanced weather briefings with ML-based visibility forecasts complement traditional meteorological reports.</p>
			</div>
		</div>
	</section>

	<!-- Technical Details Section -->
	<section class="section technical-section">
		<h2 class="section-title">⚙️ Technical Implementation</h2>
		<div class="tech-details">
			<div class="tech-box">
				<h4>🗄️ Dataset</h4>
				<ul>
					<li>Source: METAR observations from metar.csv</li>
					<li>Data cleaning: Removed rows with missing values</li>
					<li>Split: 80% training, 20% testing</li>
					<li>Preprocessing: StandardScaler for feature normalization</li>
				</ul>
			</div>
			<div class="tech-box">
				<h4>🛠️ Tools & Libraries</h4>
				<ul>
					<li>Python 3.x with pandas, numpy</li>
					<li>Scikit-learn for model training & evaluation</li>
					<li>Matplotlib & Seaborn for visualization</li>
					<li>Random Forest: 100 estimators, random_state=42</li>
				</ul>
			</div>
			<div class="tech-box">
				<h4>📈 Model Training</h4>
				<ul>
					<li>Linear Regression: Baseline model</li>
					<li>Random Forest: n_estimators=100, n_jobs=-1</li>
					<li>Gradient Boosting: n_estimators=100</li>
					<li>Evaluation: MSE, RMSE, MAE, R² metrics</li>
				</ul>
			</div>
		</div>
	</section>
</div>

<style>
	:global(body) {
		margin: 0;
		padding: 0;
		background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
		font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
		color: #e2e8f0;
	}

	.container {
		max-width: 1400px;
		margin: 0 auto;
		padding: 2rem;
	}

	/* Header Styles */
	.header {
		background: linear-gradient(135deg, #4f46e5 0%, #7c3aed 100%);
		border-radius: 20px;
		padding: 3rem 2rem;
		margin-bottom: 3rem;
		box-shadow: 0 20px 60px rgba(79, 70, 229, 0.3);
		text-align: center;
	}

	.title {
		font-size: 3rem;
		font-weight: 800;
		margin: 0 0 0.5rem 0;
		color: white;
		text-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
	}

	.subtitle {
		font-size: 1.3rem;
		color: #e0e7ff;
		margin: 0 0 1.5rem 0;
		font-weight: 300;
	}

	.header-badge {
		display: flex;
		gap: 1rem;
		justify-content: center;
		flex-wrap: wrap;
	}

	.badge {
		background: rgba(255, 255, 255, 0.2);
		backdrop-filter: blur(10px);
		padding: 0.5rem 1.5rem;
		border-radius: 50px;
		font-size: 0.9rem;
		font-weight: 500;
		color: white;
		border: 1px solid rgba(255, 255, 255, 0.3);
	}

	/* Section Styles */
	.section {
		background: rgba(30, 41, 59, 0.5);
		backdrop-filter: blur(10px);
		border-radius: 16px;
		padding: 2.5rem;
		margin-bottom: 2rem;
		border: 1px solid rgba(148, 163, 184, 0.1);
	}

	.section-title {
		font-size: 2rem;
		font-weight: 700;
		margin: 0 0 1rem 0;
		color: #f1f5f9;
		border-left: 4px solid #4f46e5;
		padding-left: 1rem;
	}

	.section-description {
		font-size: 1.1rem;
		line-height: 1.7;
		color: #cbd5e1;
		margin-bottom: 2rem;
	}

	/* Overview Grid */
	.overview-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
		gap: 1.5rem;
		margin-top: 2rem;
	}

	.overview-card {
		background: linear-gradient(135deg, #1e293b 0%, #334155 100%);
		padding: 2rem;
		border-radius: 12px;
		border: 1px solid rgba(148, 163, 184, 0.2);
		transition: transform 0.3s ease, box-shadow 0.3s ease;
	}

	.overview-card:hover {
		transform: translateY(-5px);
		box-shadow: 0 10px 30px rgba(79, 70, 229, 0.2);
	}

	.icon {
		font-size: 3rem;
		margin-bottom: 1rem;
	}

	.overview-card h3 {
		font-size: 1.5rem;
		margin: 0 0 1rem 0;
		color: #a78bfa;
	}

	.overview-card p {
		font-size: 1rem;
		line-height: 1.6;
		color: #cbd5e1;
		margin: 0;
	}

	/* Features Grid */
	.features-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
		gap: 1rem;
		margin-top: 2rem;
	}

	.feature-card {
		background: linear-gradient(135deg, #334155 0%, #475569 100%);
		padding: 1.5rem;
		border-radius: 10px;
		border-left: 3px solid #4f46e5;
		transition: all 0.3s ease;
	}

	.feature-card:hover {
		border-left-width: 6px;
		transform: translateX(5px);
	}

	.feature-code {
		background: #4f46e5;
		color: white;
		padding: 0.3rem 0.8rem;
		border-radius: 6px;
		font-family: 'Courier New', monospace;
		font-size: 0.9rem;
		font-weight: 600;
	}

	.feature-name {
		font-size: 1.1rem;
		color: #f1f5f9;
		margin: 1rem 0 0.5rem 0;
	}

	.feature-desc {
		font-size: 0.95rem;
		color: #94a3b8;
		margin: 0;
		line-height: 1.5;
	}

	/* Models Grid */
	.models-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
		gap: 2rem;
		margin-top: 2rem;
	}

	.model-card {
		background: linear-gradient(135deg, #1e293b 0%, #334155 100%);
		padding: 2rem;
		border-radius: 12px;
		border: 1px solid rgba(148, 163, 184, 0.2);
		transition: transform 0.3s ease, box-shadow 0.3s ease;
		overflow: hidden;
		display: flex;
		flex-direction: column;
	}

	.model-card:hover {
		transform: translateY(-5px);
		box-shadow: 0 15px 40px rgba(0, 0, 0, 0.3);
	}

	.model-name {
		font-size: 1.5rem;
		font-weight: 700;
		margin: 0 0 1rem 0;
		color: #f1f5f9;
	}

	.model-description {
		font-size: 0.95rem;
		color: #94a3b8;
		margin-bottom: 1.5rem;
		line-height: 1.6;
		min-height: 60px;
	}

	.metrics-grid {
		display: flex;
		gap: 1rem;
		margin-bottom: 1.5rem;
		flex-wrap: wrap;
	}

	.metric {
		flex: 1;
		min-width: 90px;
		text-align: center;
		padding: 1rem 0.5rem;
		background: rgba(15, 23, 42, 0.7);
		border-radius: 8px;
		border: 1px solid rgba(148, 163, 184, 0.1);
	}

	.metric-label {
		display: block;
		font-size: 0.85rem;
		color: #94a3b8;
		margin-bottom: 0.5rem;
		font-weight: 600;
		text-transform: uppercase;
		letter-spacing: 0.5px;
	}

	.metric-value {
		display: block;
		font-size: 1.4rem;
		font-weight: 700;
		word-break: break-all;
	}

	.performance-badge {
		padding: 1rem;
		border-radius: 8px;
		text-align: center;
		font-size: 0.9rem;
		font-weight: 600;
		line-height: 1.5;
		margin-top: auto;
	}

	/* Metrics Explanation */
	.metrics-explanation {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
		gap: 1.5rem;
		margin-top: 2rem;
	}

	.metric-explain-card {
		background: linear-gradient(135deg, #334155 0%, #475569 100%);
		padding: 1.5rem;
		border-radius: 10px;
		border: 1px solid rgba(148, 163, 184, 0.2);
	}

	.metric-explain-header {
		margin-bottom: 1rem;
	}

	.metric-badge {
		background: #10b981;
		color: white;
		padding: 0.3rem 0.8rem;
		border-radius: 6px;
		font-size: 0.85rem;
		font-weight: 700;
		display: inline-block;
		margin-bottom: 0.5rem;
	}

	.metric-explain-card h4 {
		font-size: 1.2rem;
		color: #f1f5f9;
		margin: 0;
	}

	.metric-explain-desc {
		color: #cbd5e1;
		margin: 0.5rem 0;
		line-height: 1.6;
	}

	.metric-interpret {
		color: #94a3b8;
		margin: 0.5rem 0 0 0;
		font-size: 0.95rem;
		line-height: 1.6;
	}

	/* Image Container */
	.image-container {
		margin: 2rem 0;
		border-radius: 12px;
		overflow: hidden;
		background: white;
		box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
	}

	.chart-image {
		width: 100%;
		height: auto;
		display: block;
	}

	/* Insight Box */
	.insight-box {
		background: linear-gradient(135deg, #059669 0%, #10b981 100%);
		padding: 1.5rem;
		border-radius: 10px;
		margin-top: 1.5rem;
		border-left: 4px solid #34d399;
		color: white;
		line-height: 1.7;
	}

	.insight-box strong {
		font-weight: 700;
	}

	/* Results Section */
	.results-section {
		background: linear-gradient(135deg, #1e293b 0%, #334155 100%);
		border: 2px solid #4f46e5;
	}

	.results-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
		gap: 2rem;
		margin-top: 2rem;
	}

	.result-card {
		background: rgba(15, 23, 42, 0.7);
		padding: 2rem;
		border-radius: 12px;
		border: 1px solid rgba(148, 163, 184, 0.2);
	}

	.result-card.winner {
		background: linear-gradient(135deg, #1e3a8a 0%, #1e40af 100%);
		border: 2px solid #fbbf24;
		box-shadow: 0 10px 40px rgba(251, 191, 36, 0.3);
	}

	.result-icon {
		font-size: 3rem;
		margin-bottom: 1rem;
	}

	.result-card h3 {
		font-size: 1.8rem;
		color: #f1f5f9;
		margin: 0 0 1.5rem 0;
	}

	.result-stats {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 1rem;
		margin-bottom: 1.5rem;
	}

	.stat {
		text-align: center;
		padding: 1rem;
		background: rgba(255, 255, 255, 0.1);
		border-radius: 8px;
	}

	.stat-value {
		display: block;
		font-size: 2rem;
		font-weight: 700;
		color: #fbbf24;
		margin-bottom: 0.3rem;
	}

	.stat-label {
		display: block;
		font-size: 0.85rem;
		color: #cbd5e1;
	}

	.result-conclusion {
		color: #e0e7ff;
		line-height: 1.7;
		margin: 0;
	}

	.takeaways-list {
		margin: 1rem 0 0 0;
		padding-left: 1.5rem;
		line-height: 1.8;
	}

	.takeaways-list li {
		color: #cbd5e1;
		margin-bottom: 0.8rem;
	}

	.takeaways-list strong {
		color: #a78bfa;
	}

	/* Applications Grid */
	.applications-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
		gap: 1.5rem;
		margin-top: 2rem;
	}

	.app-card {
		background: linear-gradient(135deg, #334155 0%, #475569 100%);
		padding: 2rem;
		border-radius: 12px;
		border: 1px solid rgba(148, 163, 184, 0.2);
		text-align: center;
		transition: all 0.3s ease;
	}

	.app-card:hover {
		transform: translateY(-5px);
		border-color: #4f46e5;
		box-shadow: 0 10px 30px rgba(79, 70, 229, 0.3);
	}

	.app-icon {
		font-size: 3rem;
		margin-bottom: 1rem;
	}

	.app-card h4 {
		font-size: 1.3rem;
		color: #f1f5f9;
		margin: 0 0 1rem 0;
	}

	.app-card p {
		font-size: 0.95rem;
		color: #94a3b8;
		line-height: 1.6;
		margin: 0;
	}

	/* Technical Section */
	.technical-section {
		background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
	}

	.tech-details {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
		gap: 1.5rem;
		margin-top: 2rem;
	}

	.tech-box {
		background: rgba(51, 65, 85, 0.5);
		padding: 1.5rem;
		border-radius: 10px;
		border-left: 3px solid #10b981;
	}

	.tech-box h4 {
		font-size: 1.3rem;
		color: #10b981;
		margin: 0 0 1rem 0;
	}

	.tech-box ul {
		margin: 0;
		padding-left: 1.5rem;
		line-height: 1.8;
	}

	.tech-box li {
		color: #cbd5e1;
		margin-bottom: 0.5rem;
	}

	/* Responsive Design */
	@media (max-width: 768px) {
		.container {
			padding: 1rem;
		}

		.title {
			font-size: 2rem;
		}

		.subtitle {
			font-size: 1rem;
		}

		.section {
			padding: 1.5rem;
		}

		.section-title {
			font-size: 1.5rem;
		}

		.overview-grid,
		.features-grid,
		.models-grid,
		.results-grid,
		.applications-grid,
		.tech-details {
			grid-template-columns: 1fr;
		}

		.metrics-grid {
			grid-template-columns: 1fr;
		}
	}
</style>
