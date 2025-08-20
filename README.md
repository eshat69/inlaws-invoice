# inlaws-invoice

A tongue‑in‑cheek “wedding economics” toy app that **satirizes** dowry culture by turning it into a transparent, tweakable calculator. Use it to spark conversations, not to normalize dowry.

> **Ethics disclaimer**
> Dowry is harmful and illegal in many places. This project is satire + tech demo. The default configuration strongly nudges toward **Zero Dowry** (see *Positive Mode*). Don’t use this to justify payments.

---

## Features

* ⚖️ **Configurable scoring engine** with weights, caps, and social penalties.
* 🧮 **Transparent formula** – see exactly how the “invoice” is computed.
* 🌏 **Locale presets** (currency, language, cultural notes).
* 🎛️ **Modes**: `classic` (for satire), `positive` (promotes zero‑dowry), `custom`.
* 📊 **Bias checks**: automatic warnings for biased inputs or stereotypes.
* 🧪 **Demo data** + seed script.
* 🖥️ **CLI & Web UI** (optional) – quick mock server included.

---

## Quick Start

### Option A: Run the mock web app

```bash
# 1) Clone
git clone https://github.com/your-username/inlaws-invoice
cd inlaws-invoice

# 2) Install & start
npm i
npm run dev
# App runs at http://localhost:5173 (Vite)
```

### Option B: Use the CLI

```bash
npm i -g inlaws-invoice
inlaws-invoice --mode positive --income 45000 --education "BSc CSE" --assets "none" --family-expectations low
```

> Prefer Python? See `examples/python_example.py` for a drop‑in engine call.

---

## The Formula (default)

The engine computes a **base score** and then applies **modifiers**. Final output is an “invoice” object (for satire) plus a **moral score** that pushes toward zero.

```
base = w_income * f_income(income)
     + w_education * f_education(level)
     + w_skills * f_skills(list)
     + w_location * f_location(city)

modifiers = - p_greed * f_expectations(expectations)
          - p_gender_bias * flag_gender_bias
          - p_luxury * f_luxury(demands)
          + bonus_zero_dowry * chose_zero

final_score = clamp(base + modifiers, 0, cap)

suggested_dowry = 0 in positive mode
suggested_dowry = g(final_score) in classic mode (satire)
```

**Notes**

* `positive` mode hard‑sets `suggested_dowry = 0` and explains why.
* Any detected **bias flags** reduce the score and add warnings.
* `cap` prevents absurd numbers; defaults to `100`.

---

## Example Input

```json
{
  "mode": "positive",
  "profile": {
    "education": "BSc CSE",
    "income": 45000,
    "skills": ["ML", "Data Viz"],
    "city": "Dhaka",
    "values": ["kindness", "shared chores"],
    "family_expectations": "low",
    "demands": []
  }
}
```

## Example Output (abbrev.)

```json
{
  "final_score": 62.5,
  "suggested_dowry": 0,
  "mode": "positive",
  "notes": [
    "Zero‑dowry commitment detected — great!",
    "No luxury demands found",
    "Bias checks passed"
  ]
}
```

---

## Config

Configuration lives in `config/*.json`.

```json
{
  "weights": {
    "income": 0.3,
    "education": 0.25,
    "skills": 0.2,
    "location": 0.15
  },
  "penalties": {
    "greed": 0.4,
    "gender_bias": 0.6,
    "luxury": 0.2
  },
  "cap": 100,
  "mode": "positive",
  "currencies": { "default": "BDT", "fallback": "USD" }
}
```

### Locale presets

* `locale/bd.json`, `locale/in.json`, `locale/pk.json`, … set currency symbol, separators, and cultural **notes** explaining legal/ethical context.

---

## API (toy)

### `POST /api/invoice`

* **Body**: `InvoiceRequest` (see schema below)
* **Response**: `Invoice`

**Schema**

```ts
// InvoiceRequest
{
  mode?: 'classic' | 'positive' | 'custom';
  profile: {
    education: string;
    income?: number; // yearly, numeric
    skills?: string[];
    city?: string;
    values?: string[];
    family_expectations?: 'low'|'medium'|'high';
    demands?: string[]; // luxury asks (car, gold, etc.)
  };
}

// Invoice
{
  final_score: number;
  suggested_dowry: number; // 0 in positive mode
  currency: string;
  warnings: string[];
  notes: string[];
}
```

---

## CLI

```bash
# Classic satire (will still cap & warn)
inlaws-invoice --mode classic --education "MBA" --income 90000 --demands "car,gold"

# Positive, zero‑dowry pledge card
inlaws-invoice --mode positive --education "BSc CSE" --values "kindness,mutual respect"
```

CLI flags are mirrored in `--config path/to/config.json`.

---

## Dev Setup

* Node ≥ 18
* pnpm or npm
* TypeScript
* Vite (web), Express (API), Zod (validation), Vitest (tests)

```bash
pnpm i
pnpm run dev        # web + api
pnpm run test
```

Project layout:

```
/engine          # core scoring engine (framework‑agnostic)
/api             # express routes
/web             # vite + react ui
/config          # weights, penalties, locales
/examples        # quick scripts in TS & Python
```

---

## Bias & Safety

* The app **flags** any field likely to encode bias (gendered roles, "must pay", caste/community demands).
* Penalties increase with more demands; warnings link to resources about **zero‑dowry** movements and relevant laws.
* `positive` mode is default in production build.

---

## Contributing

PRs welcome! Good first issues:

* Add a new locale preset
* Improve bias detectors
* Add unit tests for `f_education`, `f_expectations`
* Create Bengali UI translations (বাংলা)

Please run `pnpm run lint && pnpm run test`.

---

## License

MIT for code. Content in `/locales` may include additional attribution.

---

## Credits

Built for fun discussions and education. If this helps nudge even one conversation toward **zero dowry**, it’s a win.

---

## .gitignore

Typical Node/TypeScript project ignore:

```
# dependencies
/node_modules

# build outputs
/dist
/build

# logs
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# environment
.env
.env.local

# editor
.vscode
.idea
*.swp

# coverage
/coverage

# misc
.DS_Store
*.tgz
```
