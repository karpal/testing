require('dotenv').config();
const fetch = (...args) => import('node-fetch').then(({ default: fetch }) => fetch(...args));

const BEARER_TOKEN = process.env.BEARER_TOKEN;
const CHAIN_ID = 10143;
const LOOP_INTERVAL_MS = 10 * 1000;

const tokenPairs = require('./pairs'); // impor pair dari file lain

function generateStrategyName(base, quote) {
  const time = new Date().toISOString().split("T")[0];
  return `${base.symbol} → ${quote.symbol} | MARKET | ${time} | karpal`;
}

function getRandomElement(arr) {
  return arr[Math.floor(Math.random() * arr.length)];
}

async function createStrategy(pair) {
  const base = pair.baseToken;
  const quote = pair.quoteToken;

  const payload = {
    chainId: CHAIN_ID,
    inputTokens: [
      {
        address: base.address,
        amount: "100000000000000000000"
      }
    ],
    name: generateStrategyName(base, quote),
    slippage: -1,
    status: "NOT_STARTED",
    marketOrders: [
      {
        allocationPercentage: 100,
        quoteToken: quote.address
      }
    ],
    singleLimitOrders: [],
    dcas: []
  };

  try {
    const res = await fetch("https://app.hedgemony.xyz/api-proxy/strategies", {
      method: "POST",
      headers: {
        "accept": "application/json, text/plain, */*",
        "authorization": `Bearer ${BEARER_TOKEN}`,
        "content-type": "application/json"
      },
      body: JSON.stringify(payload)
    });

    const data = await res.json();

    if (res.status === 401) {
      console.error(`[✖️] Unauthorized (Token mungkin expired) → ${base.symbol} → ${quote.symbol}`);
    } else if (res.ok) {
      console.log(`[✔️] Strategi dibuat: ${payload.name}`);
    } else {
      console.error(`[✖️] Gagal buat strategi untuk ${base.symbol} → ${quote.symbol}:`, data);
    }
  } catch (err) {
    console.error(`[✖️] Error saat fetch strategi: ${base.symbol} → ${quote.symbol}:`, err.message);
  }
}

async function runStrategyLoop() {
  const pair = getRandomElement(tokenPairs);
  await createStrategy(pair);
}

console.log(`🔁 Memulai auto-strategy generator setiap ${LOOP_INTERVAL_MS / 1000} detik...`);

runStrategyLoop();
setInterval(runStrategyLoop, LOOP_INTERVAL_MS);
