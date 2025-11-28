

# Full Hash Algorithm v3 (Hardware Implementation)

Questo repository contiene l'implementazione hardware di un modulo di hashing personalizzato, sviluppato come progetto per il corso di **Hardware and Embedded Security** (Master's Degree in Cybersecurity, Università di Pisa).

Il progetto mira a progettare, verificare e sintetizzare su FPGA un algoritmo di hashing robusto basato su S-box DES, con un focus sulla sicurezza hardware e l'integrità dei dati.

## 📋 Specifiche dell'Algoritmo

L'algoritmo "Full Hash Algorithm v3" genera un digest a **32-bit** (composto da 8 nibbles). Il processo operativo si divide in due fasi distinte:

1.  **Prima Fase (Elaborazione Messaggio):** Ogni byte del messaggio in input viene processato iterativamente (12 round per nibble) utilizzando operazioni di XOR, shift circolari e trasformazioni non lineari tramite una **S-box DES**.
2.  **Seconda Fase (Chiusura):** Una volta terminato il messaggio, l'algoritmo elabora la lunghezza del messaggio stesso (counter a 64-bit) per finalizzare il digest.

## 🛠 Architettura Hardware

Il design è stato implementato in **SystemVerilog** e segue un approccio modulare. L'architettura Top-Level (`full_hash_v3`) orchestra due sotto-moduli principali:


* **Control Part (`control_part.sv`):**
    * Implementa una **Macchina a Stati Finiti (FSM)** per gestire il flusso di controllo.
    * Gestisce i segnali di handshake (`F_rtr`, `F_dr`) per la sincronizzazione dei dati con moduli esterni.
    * Coordina le transizioni tra le fasi di lettura, Operazione 1, Operazione 2 e reset.

* **Operative Part (`operative_part.sv`):**
    * Rappresenta il **Datapath** del sistema ed esegue le operazioni di calcolo.
    * Contiene unità combinatorie specializzate:
        * `sbox.sv`: Implementazione LUT della S-box DES.
        * `xor_shift.sv`: Esegue operazioni di diffusione (XOR e shift).
        * `m6.sv` e `c6.sv`: Moduli di compressione vettoriale.
    * Include un modulo `inverter.sv` per l'adattamento dell'endianness del digest finale.

## 🚀 Flusso di Sviluppo

Il progetto è stato sviluppato seguendo quattro fasi metodologiche:

1.  **High-Level Modeling:** Creazione di un modello in **C++** (`high_level_model.cpp`) per simulare le funzionalità e servire da riferimento per la verifica.
2.  **RTL Design:** Definizione dell'architettura logica e decomposizione in sotto-moduli SystemVerilog.
3.  **Simulazione e Test:** Verifica funzionale tramite testbench (`testbench.sv`) coprendo vari scenari:
    * Esecuzione regolare.
    * Gestione del Reset asincrono in corsa.
    * Start "bloccanti" imprevisti.
    * Ritardi nella trasmissione dati.
    * Input vuoti (Empty Value Input).
4.  **Implementazione FPGA:** Sintesi e analisi temporale (STA) su hardware Intel.

## 📊 Risultati dell'Implementazione

Il design è stato sintetizzato utilizzando **Intel Quartus Prime** con target device **Intel Cyclone V** (5CGXFC9D6F27C7).

* **Risorse Logiche:** Utilizzo efficiente delle ALM (< 1% del device).
* **Performance Temporali (STA):**
    * Analisi effettuata con modello *Slow 1100mV 85C*.
    * Massima frequenza operativa stimata: **~136.54 MHz** (Periodo di clock ~7.35ns).

## 👥 Autori
* Diego Bernardini
* Giovanni Cisternini
