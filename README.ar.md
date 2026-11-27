# 🛡️ PII-Shield: إخفاء بيانات الهوية على جانب العميل وبوابة الذكاء الاصطناعي

> حالة الترجمة: مسودة بمساعدة الذكاء الاصطناعي، بانتظار مراجعة لغوية بشرية.

[English](README.md) · [Türkçe](README.tr.md) · [Deutsch](README.de.md) · [العربية](README.ar.md)

![Angular](https://img.shields.io/badge/Angular-DD0031?style=for-the-badge&logo=angular&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)
![ONNX Runtime](https://img.shields.io/badge/ONNX-005C84?style=for-the-badge&logo=onnx&logoColor=white)
![License: AGPL-3.0](https://img.shields.io/badge/License-AGPL--3.0-blue.svg?style=for-the-badge)


**PII-Shield** تطبيق ويب يضع الخصوصية أولاً ويعمل بالكامل على جانب العميل. يكتشف المعلومات الشخصية القابلة للتعريف ويخفيها في المتصفح قبل إرسال طلبات المستخدم ووثائقه إلى نماذج اللغة الكبيرة مثل Gemini وOpenAI. لا يحتاج إلى خادم خلفي ويستخدم Web Workers وTransformers في المتصفح بهدف منع خروج البيانات الحساسة غير المخفية من الجهاز.

## 🚀 الميزات
- **تنفيذ محلي بالكامل:** المعالجة في ذاكرة المتصفح؛ لا اعتماد على خادم ولا إرسال إلى قواعد بيانات خارجية.
- **تحليل صيغ متعددة:** سحب وإفلات ملفات TXT وCSV وDOCX (mammoth.js) وPDF (pdf.js).
- **اكتشاف هجين للكيانات (NER):** نموذج BERT عبر @xenova/transformers يكتشف الأشخاص (PER) والمؤسسات (ORG)؛ التعبيرات النمطية تلتقط البريد الإلكتروني وأرقام الهاتف.
- **مراجعة بشرية (HITL):** مراجعة القناع في قاموس محلي وإزالة النتائج الإيجابية الكاذبة وإضافة أقنعة يدوية قبل الاستعلام.
- **إزالة القناع:** استبدال الرموز مثل [PERSON_1] في الرد بالقيم الحساسة الأصلية.

## 🧠 البنية
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

## الترخيص
**GNU Affero General Public License v3.0 (AGPL-3.0)**؛ انظر [LICENSE](LICENSE). تقبل المساهمات بموجب الترخيص نفسه. يجب أن تحفظ النسخ المعدلة المشمولة أحكام AGPL-3.0 بما فيها إتاحة المصدر عند التفاعل الشبكي.