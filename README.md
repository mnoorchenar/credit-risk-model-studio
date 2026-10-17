---
title: Credit Risk Model Studio
emoji: 📊
colorFrom: blue
colorTo: indigo
sdk: static
pinned: false
short_description: Train, explain and govern a mortgage default model
---

<div align="center">

<h1>📊 Credit Risk Model Studio</h1>
<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=22&duration=3000&pause=1000&color=2563eb&center=true&vCenter=true&width=700&lines=Probability-of-Default+Model+Workbench;Train+·+Explain+·+Decide+·+Govern;100%+In-Browser+·+Nothing+Uploaded" alt="Typing SVG"/>

<br/>

[![JavaScript](https://img.shields.io/badge/JavaScript-ES2020-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://developer.mozilla.org/docs/Web/JavaScript)
[![HTML5](https://img.shields.io/badge/HTML5-Static-E34F26?style=for-the-badge&logo=html5&logoColor=white)](https://developer.mozilla.org/docs/Web/HTML)
[![HuggingFace](https://img.shields.io/badge/HuggingFace-Inference-ffcc00?style=for-the-badge&logo=huggingface&logoColor=black)](https://huggingface.co/docs/inference-providers)
[![Status](https://img.shields.io/badge/Status-Active-22c55e?style=for-the-badge)](#)

<br/>

[![Live Demo](https://img.shields.io/badge/%E2%96%B6_Live_Demo-Hugging_Face_Space-ffcc00?style=for-the-badge&logo=huggingface&logoColor=black)](https://huggingface.co/spaces/mnoorchenar/credit-risk-copilot)
[![Source Code](https://img.shields.io/badge/Source_Code-GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/mnoorchenar/credit-risk-copilot)

<br/>

**📊 Credit Risk Model Studio** — An end-to-end, in-browser workbench for a **probability-of-default model** on residential secured lending (mortgages and HELOCs). Everything runs client-side: the data is generated, cleaned, modelled, explained and validated in your browser, and nothing is uploaded.

<br/>

---

</div>

## Table of Contents

- [Overview](#-overview)
- [The workflow](#️-the-workflow)
- [Run and test locally](#-run-and-test-locally)
- [A chat assistant on every page](#-a-chat-assistant-on-every-page)
- [AI is optional](#-ai-is-optional)
- [Notes](#-notes)
- [Project Structure](#-project-structure)
- [Author](#-author)
- [Contributing](#-contributing)
- [Disclaimer](#️-disclaimer)
- [License](#-license)

---

## 📖 Overview

> **All data is synthetic.** It does not come from any lender and is not tied to any institution's portfolio, policy or models.

---

## 🏗️ The workflow

| Step | What you can do |
|---|---|
| **1 Data** | 30,000 accounts with realistic defects. Seven automated integrity controls find missing values, invalid codes and duplicates; fixes are logged and use training-set medians only (no leakage). Explore any feature against default rate. |
| **2 Train** | Train a **champion** regularised logistic scorecard and a **challenger** gradient-boosting model written from scratch. Tune hyperparameters, watch live learning curves, and use the *Overfit* preset to see a model memorise its training data and early stopping fix it. |
| **3 Evaluate** | ROC, AUC / Gini / KS, calibration, decile lift, score distribution and an interactive approve/decline operating point. |
| **4 Explain** | Exact **TreeSHAP** (verified against brute-force Shapley values), SHAP beeswarm, mean \|SHAP\| vs permutation importance, partial dependence / ICE with business-logic checks, and per-account waterfalls with adverse-action style **reason codes** and **counterfactual recourse**. |
| **5 Decide** | Turn PDs into auto-approve / manual-review / decline bands, expected vs realised loss, strategy curves and a **swap-set analysis** against a rules-based policy at the same approval rate. |
| **6 Govern** | Nine automated validation checks (discrimination, overfitting, out-of-time, calibration, PSI, monotonicity, segment consistency, explainability, data integrity), population drift, macro stress tests and a downloadable model card. |
| **7 Copilot** | An executive model-risk memo and Q&A. Optional AI (free Hugging Face Inference Providers) writes the text, but may only quote numbers from the computed fact sheet: anything else, or any claim that contradicts the validation results, is rejected and a template is used. |

---

## 🚀 Run and test locally

```bash
python -m http.server 7860          # then open http://localhost:7860
node --test tests/ml.test.mjs tests/governance.test.mjs tests/chat.test.mjs
```

The tests cover metrics, training, TreeSHAP additivity and equality with brute-force Shapley values, decision bands, swap-set maths, recourse, stress tests and the AI guardrails.

---

## 🤖 A chat assistant on every page

The chat bubble (bottom right) knows the page you are on. Each tab builds a **fact sheet from what is on screen** (selected feature, account, model, cutoffs, stress sliders, training results) plus a short glossary. Facts are ranked by relevance to your question and the answer is written only from them. It remembers the conversation per page for follow-ups, shows which facts it used, and has **Reset** (this page) and **Clear all pages**. The Copilot tab shares the same chat store. Out-of-scope questions get a clear scope message, and AI answers that quote unknown numbers or overstate the validation results are rejected.

---

## 🤖 AI is optional

Click **Connect AI** and paste a Hugging Face token that can call Inference Providers (a free fine-grained token is enough).
It stays in your browser, is sent only to `router.huggingface.co`, and is stored locally only if you tick "Remember on this device".
Without a token every feature still works; only the memo and answers fall back to built-in templates.

---

## 📝 Notes

- Models, SHAP, PSI and metrics are implemented in plain JavaScript (`js/ml.js`) so every number is traceable.
- Stress-test sensitivities are mechanical re-scoring, not an economic forecast. Segment checks cover business segments only; a real fairness review is broader.

---

## 📁 Project Structure

```
credit-risk-copilot/
│
├── 📂 js/
│   ├── app.js
│   ├── chat.js
│   ├── common.js
│   ├── data.js
│   ├── explain.js
│   ├── governance.js
│   └── … 12 more
├── 📂 tests/                     # Automated tests
│   ├── chat.test.mjs
│   ├── governance.test.mjs
│   └── ml.test.mjs
├── 📂 vendor/
│   └── chart.umd.js
├── 📄 GUIDE.md
├── 📄 index.html                 # App entry page
└── 📄 styles.css
```

---

## 👨‍💻 Author

<div align="center">

<table>
<tr>
<td align="center" width="100%">

<img src="https://avatars.githubusercontent.com/mnoorchenar" width="120" style="border-radius:50%; border: 3px solid #4f46e5;" alt="Mohammad Noorchenarboo"/>

<h3>Mohammad Noorchenarboo</h3>

<code>Data Scientist</code> &nbsp;|&nbsp; <code>AI Researcher</code> &nbsp;|&nbsp; <code>Biostatistician</code>

📍 &nbsp;Ontario, Canada &nbsp;&nbsp; 📧 &nbsp;[mohammadnoorchenarboo@gmail.com](mailto:mohammadnoorchenarboo@gmail.com)

──────────────────────────────────────

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/mnoorchenar)&nbsp;
[![Personal Site](https://img.shields.io/badge/Website-mnoorchenar.github.io-4f46e5?style=for-the-badge&logo=githubpages&logoColor=white)](https://mnoorchenar.github.io/)&nbsp;
[![HuggingFace](https://img.shields.io/badge/HuggingFace-ffcc00?style=for-the-badge&logo=huggingface&logoColor=black)](https://huggingface.co/mnoorchenar)&nbsp;
[![Google Scholar](https://img.shields.io/badge/Scholar-4285F4?style=for-the-badge&logo=googlescholar&logoColor=white)](https://scholar.google.ca/citations?user=nn_Toq0AAAAJ&hl=en)&nbsp;
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/mnoorchenar)

</td>
</tr>
</table>

</div>

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. **Fork** the repository
2. **Create** a feature branch: `git checkout -b feature/amazing-feature`
3. **Commit** your changes: `git commit -m 'Add amazing feature'`
4. **Push** to the branch: `git push origin feature/amazing-feature`
5. **Open** a Pull Request

---

## ⚠️ Disclaimer

> [!WARNING]
> This project is developed strictly for educational and research purposes and does not constitute professional advice of any kind. All datasets used are either synthetically generated or publicly available — no real user data is stored. This software is provided "as is" without warranty of any kind; use at your own risk.

---

## 📜 License

Distributed under the **MIT License**.

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:2563eb,100:06b6d4&height=120&section=footer&text=Made%20with%20%E2%9D%A4%EF%B8%8F%20by%20Mohammad%20Noorchenarboo&fontColor=ffffff&fontSize=18&fontAlignY=80" width="100%"/>

[![GitHub Stars](https://img.shields.io/github/stars/mnoorchenar/credit-risk-copilot?style=social)](https://github.com/mnoorchenar/credit-risk-copilot/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/mnoorchenar/credit-risk-copilot?style=social)](https://github.com/mnoorchenar/credit-risk-copilot/fork)

<sub>The name "Credit Risk Model Studio" is used purely for academic and research purposes. Any similarity to existing product names or trademarks is entirely coincidental. This project has no affiliation with any commercial entity.</sub>

</div>
