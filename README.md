# Docker Scout দিয়ে Software Supply Chain সুরক্ষিত করা

এই ডকুমেন্টে Docker Scout কী, কেন দরকার, SBOM ও Attestation কী জিনিস, আর Docker Scout কীভাবে vulnerability detect করে সেটা ব্যাখ্যা করা হয়েছে।

---

## সমস্যাটা কী?

Container image insecure হলে বড় ধরনের ঝুঁকি তৈরি হয়। বাস্তবতা বলছে:

- প্রায় **৬০%** organization বছরে অন্তত একটা security breach/vulnerability incident-এর মুখোমুখি হয়েছে, যেটা operational disruption ঘটায়
- এমন incident-এর **৪৪%** ক্ষেত্রে ১ ঘণ্টার বেশি downtime হয়
- একটা average data breach-এর খরচ প্রায় **$4.45 মিলিয়ন**

অর্থাৎ container security একটা "later fix করব" জিনিস না — শুরু থেকেই গুরুত্বপূর্ণ।

## সমাধান: Docker Scout

Docker Scout হলো এমন একটা টুল যেটা container image-এর মধ্যে থাকা **vulnerability automatically detect ও remediate (সমাধান)** করতে সাহায্য করে, এবং security standard মেনে চলা নিশ্চিত করে। এটা Docker Desktop, GitHub Actions, Jenkins, Kubernetes আর অন্যান্য CI tool-এর সাথে ভালোভাবে কাজ করে।

**মূল সুবিধা:**

- Trusted ও secure content নিশ্চিত করা
- পুরো Software Development Lifecycle (SDLC)-এর জন্য একটা "system of record" রাখা
- Continuous ভাবে security posture উন্নত করা

---

## Software Supply Chain Security (S3C) কী?

**Software supply chain** বলতে বোঝায় software develop করা থেকে শুরু করে deploy ও maintain করা পর্যন্ত পুরো end-to-end process। আর **S3C (Software Supply Chain Security)** হলো এই পুরো process-এর component ও ধাপগুলোকে সুরক্ষিত রাখার practice।

### একটা fundamental shift

আগে সাধারণত security ও compliance-কে software delivery/release-এর একদম শেষে, প্রায় afterthought হিসেবে দেখা হতো। S3C এই চিন্তাধারা বদলে দেয় — security-কে পুরো development lifecycle-এর মধ্যে ছড়িয়ে দেয়:

- **Inner loop** → development ও testing-এর সময়
- **Outer loop** → shipping ও monitoring-এর সময়

industry best-practice অনুসরণ করলে organization নিজের software-কে security threat, compliance risk থেকে রক্ষা করতে পারে, এবং প্রজেক্টের মধ্যে visibility, collaboration ও traceability বাড়ে — ফলে threat দ্রুত detect, response, ও remediate করা যায়।

### Supply chain সুরক্ষিত করার মূল ধাপ

- অ্যাপ্লিকেশন বানাতে/চালাতে যেসব software component ও dependency ব্যবহার হচ্ছে সেগুলো identify করা
- পুরো development lifecycle জুড়ে security testing automate করা
- supply chain-কে security threat-এর জন্য monitor করা
- software কীভাবে বানানো হবে ও তাতে কী component থাকবে, সেটা নিয়ন্ত্রণ করার জন্য security policy তৈরি করা

আজকাল software অনেক আলাদা আলাদা source থেকে আসা component দিয়ে তৈরি হয় — তাই ঠিক কোন component ব্যবহার হচ্ছে ও তার সাথে কী risk জড়িত, সেটা স্পষ্টভাবে জানা জরুরি।

### Docker Scout অন্য টুল থেকে আলাদা কেন?

প্রচলিত security tool গুলো সাধারণত **scheduled, point-in-time scan** করে — মানে নির্দিষ্ট সময়ে, নির্দিষ্ট stage-এ scan হয়। Docker Scout এর বদলে একটা **event-driven model** ব্যবহার করে যেটা পুরো supply chain জুড়ে কাজ করে। ফলে নতুন কোনো vulnerability প্রকাশ পেলে সেটা তোমার image-কে কীভাবে প্রভাবিত করছে, সেই updated risk assessment **কয়েক সেকেন্ডের মধ্যেই** পাওয়া যায় — development-এর অনেক আগেই।

