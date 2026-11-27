# 🛡️ PII-Shield: İstemci tarafında kişisel bilgi maskeleme ve yapay zekâ ağ geçidi

> Çeviri durumu: Yapay zekâ destekli taslak; insan dil incelemesi bekliyor.

[English](README.md) · [Türkçe](README.tr.md) · [Deutsch](README.de.md) · [العربية](README.ar.md)

![Angular](https://img.shields.io/badge/Angular-DD0031?style=for-the-badge&logo=angular&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)
![ONNX Runtime](https://img.shields.io/badge/ONNX-005C84?style=for-the-badge&logo=onnx&logoColor=white)
![License: AGPL-3.0](https://img.shields.io/badge/License-AGPL--3.0-blue.svg?style=for-the-badge)


**PII-Shield**, kullanıcı istemlerini ve belgelerini büyük dil modellerine (ör. Gemini, OpenAI) göndermeden önce tarayıcıda kişisel tanımlayıcı bilgileri (PII) algılayıp maskeleyen gizlilik odaklı, tamamen istemci taraflı bir web uygulamasıdır. Sunucu gerektirmez; Web Workers ve tarayıcı içi Transformers ile maskelenmemiş hassas verilerin cihazdan ayrılmasını önlemeyi hedefler.

## 🚀 Özellikler
- **%100 istemci tarafı:** İşleme tarayıcı RAM'inde gerçekleşir; sunucu bağımlılığı ve harici veritabanına veri gönderimi yoktur.
- **Çok formatlı belge ayrıştırma:** Sürükle-bırak ile TXT, CSV, DOCX (mammoth.js), PDF (pdf.js).
- **Hibrit varlık tanıma (NER):** @xenova/transformers tarayıcı BERT modeli kişi ve kurumları algılar; düzenli ifadeler e-posta ve telefon gibi standart biçimleri yakalar.
- **İnsan incelemesi (HITL):** Yerel sözlükte maskeleri inceleyin, yanlış pozitifleri kaldırın ve yapay zekâya sormadan önce özel maskeler ekleyin.
- **Maskeyi geri açma:** LLM yanıtındaki [PERSON_1] gibi etiketler özgün hassas değerlere dönüştürülür.

## 🧠 Mimari
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

## Lisans
**GNU Affero General Public License v3.0 (AGPL-3.0)**; [LICENSE](LICENSE). Katkılar aynı lisansla kabul edilir. Değiştirilmiş kapsam içi sürümler, ağ etkileşimine ilişkin kaynak erişilebilirliği dahil AGPL-3.0 koşullarını korumalıdır.