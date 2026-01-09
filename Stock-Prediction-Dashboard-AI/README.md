
# Stock Prediction Dashboard (AI)

A Flask web app that downloads historical stock data using `yfinance`, builds technical-indicator features, trains a GRU-based deep learning model, and returns predicted vs actual closing prices for a validation window—displayed in a simple dashboard UI.

## What this project does

- Accepts a stock ticker (example: `AAPL`).
- Downloads historical OHLC data (2015 → 2023) with `yfinance`.
- Builds features:
	- Close price
	- 7-day moving average (MA7)
	- 21-day moving average (MA21)
	- RSI
	- MACD (histogram-style value)
- Normalizes features with `MinMaxScaler`.
- Creates sequences (default: 60 time steps) and trains a GRU model.
- Predicts closing prices on the validation split and returns:
	- predictions + actual values
	- dates
	- metrics (MSE, RMSE, MAE)
	- training history (loss curves)

## Tech stack

- Backend: Flask (Python)
- Data: `yfinance`, `pandas`, `numpy`
- ML: TensorFlow/Keras (GRU)
- Frontend: HTML templates + static JS/CSS

## Project structure

```
Stock-Prediction-Dashboard-AI/
	app.py
	best_model.keras
	requirements.txt
	templates/
		index.html
	static/
		css/style.css
		js/main.js
```

## Requirements

- Python 3.10+ recommended
- Internet access (to download market data)

## Setup (Windows / PowerShell)

From the project folder:

```powershell
cd C:\X_plainable_ai\Stock-Prediction-Dashboard-AI

python -m venv .venv
.\.venv\Scripts\Activate.ps1

python -m pip install --upgrade pip
pip install -r requirements.txt
```

If installation fails due to version conflicts (common with TensorFlow / NumPy), see **Troubleshooting** below.

## Run the app

```powershell
cd C:\X_plainable_ai\Stock-Prediction-Dashboard-AI
.\.venv\Scripts\Activate.ps1

python app.py
```

Then open:

- http://127.0.0.1:5000

## How to use

1. Open the home page.
2. Enter a ticker symbol (letters only, up to 10 characters), e.g. `AAPL`, `MSFT`, `TSLA`.
3. Submit to receive predicted vs actual closing prices and evaluation metrics.

## API

### `POST /predict`

Accepts either form data or JSON:

**JSON example**

```bash
curl -X POST http://127.0.0.1:5000/predict \
	-H "Content-Type: application/json" \
	-d "{\"ticker\": \"AAPL\"}"
```

**Response includes**

- `predictions`: list of predicted closing prices (real scale)
- `historical_prices`: actual closing prices for the validation window (real scale)
- `dates`: date strings aligned to the validation window
- `metrics`: `mse`, `rmse`, `mae`
- `training_history`: training/validation loss by epoch

## Notes / performance

- The model is trained on each request to `/predict`. The first request can take a while depending on your CPU.
- Data range is fixed in `app.py` (2015-01-01 to 2023-01-01). If you want the latest data, adjust the `end=` date.

## Troubleshooting

### 1) NumPy / pandas / TensorFlow compatibility errors

If you see errors around `numpy` or binary compatibility, try installing a compatible set of versions.

Option A (simple reset):

```powershell
deactivate
Remove-Item -Recurse -Force .venv
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Option B (pin a safe baseline if needed):

```powershell
pip install "numpy<2" "pandas<3" "tensorflow>=2.12,<2.16" yfinance flask scikit-learn
```

### 2) “No data found” / invalid ticker

- Double-check the ticker symbol spelling.
- Some symbols require exchange-specific formats in `yfinance`.

### 3) App runs but nothing shows

- Confirm you’re visiting http://127.0.0.1:5000
- Check the terminal output for Flask errors.

## License

Add a license if you plan to publish this project.