**কীভাবে কাজ করে:**

1. Image-এর composition analyze করে একটা **SBOM (Software Bill of Materials)** তৈরি করে
2. এই SBOM-কে security advisory-র সাথে cross-reference করে CVE (Common Vulnerabilities and Exposures) খুঁজে বের করে
3. **২০টিরও বেশি security advisory source**-এর সাথে integrate থাকে, আর vulnerability database real-time-এ আপডেট হয় — তাই সবসময় সর্বশেষ তথ্য অনুযায়ী security posture দেখানো হয়

---

## Software Bill of Materials (SBOM)

### সাধারণ BOM কী?

BOM (Bill of Materials) হলো কোনো একটা product বানাতে যেসব material/part লাগে তার একটা লিস্ট — যেমন একটা কম্পিউটারের BOM-এ motherboard, CPU, RAM, power supply ইত্যাদির নাম ও পরিমাণ থাকবে।

### SBOM কী?

SBOM হলো ঠিক এই ধারণাটাই কিন্তু software-এর জন্য — একটা software-এ ব্যবহৃত সব component-এর লিস্ট, যার মধ্যে থাকে:

- open-source ও third-party component
- নিজে লেখা custom code

### কেন দরকার?

Software-এ ঠিক কোন কোন component আছে সেটা জানা থাকলে:

- সেই component-গুলোর মধ্যে কোনো vulnerability দ্রুত identify ও patch করা যায়
- কোনো component-এর license তোমার প্রজেক্টের সাথে সাংঘর্ষিক কিনা সেটা যাচাই করা যায়

### SBOM-এ সাধারণত কী থাকে

- software (যেমন কোনো library/framework)-এর নাম
- সেই software-এর version
- সেটা কোন license-এ distribute করা হয়েছে
- এটা যেসব অন্য component-এর উপর নির্ভর করে তাদের লিস্ট

### Docker Scout কীভাবে SBOM ব্যবহার করে

কোনো image analyze করার সময় Docker Scout হয় image-এর সাথে attestation হিসেবে যুক্ত থাকা SBOM ব্যবহার করে, নাহলে image-এর content analyze করে নিজেই একটা SBOM তৈরি করে ফেলে। এরপর এই SBOM-টাকে advisory database-এর সাথে cross-reference করে দেখা হয় কোনো component-এ known vulnerability আছে কিনা।

---

## Attestations

**Build attestation** হলো একটা image কীভাবে বানানো হয়েছে আর তাতে কী আছে সেই সম্পর্কে বিস্তারিত তথ্য। এগুলো build-time-এ **BuildKit** দিয়ে তৈরি হয় এবং metadata হিসেবে চূড়ান্ত image-এর সাথে যুক্ত থাকে — ফলে image-এর origin, creator, ও content ইন্সপেক্ট করা যায়।

Docker Scout এই attestation ব্যবহার করে image-এর security ও supply-chain posture যাচাই করে, এবং কোনো সমস্যা পেলে (যেমন attestation missing বা outdated) সমাধানের পরামর্শ দেয়।

**দুই ধরনের গুরুত্বপূর্ণ attestation:**

| ধরন | কী বলে |
|---|---|
| **SBOM** | image-এর ভিতরের software artifact-এর লিস্ট |
| **Provenance** | image-টা ঠিক কীভাবে build হয়েছে সেটার বিস্তারিত |

Attestation তৈরি করতে হয় নিচের flag দিয়ে:

```bash
docker buildx build --provenance --sbom .
```

এই attestation গুলো image index-এর সাথে যুক্ত থাকে, তাই পুরো image pull না করেই সেগুলো inspect করা যায়। Docker Scout এই metadata ব্যবহার করে আরও নির্ভুল সুপারিশ ও ভালো নিয়ন্ত্রণ দেয়।

---

