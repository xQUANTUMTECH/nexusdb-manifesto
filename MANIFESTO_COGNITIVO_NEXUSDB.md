# NexusDB — Un cervello accessorio per qualsiasi modello di AI

*X Quantum Tech FZE LLC · novembre 2026 · English version: [NEXUSDB_BEYOND_VECTOR_MANIFESTO.md](NEXUSDB_BEYOND_VECTOR_MANIFESTO.md)*

> «NexusDB» è un nome di lavoro. Non è il database NexusDB per Delphi (nexusdb.com); il nome definitivo del prodotto sarà scelto prima del lancio.

## 1. Il problema

Ogni organizzazione vuole che la sua AI conosca persone, prezzi, regole e procedure — e che resti aggiornata. I tre strumenti disponibili oggi si rompono tutti da qualche parte:

- **File di istruzioni** (`.md`, prompt): testo statico incollato in ogni richiesta. Il costo cresce con ogni pagina, oltre qualche decina di pagine non ci sta più, e non impara mai quale risposta era giusta.
- **Fine-tuning**: congela la conoscenza nei pesi. Costoso, da rifare a ogni cambiamento, impossibile sui modelli chiusi via API — e nei nostri test ha fatto smettere il modello di citare le fonti.
- **Memoria del fornitore**: vive dentro un solo assistente, opaca, legata a quel fornitore, centrata su fatti e preferenze invece che sulle procedure, e persa quando si cambia modello.

Manca una memoria che appartenga al cliente, funzioni con qualsiasi modello e impari come si fa il lavoro.

## 2. L'idea

Il modello è la corteccia: linguaggio e ragionamento generale, uguali per ogni cliente. Ciò che rende prezioso un professionista è diverso — ricordare cosa è successo, sapere qual è il passo successivo, trasformare il lavoro ripetuto in abitudine. Nel cervello sono sistemi separati. Nell'AI devono essere separati, portabili e di proprietà del cliente.

Una **cartuccia** NexusDB è un unico file portabile (`.nxm`) che sta accanto a qualsiasi modello, aperto o chiuso, piccolo o grande. Contiene:

