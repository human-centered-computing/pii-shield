# 🛡️ PII-Shield: Clientseitige PII-Maskierung und KI-Gateway

> Übersetzungsstatus: KI-unterstützter Entwurf, menschliche Sprachprüfung ausstehend.

[English](README.md) · [Türkçe](README.tr.md) · [Deutsch](README.de.md) · [العربية](README.ar.md)

![Angular](https://img.shields.io/badge/Angular-DD0031?style=for-the-badge&logo=angular&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)
![ONNX Runtime](https://img.shields.io/badge/ONNX-005C84?style=for-the-badge&logo=onnx&logoColor=white)
![License: AGPL-3.0](https://img.shields.io/badge/License-AGPL--3.0-blue.svg?style=for-the-badge)


**PII-Shield** ist eine datenschutzorientierte, vollständig clientseitige Webanwendung, die personenbezogene Informationen (PII) im Browser erkennt und maskiert, bevor Prompts und Dokumente an große Sprachmodelle (z. B. Gemini, OpenAI) gesendet werden. Ohne Backend nutzt sie Web Workers und browserinterne Transformers, um zu verhindern, dass unmaskierte sensible Daten das Gerät verlassen.

## 🚀 Funktionen
- **Vollständig clientseitig:** Verarbeitung lokal im Browser-RAM; keine Serverabhängigkeit oder Übertragung an externe Datenbanken.
- **Mehrformat-Dokumentanalyse:** Drag & Drop für TXT, CSV, DOCX (mammoth.js) und PDF (pdf.js).
- **Hybride Entitätserkennung (NER):** @xenova/transformers erkennt Personen (PER) und Organisationen (ORG) mit einem BERT-Modell im Browser; reguläre Ausdrücke erfassen E-Mail-Adressen und Telefonnummern.
- **Menschliche Kontrolle (HITL):** Masken im lokalen Wörterbuch prüfen, falsch positive Treffer entfernen und eigene Masken vor der KI-Anfrage hinzufügen.
- **Demaskierung:** Platzhalter wie [PERSON_1] in LLM-Antworten werden durch ursprüngliche sensible Werte ersetzt.

## 🧠 Architektur
```mermaid
flowchart TD
    User["User / Document Upload"] -->|"Drag & Drop / Text Input"| ShieldEditor["ShieldEditor Component"]
    ShieldEditor -->|"Read TXT, CSV, DOCX, PDF"| FileUploader["FileUploader Component"]
    FileUploader -->|"Extracted Raw Text"| ShieldEditor
    ShieldEditor -->|"RxJS debounce 600ms"| PIIService["PiiShield Service"]
    PIIService -->|"Post Message: extract_entities"| Worker["Web Worker: pii-shield.worker.ts"]
    Worker -->|"@xenova/transformers BERT NER"| Worker
    Worker -->|"Return PER / ORG entities"| PIIService
    PIIService -->|"Regex EMAIL, PHONE & HITL Manual Masks"| PIIService
    PIIService -->|"Masked Text & RAM Dictionary"| ShieldEditor
    ShieldEditor -->|"Send Masked Prompt"| LLMService["LLM API Service"]
    LLMService -->|"AI Response with Tags"| ShieldEditor
    ShieldEditor -->|"Demask via Dictionary"| DemaskedView["Restored Original Text"]
```

## Lizenz
**GNU Affero General Public License v3.0 (AGPL-3.0)**; [LICENSE](LICENSE). Beiträge werden unter derselben Lizenz angenommen. Modifizierte erfasste Versionen müssen die AGPL-3.0-Bedingungen einschließlich der Quellcode-Verfügbarkeit bei Netzwerkinteraktion wahren.