## Remediation (সমাধান)

Docker Scout-এর **remediation feature** policy evaluation-এর ভিত্তিতে তৈরি recommendation দিয়ে supply-chain ও security সমস্যা সমাধানে সাহায্য করে — যাতে policy compliance বাড়ে, আর image metadata আরও ভালো হয়, ফলে ভবিষ্যতে Docker Scout আরও নির্ভুল evaluation করতে পারে।

এই feature দিয়ে যাচাই করা যায়:

- base image up-to-date আছে কিনা
- supply-chain attestation সম্পূর্ণ আছে কিনা

কোনো violation পাওয়া গেলে Docker Scout সরাসরি fix suggest করে — যেমন base image update করা বা missing attestation যোগ করা। যথেষ্ট তথ্য না থাকলে (compliance uncertain হলে) সমস্যা সমাধানে করণীয় পদক্ষেপ সুপারিশ করে।

Docker Scout Dashboard-এ গিয়ে এসব violation/uncertainty রিভিউ করা যায়, এবং GitHub-এর মতো integration থাকলে dashboard থেকেই সরাসরি fix automate করা যায়।

---

## গুরুত্বপূর্ণ প্রশ্নোত্তর

### Docker Scout অন্য security tool থেকে কীভাবে আলাদা?

Third-party security tool গুলো সাধারণত limited scope-এ কাজ করে — remediation guidance দিলেও সেটা সীমিত, runtime monitoring/protection-এ ঘাটতি থাকে, এবং Docker-specific build-এর জন্য policy evaluation সীমিত। Docker Scout পুরো supply chain-কে কেন্দ্র করে কাজ করে, actionable guidance দেয়, শক্তিশালী policy enforcement-সহ comprehensive runtime protection দেয় — শুধু vulnerability খুঁজে বের করাই না, শুরু থেকে secure application বানাতেও সাহায্য করে।

### Docker Hub ছাড়া অন্য registry-র সাথে কি Docker Scout ব্যবহার করা যায়?

হ্যাঁ। Docker Scout নিচের third-party registry-র সাথে integrate করা যায়:

- Artifactory
- Amazon Elastic Container Registry (ECR)
- Azure Container Registry (ACR)

এতে Docker Hub-এ hosted না থাকা image-এরও composition analyze করা যায়।

### Docker Desktop-এ কি Docker Scout CLI default-ভাবে থাকে?

হ্যাঁ, Docker Scout CLI plugin Docker Desktop-এর সাথে pre-installed অবস্থায় আসে।

### Docker Desktop ছাড়া শুধু Linux-এ Docker Engine চালালে কী হবে?

তখন Docker Scout pre-installed থাকে না, তবে standalone binary হিসেবে আলাদা করে install করা যায়।

### Docker Scout SBOM কীভাবে ব্যবহার করে?

কোনো image analyze করার সময় Docker Scout হয় image-এর সাথে attach করা (attestation হিসেবে) SBOM ব্যবহার করে, অথবা image-এর content analyze করে নিজে একটা SBOM তৈরি করে নেয়। তারপর সেই SBOM-কে advisory database-এর সাথে cross-reference করে দেখা হয় কোনো component-এ known vulnerability আছে কিনা।

---

## সংক্ষেপে

| Concept | মূল কথা |
|---|---|
| **S3C** | পুরো software delivery process (dev থেকে deployment পর্যন্ত) জুড়ে security বজায় রাখার practice |
| **SBOM** | image-এর ভিতরের সব component/dependency-র লিস্ট — vulnerability ও license risk ধরতে সাহায্য করে |
| **Attestation** | image কীভাবে বানানো হয়েছে (Provenance) ও তাতে কী আছে (SBOM) — এই metadata build-time-এ BuildKit তৈরি করে |
| **Remediation** | policy evaluation-এর ভিত্তিতে base image update বা missing attestation যোগ করার মতো তৈরি করা actionable fix |
| **Docker Scout-এর মূল পার্থক্য** | scheduled scan না — event-driven, real-time vulnerability database, পুরো supply chain জুড়ে coverage |
