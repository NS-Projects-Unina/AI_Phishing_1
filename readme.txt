# 🛡️ ARGUS — AI-Powered Phishing Email Detection & Generation

> ARGUS è un sistema dual-component basato su Large Language Models per il **rilevamento** e la **generazione** di e-mail di phishing, pensato a fini di studio, simulazione e prevenzione.

---

## 📋 Descrizione

Il sistema è composto da due sottosistemi indipendenti:

- **Detection** — analizza le e-mail in una casella Gmail e le classifica come *phishing* o *non phishing*, sia in tempo reale (nuove e-mail) che retroattivamente (e-mail già ricevute).
- **Generation** — genera e-mail di phishing realistiche a partire da un prompt testuale fornito dall'utente, con possibilità di modifica e invio automatico.

Entrambi i sottosistemi si basano su **LLaMA 2 7B** fine-tunato con la tecnica **LoRA** (Low-Rank Adaptation).

---

## 🏗️ Architettura

```
ARGUS
├── Subsystem for Detection
│   ├── LLaMA 2:7B (fine-tuned per classificazione binaria)
│   └── Integrazione Gmail via IMAP
└── Subsystem for Generation
    ├── LLaMA 3.1:70B (generazione dataset di training)
    ├── LLaMA 2:7B (fine-tuned per generazione e-mail)
    └── Integrazione Gmail via SMTP
```

---

## 📁 Struttura del Repository

```
AI_Phishing_1/
├── clientEmail.py                    # Client detection (IMAP + LLM)
├── client_InvioMail.py               # Client generazione e invio (SMTP + LLM)
├── Llama-2-7b-hf.ipynb               # Notebook training & evaluation (detection e generation)
├── Generatore_di_prompt_llama_3_1_70b.py  # Generazione dataset per il modello generativo
├── llama-finetuned-final/            # Pesi fine-tunati (detection)
└── llama-finetuned-generation/       # Pesi fine-tunati (generation)
```

---

## ⚙️ Requisiti

- Python 3.x
- Account Gmail con **autenticazione a due fattori** abilitata
- Account [Hugging Face](https://huggingface.co/) con accesso a [LLaMA 2](https://huggingface.co/meta-llama/Llama-2-7b-hf)
- GPU con almeno 24 GB di VRAM (testato su NVIDIA L4 via Lightning AI)

### Dipendenze Python

```bash
pip install torch transformers datasets accelerate peft bitsandbytes mauve-text
```

---

## 🚀 Setup e Utilizzo

### 1. Accesso al modello LLaMA 2

Richiedere l'accesso al modello su Hugging Face:
👉 https://huggingface.co/meta-llama/Llama-2-7b-hf

### 2. Token Hugging Face

Creare un **Access Token** (Read) nelle impostazioni del proprio account HF e salvarlo.

### 3. App Password Gmail

Abilitare la 2FA sul proprio account Google, poi generare una **App Password** cercando "Password per le app" nelle impostazioni account.

---

### 🔍 Sistema di Detection

1. Scaricare la cartella `llama-finetuned-final/` e il file `clientEmail.py` nella stessa directory.
2. Aprire `clientEmail.py` e modificare:
   - Nella funzione `load_model()`: inserire il token Hugging Face
   - Righe 552 e 554: inserire indirizzo Gmail e App Password
3. Eseguire:

```bash
python clientEmail.py
```

Il sistema chiederà:
- Se analizzare solo le e-mail future o anche quelle passate
- Se salvare i risultati in un file `report.txt`

> Per interrompere: `Ctrl + C`

---

### ✉️ Sistema di Generazione

**Opzione A — Notebook**

1. Aprire `Llama-2-7b-hf.ipynb`
2. Installare le dipendenze e autenticarsi su Hugging Face
3. Caricare il modello con i pesi da `llama-finetuned-generation/`
4. Inserire un prompt e ottenere l'e-mail generata

**Opzione B — Script Python**

1. Scaricare `llama-finetuned-generation/` e `client_InvioMail.py` nella stessa directory
2. Aprire `client_InvioMail.py` e modificare:
   - Nella funzione `caricamento_modulo()`: inserire il token Hugging Face
   - Righe 24 e 25: inserire indirizzo Gmail e App Password
3. Eseguire:

```bash
python client_InvioMail.py
```

Il sistema chiederà il prompt, mostrerà l'e-mail generata e offrirà la possibilità di modificarla e/o inviarla.

---

## 📊 Risultati

### Detection

| Dataset | Accuracy | Precision | Recall | F1-score |
|---|---|---|---|---|
| CEAS_08 | 0.8781 | 0.8279 | 0.9995 | 0.9056 |
| TREC_06 | 0.9762 | 0.9277 | 0.9843 | 0.9552 |
| Cross Validation | 0.9941 | 0.9943 | 0.9938 | 0.9941 |

**Robustezza (test adversariali):**

| Scenario | Accuracy | F1-score |
|---|---|---|
| Noise 15% | 0.9910 | 0.9912 |
| Noise 40% | 0.9899 | 0.9901 |

### Generation

| Metrica | Valore |
|---|---|
| Tempo medio di risposta | ~54 secondi |

---

## 🧠 Dettagli Tecnici

### Modello di Detection
- **Base model**: LLaMA 2 7B (quantizzato a 8 bit)
- **Fine-tuning**: LoRA (r=8, α=32, dropout=0.05)
- **Layers**: Q_proj e V_proj
- **Dataset**: 33.619 istanze bilanciate (TREC-05, Nazario, Nigerian Fraud, TREC-07)

### Modello di Generation
- **Dataset generation**: LLaMA 3.1 70B per la creazione automatica dei prompt
- **Fine-tuning**: LLaMA 2 7B con LoRA sullo stesso schema del modello di detection
- **Dataset**: 15.000 e-mail di phishing con prompt associato

### Hardware utilizzato
- **Training**: NVIDIA L4 Tensor Core (24 GB VRAM) via Lightning AI
- **Dataset generation**: GPU AWS (48 GB VRAM) via Lightning AI

---

## ⚠️ Note Etiche

Questo sistema è sviluppato **esclusivamente a fini di ricerca e simulazione** nell'ambito della sicurezza informatica. Il sottosistema di generazione non memorizza dati sugli utenti né mantiene uno storico delle interazioni. L'uso del sistema per attività malevole reali è esplicitamente vietato.

---

## 👥 Autori

| Nome | Matricola | Email |
|---|---|---|
| Corrado Colaleo | M63001762 | co.colaleo@studenti.unina.it |
| Francesca De Rosa | M63001811 | francesca.derosa19@studenti.unina.it |
| Riccardo Carta | M63001773 | ri.carta@studenti.unina.it |