1. **Conoscenza** — documenti e storico di lavoro con ricerca ibrida (semantica + parole chiave) e citazioni esatte delle fonti. *Nel motore Rust.*
2. **Esperienza** — ogni risposta verificata diventa una nuova via d'accesso alla sua fonte, usata solo se una nuova domanda è abbastanza simile (soglia di somiglianza), reversibile, per cliente. *Nel motore Rust.*
3. **Mini-cervelli** — piccole reti (1–3 milioni di parametri, su CPU, addestrate dall'uso in pochi secondi) che decidono dove cercare e cosa viene dopo. *Misurate in laboratorio; prossima integrazione nel motore.*
4. **Riordino** — un secondo passaggio sui primi 50 candidati. *Misurato in laboratorio; prossima integrazione nel motore.*

La cartuccia non impara fatti nuovi: impara le strade tra i fatti. Il modello resta invariato, quindi l'esperienza sopravvive al cambio di modello o di fornitore.

## 3. Cosa abbiamo misurato (novembre 2026)

Risultati su scala dimostrativa: singole GPU a noleggio, modelli aperti da 1–4 miliardi di parametri, un team centrale di due persone.

| ID | Cosa dimostra | Risultato | Test |
|---|---|---|---|
| E1 | Un modello piccolo con la nostra ricerca vale i modelli di frontiera | 69,6% vs 68,6–71,0% | 400 domande, giudice cieco; Gemma 3 4B contro quattro modelli di frontiera con la stessa cartuccia e ricerca standard |
| E2 | Nessun addestramento batte il fine-tuning, stesso modello | +32,6 vs +8,9 punti | Gemma 3 1B: cartuccia senza addestramento contro fine-tuning su tutti i passaggi |
| E3 | Impara dall'uso verificato, senza riaddestrare | +11,2 punti (48,1 → 59,4%) | 26.682 passaggi, formulazioni mai viste, 3 semi; danno su temi non toccati −0,4 |
| E4 | Il guadagno è il sistema, non i vettori | danno −29,0 vs −0,4 | Ablazione: copiare le domande passate come vettori rompe le altre ricerche; il sistema con soglia no |
| E5 | Conosce il passo successivo di una procedura | 2,6% → 50,5% | 400 procedure pubbliche nascoste fra 14.627 passaggi, 3 semi |
| E6 | Reti addestrate battono le regole scritte a mano sul lavoro reale | +5,2 / +7,1 punti | 849 note reali + 823 sessioni; 2 suddivisioni; soglie fissate prima |
| E7 | Impara parlandogli, con meno token | 71% vs 5% · 4,6× meno token in ingresso | Chat dal vivo con un modello da 4B; 8 dialoghi contro tutta la memoria incollata come file |
| E9 | Benchmark pubblico LongMemEval | **85,6%** con GPT-4o · 79,6% vs 63,8% di Zep con GPT-4o-mini | 500 domande, giudice ufficiale — ogni risposta pubblica |

Registro completo LongMemEval: **[xquantumtech.com/benchmark/longmemeval](https://xquantumtech.com/benchmark/longmemeval/)** · file: **[nexusdb-longmemeval](https://github.com/xQUANTUMTECH/nexusdb-longmemeval)**.

## 4. Come testiamo

- **Soglie prima dei risultati.** I criteri di successo (guadagno minimo, danno massimo) sono scritti prima di ogni prova; le configurazioni che falliscono sono riportate come fallimenti, non ritoccate sul test.
- **Materiale mai visto.** Formulazioni nuove, domande scritte da altre famiglie di modelli, note e sessioni escluse dall'addestramento; danno sempre misurato su temi non toccati.
- **Ripetizioni e intervalli.** Tre semi o due suddivisioni indipendenti, intervalli di confidenza al 95%, conteggio delle domande migliorate e peggiorate.
- **Giudice cieco e indipendente.** Un modello che non è un concorrente e non sa quale sistema ha risposto.
- **Risultati negativi conservati.** Le idee fallite restano nel registro.
- **Revisione indipendente.** Un sistema di AI separato ha fatto da avvocato del diavolo sul codice di apprendimento; i difetti trovati sono stati corretti e i numeri interessati rimisurati e abbassati.

## 5. Limiti dichiarati

- Risultati su scala dimostrativa; le prove su legale, scienza e agenti (E8) sono piccole e iniziali.
- In E1 i modelli di frontiera usavano la ricerca standard: l'affermazione precisa è «un modello da 4B con la nostra ricerca equivale a modelli di frontiera con una ricerca standard».
- In E3 l'insegnante è il passaggio di riferimento verificato; con un insegnante completamente automatico il guadagno finora è +5,6 punti (1 seme).
- LongMemEval misura il richiamo da chat sintetiche brevi; i sistemi che rispondono con modelli più recenti (es. gpt-5-mini) non sono confrontabili con le nostre prove su GPT-4o.
- Il nostro embedder (67% di bge-m3) e il nostro lettore sono ricerca, non prodotto.

## 6. Cosa sostituisce questo manifesto

La versione di aprile 2026 descriveva un «manifold DNA-Φ ispirato alla quantistica», un punteggio basato sulla fase e altri meccanismi mai dimostrati superiori a una semplice ricerca ibrida in un'ablazione. Quelle affermazioni sono ritirate. Tutto ciò che è scritto sopra è misurato, con metodo e limiti.

## Contatti

Maurizio Tarricone — Founder & CEO, X Quantum Tech FZE LLC (Emirati Arabi Uniti)
ceo@xquantumtech.com · [xquantumtech.com/it/nexusdb](https://xquantumtech.com/it/nexusdb)

Il motore è proprietario. Script, log e registro completo dei risultati sono disponibili a investitori e partner di progetto su richiesta, sotto NDA.

© 2026 X Quantum Tech FZE LLC.
