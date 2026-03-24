<div align="center">

# CareChain
### Unified Emergency Medical Identity System — India

[![Firebase](https://img.shields.io/badge/Firebase-10.12.2-orange?logo=firebase)](https://firebase.google.com)
[![Firestore](https://img.shields.io/badge/Firestore-Cloud-blue?logo=google-cloud)](https://firebase.google.com/products/firestore)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![Domain](https://img.shields.io/badge/Domain-HealthTech-red)](https://github.com)
[![Status](https://img.shields.io/badge/Status-Active-brightgreen)](https://github.com)

> **One encrypted identity. Your complete medical profile — accessible in seconds, only when it matters most.**

<img width="1913" height="903" alt="image" src="https://github.com/user-attachments/assets/ae87455f-b0bf-46ad-ab5f-40dd746cebba" />



</div>

---
## 🚨 The Problem

India records **1.5 lakh+ road accident deaths annually**. A significant percentage of victims arrive at hospitals **unconscious and unidentified** — with no medical history, no allergy information, and no emergency contact details.

### The Gap

```
                                              Patient arrives unconscious
                                                          ↓
                                              Hospital has NO information
                                                          ↓
                                            Doctors treat blindly — risk of:
                                      • Fatal drug reactions (unknown allergies)
                                    • Wrong blood transfusion (unknown blood group)
                                   • Missed critical conditions (epilepsy, diabetes)
                                                          ↓
                                             Family unreachable for hours
                                                          ↓
                                              Critical treatment delayed
```

**Existing systems like ABHA (Ayushman Bharat Health Account) require the patient to be conscious and cooperative to share their records.** There is no verified mechanism for emergency access when the patient cannot consent.

Studies show unidentified patients in Indian emergency rooms had a **median identification time of 76 hours** from admission — meaning hospitals wait up to three days to get a patient's name.

### Who is affected

| Stakeholder | Problem |
|---|---|
| **Patients** | Cannot advocate for themselves when unconscious |
| **Hospitals** | Lack life-saving information at the critical moment |
| **Police / Responders** | No verified way to connect an unidentified person to their medical profile |
| **Families** | Not notified until hours later |

---

## 💡 The Solution

**CareChain** is a tri-party verified emergency medical identity platform — the only system that works even when the patient is **unconscious**.

<!-- 📸 INSERT IMAGE: System flow diagram -->
<!-- Recommended: A clean diagram showing Patient → Hospital → Police → Access flow -->
<!-- File suggestion: /assets/diagrams/system-flow.png -->

### How it works in 60 seconds

1. **Patient registers** — gets a unique `CareChain ID` (e.g. `CC-4421-KA`) linked to their encrypted medical profile
2. **Emergency occurs** — hospital enters/scans the patient's CareChain ID
3. **Hospital submits access request** — tagged as Emergency or General Checkup
4. **Police station verifies** — authenticated via shared station credentials, approves or rejects
5. **Records unlocked** — hospital views full medical profile only after police approval
6. **SMS sent** — emergency contact notified automatically
7. **Audit log created** — every access event permanently recorded

### Why it's different

Unlike ABHA, unlike blockchain-based EMR systems, unlike traditional hospital records — CareChain is the **only system** that:
- Works when the patient is **unconscious**
- Requires **dual verification** (hospital + police) for emergency access
- Gives patients **full control** over their session lifecycle
- Works with **existing infrastructure** — no blockchain, no special hospital hardware

---

## ✨ Key Features

**Patient**
- Unique CareChain ID with emergency overview — blood group, allergies, conditions
- Full profile management with blood group selector (18 options including rare groups)
- View, verify, or dispute hospital-added records and prescriptions
- Approve / deny / end hospital access sessions

**Hospital**
- Patient lookup by name, father, mother, location, or CareChain ID
- Emergency mode (police verification) and General Checkup mode (patient approval)
- Add medical records — diagnosis, treatment, operative report, discharge summary
- Add prescriptions — multi-drug with dosage, frequency, and duration
- Cross-hospital record view on general approval

**Police Station**
- Shared station login — one email and password per station
- Approve or reject hospital emergency requests in real time
- Search patients for emergency identification
- Full patient record access for on-ground response

---

## 🏗 System Architecture

```
┌─────────────────────────────────────────────────────────┐
│                     FRONTEND                             │
│         HTML5 · CSS3 · Vanilla JS (ES6 Modules)         │
│              Role-based UI routing                       │
└──────────────────┬──────────────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────────────┐
│                  FIREBASE AUTH                           │
│         Email/Password Authentication                    │
│         Role stored in Firestore users/{uid}             │
└──────────────────┬──────────────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────────────┐
│               CLOUD FIRESTORE                            │
│         asia-south1 (Mumbai) — India data residency      │
│                                                          │
│   users/        patients/      requests/    auditLog/    │
│   hospitals/    policeStations/                          │
└──────────────────┬──────────────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────────────┐
│             FIRESTORE SECURITY RULES                     │
│   Police → read all patients                             │
│   Hospital → read only approved patient documents        │
│   Patient → read/write own profile only                  │
└─────────────────────────────────────────────────────────┘
```

<!-- 📸 INSERT IMAGE: Architecture diagram -->
<!-- Recommended: A visual layered diagram of the above architecture -->
<!-- File suggestion: /assets/diagrams/architecture.png -->

---

## 🛠 Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| **Frontend** | HTML5, CSS3, JavaScript ES6 | Single-file web application |
| **Authentication** | Firebase Auth 10.12.2 | Email/password, role-based sessions |
| **Database** | Cloud Firestore | Real-time NoSQL, encrypted at rest |
| **Hosting** | Firebase Hosting | Global CDN, HTTPS by default |
| **Real-time** | Firestore `onSnapshot` | Live request status updates |
| **Region** | `asia-south1` (Mumbai) | India data residency — DISHA compliant |


---

## 🗄 Database Schema

```
Firestore Root
│
├── users/{firebaseUid}
│   ├── firebaseUid, careChainId, name, email, role
│   ├── father, mother, dob, location, aadhar, contact
│   └── createdAt, status
│
├── patients/{careChainId}
│   ├── uid, name, blood, dob, allergies[], conditions[]
│   ├── medications[], emergency_contact, insurance
│   ├── father, mother, location, address
│   └── [sub] hospitalRecords/{recordId}
│           ├── date, hospital, dept, doctor, reason
│           ├── treatment, ops, surgeon, anaes, findings
│           ├── discharge, addedBy, visibleTo[]
│           └── patientVerified, patientVerifyStatus
│
├── prescriptions/{prescriptionId}
│   ├── patientId, hospital, doctor, date, diagnosis
│   ├── meds[{name, dose, freq, dur, notes}]
│   ├── instructions, reviewDate
│   └── patientVerified, patientVerifyStatus
│
├── requests/{requestId}
│   ├── type (emergency | general)
│   ├── hospitalId, hospitalName
│   ├── patientMedId, patientName
│   ├── status (pending | patient_pending | patient_approved |
│   │          approved | rejected | hospital_closed | patient_closed)
│   ├── policeApproved, timestamp
│   └── officerStation
│
├── policeStations/{stationId}
│   ├── stationName, email, password (hashed in prod)
│   └── location, district
│
└── auditLog/{logId}
    ├── action, actorId, actorRole
    ├── patientMedId, requestId
    └── timestamp — append-only, never deleted
```

---

## 🔐 Security Model

### Three-party verification

```
Emergency Access:
  Hospital Request → Police Approval → Records Unlocked

General Checkup:
  Hospital Request → Patient Approval → Records Unlocked
```

### Access control rules

```javascript
// Firestore Security Rules (simplified)

// Patients — read/write own profile only
match /patients/{medId} {
  allow read: if isPolice()
    || (isHospital() && hasApprovedRequest(medId))
    || isOwner(medId);
  allow write: if isOwner(medId)
    || (isHospital() && hasApprovedRequest(medId));
}

// Requests — hospital creates, police updates
match /requests/{reqId} {
  allow create: if isHospital();
  allow update: if isPolice() || isPatient();
  allow read:   if isAuthenticated();
}

// Audit log — append only, never delete
match /auditLog/{logId} {
  allow create: if isAuthenticated();
  allow read:   if isPolice();
  allow update, delete: if false;
}
```

### Data protection
- All Firestore data encrypted at rest by Google Cloud (AES-256)
- Data stored in `asia-south1` (Mumbai) — India data residency
- HTTPS enforced via Firebase Hosting
- No single party can access patient data unilaterally
- Every access event logged in immutable audit trail

---
## 🔑 Demo Credentials
### 🧑‍⚕️ Patients



| Email | Password |
|:---|:---|
| `arjun@email.com` | `arjun123` |
| `priya@email.com` | `priya123` |



<details>
<summary>View all patient accounts</summary>


| Email | Password |
|:---|:---|
| `rahul@email.com` | `rahul123` |
| `kavya@email.com` | `kavya123` |
| `rafiq@email.com` | `rafiq123` |


</details>

---

### 🏥 Hospitals

| Email | Password |
|:---|:---|
| `apollo@hospital.com` | `apollo123` |
| `miot@hospital.com` | `miot123` |

<details>
<summary>View all hospital accounts</summary>


| Email | Password |
|:---|:---|
| `kovai@hospital.com` | `kovai123` |
| `city@clinic.com` | `city123` |
| `yashoda@hospital.com` | `yashoda123` |

</details>

---

### 🛡️ Police Stations

| Email | Password |
|:---|:---|
| `b12@tnpolice.gov.in` | `b12123` |
| `c4@tnpolice.gov.in` | `c41234` |



<details>
<summary>View all station accounts</summary>

| Email | Password |
|:---|:---|
| `h3@tspolice.gov.in` | `h31234` |

</details>

---

## 📁 Project Structure

```
carechain/
│
├── index_f.html          # Main application — all screens, modals, auth UI
├── style.css             # Complete styling — dark theme, components, responsive
├── script.js             # Application logic — 1,200+ lines, all role dashboards
├── firebase.js           # Firebase SDK, auth helpers, Firestore CRUD operations
├── upload.js             # File upload handling for medical documents
└── README.md             # This file
```

### Key functions in `script.js`
  
| Function | Description |
|---|---|
| `doSignIn()` | Firebase Auth login with role detection |
| `doSignUp()` | User registration with CareChain ID generation |
| `renderMain()` | Route renderer — loads correct dashboard per role |
| `rPtOv()` | Patient emergency overview |
| `renderPtFull()` | Patient full profile editor |
| `renderPtVisits()` | Hospital visit records with verify/dispute |
| `rHospLookup()` | Hospital patient search with Emergency/General tabs |
| `viewRec()` | Full patient record — full-page render, not modal |
| `saveHR()` | Add hospital record with patient verification status |
| `saveRx()` | Add prescription with patient verification status |
| `hospCloseRec()` | Close hospital session — cross-notifies patient |
| `ptCloseRec()` | Patient ends session — cross-notifies hospital |
| `togglePw()` | Show/hide password toggle |


---

## 👥 User Flows

### Emergency Flow

1. Patient arrives unconscious at hospital
2. Hospital logs in → Patient Lookup → searches by name/father/location
3. Hospital sends Emergency Request for patient's CareChain ID
4. Police station logs in → Pending Requests → Approves
5. Hospital → My Requests → "Open Full Record" unlocks
6. Full medical profile visible: blood group, allergies, conditions, meds
7. SMS automatically sent to emergency contact
8. Every step logged in audit trail

### General Checkup Flow

1. Hospital logs in → Patient Lookup → General Checkup tab
2. Sends general checkup request to patient
3. Patient logs in → My Approvals → Approves request
4. Hospital → My Requests → "Open Full Record"
5. ALL records from ALL hospitals visible (cross-hospital view)
6. Hospital adds new medical record / prescription
7. Patient logs in → Hospital Visits / Prescriptions → Verify or Dispute
8. Hospital can see patient verification status per entry
9. After visit: either party can close the session
10. Closed session notifies the other party
11. Hospital can send new request to reopen


### Patient Dashboard

<img width="1919" height="907" alt="image" src="https://github.com/user-attachments/assets/5cd5a7ff-5eff-47db-a96f-be00648a13f8" />


### Hospital Dashboard


<img width="1919" height="904" alt="image" src="https://github.com/user-attachments/assets/7b584a02-ae50-4683-9c86-e2d5b52b62e1" />


### Police Dashboard

<img width="1919" height="903" alt="Screenshot 2026-03-24 132116" src="https://github.com/user-attachments/assets/07042d29-97ab-4134-a508-e6ddd1023ba9" />



---

## 🌍 UN SDG Alignment

| SDG | Goal | How CareChain contributes |
|---|---|---|
| **SDG 3** | Good Health and Well-being | Reduces preventable emergency deaths by ensuring critical medical information is available at point of care |
| **SDG 16** | Peace, Justice and Strong Institutions | Integrates law enforcement into emergency healthcare with a formal, auditable verification process |
| **SDG 9** | Industry, Innovation and Infrastructure | Cloud-based, low-infrastructure solution deployable across India without requiring hospitals to upgrade existing systems |
| **SDG 10** | Reduced Inequalities | Works for any patient regardless of literacy or ability to carry physical documents |

---

## 📊 Existing Systems Comparison

<div align="center">

| Feature | ABHA | Blockchain EMR | MedRec | **CareChain** |
|:---|:---:|:---:|:---:|:---:|
| India-specific | ✅ | ❌ | ❌ | ✅ |
| Works for unconscious patients | ❌ | ✅ | ❌ | ✅ |
| Police / authority verification | ❌ | ❌ | ❌ | ✅ |
| Patient controls session lifecycle | ❌ | ❌ | ❌ | ✅ |
| Hospital record verified by patient | ❌ | ❌ | ❌ | ✅ |
| Emergency + general checkup modes | ❌ | ❌ | ❌ | ✅ |
| No blockchain dependency | ✅ | ❌ | ❌ | ✅ |
| Cross-hospital record visibility | ⚠️ | ❌ | ✅ | ✅ |
| Real-time approval notifications | ❌ | ❌ | ❌ | ✅ |
| Immutable audit log | ⚠️ | ✅ | ❌ | ✅ |

✅ Supported &nbsp;&nbsp;&nbsp; ❌ Not supported &nbsp;&nbsp;&nbsp; ⚠️ Partial

</div>

---

## 🔭 Future Scope
> 🔐 **Aadhaar OTP Verification** — Integrate UIDAI sandbox API for government-backed identity verification during patient signup, replacing manual Aadhaar entry with real-time OTP confirmation.

> 📲 **SMS & WhatsApp Alerts** — Emergency notifications to family members the moment police approval is granted, with multi-language support across Tamil, Hindi, and Telugu.

> 📄 **Medical Document Upload** — Attach scan reports, X-rays, ECGs, and discharge PDFs directly to patient records via Firebase Storage with per-document patient verification.

> 📊 **Admin & Analytics Dashboard** — District-wise access heatmap, average police response time, audit log viewer with filters, and DISHA compliance report generation for regulatory audits.

> 📱 **Mobile Application** — React Native cross-platform app with offline emergency card, push notifications, and camera QR scan for instant CareChain ID lookup at accident sites.

> 🏥 **ABHA Integration** — Map CareChain ID to existing ABHA numbers and pull ABDM-linked records via FHIR R4 gateway — positioning CareChain as a complementary emergency layer on India's national health stack.


---

## 🏆 Hackathon

This project was built for **HackHustle 2.0** organized by Saveetha Engineering College, affiliated to Anna University.

- **Domain:** Healthcare
- **Event:** HackHustle 2.0
- **Organizer:** The Tech Society, Saveetha Engineering College

---

## 📜 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

- National Health Authority India — ABHA/ABDM documentation
- Firebase by Google — real-time database and authentication infrastructure
- Government of India — DPDP Act 2023 and DISHA guidelines for health data compliance
- National Crime Records Bureau — road accident statistics India 2023

---

<div align="center">

**Built with ❤️ for India's emergency healthcare infrastructure**

*CareChain — One Thread, Every Heart. One Link, In Sync.
When Seconds Matter, the Chain won't Shatter.*



</div>
