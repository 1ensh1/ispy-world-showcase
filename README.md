# iSpy World — Bilingual Literacy Platform for Preschoolers

**iSpy World** helps preschoolers learn English and Filipino words by pointing a phone camera at everyday objects. A custom-trained YOLO model recognizes the object, and the app turns it into a bilingual vocabulary lesson. A web portal lets administrators, teachers and parents manage classes, vocabulary and each child's progress.

Capstone project, FEU Institute of Technology (BSIT – Web and Mobile Applications), built for **Future Minds Academy**, Mandaluyong City.

> This is a **showcase repository**: screenshots, demo videos and model results. The source code lives in private team repositories.

<p>
  <img alt="Model" src="https://img.shields.io/badge/YOLO11n-87.1%25%20mAP%4050-1f6feb">
  <img alt="Classes" src="https://img.shields.io/badge/object%20classes-99-2ea043">
  <img alt="Dataset" src="https://img.shields.io/badge/annotated%20images-~43k-8250df">
  <img alt="Web" src="https://img.shields.io/badge/Laravel-12-ff2d20">
  <img alt="Mobile" src="https://img.shields.io/badge/React%20Native-Expo-000000">
  <img alt="API" src="https://img.shields.io/badge/FastAPI-Python-009688">
</p>

## Demo videos

| Mobile app | Web portal |
| --- | --- |
| [▶ Watch the mobile demo](https://drive.google.com/file/d/130kFuTqRmCryOpMieI_5F5AbhKYQRlFH/view?usp=sharing) | [▶ Watch the web portal walkthrough](https://drive.google.com/file/d/1v3WyzRhiWbBoVrAqVYYy4KsTPmdBsmZ6/view?usp=sharing) |

## How it works

```mermaid
flowchart LR
    CAM["📱 Mobile app<br/>React Native (Expo)"] -- "photo" --> API["Inference API<br/>FastAPI + YOLO11n<br/>99 classes"]
    API -- "detected object" --> CAM
    API -- "detection log" --> SB[("Supabase")]
    CAM <-. "vocabulary & progress" .-> WEB["🖥️ Web portal<br/>Laravel 12<br/>Admin · Teacher · Parent"]
    WEB --> DB[("MySQL<br/>25 tables")]
    TTS["Google Cloud<br/>Text-to-Speech"] -- "word audio" --> WEB
```

1. The child points the camera at an object. The app sends the photo to the FastAPI server.
2. The YOLO11n model finds the object (for example, *apple*). Every detection is logged to Supabase.
3. The app teaches the word in English and Filipino (*apple → mansanas*), with audio.
4. Teachers and parents follow the child's progress on the web portal.

## The object-detection model

| Metric (validation set) | Score |
| --- | --- |
| mAP@50 | **87.1%** |
| mAP@50-95 | **69.2%** |
| Best F1 | **0.83** (at confidence 0.30) |
| Precision / recall (final epoch) | ~0.85 / ~0.83 |

| Setting | Value |
| --- | --- |
| Model | YOLO11n (Ultralytics), fine-tuned from pretrained weights |
| Classes | 99 everyday objects children see at home and in school |
| Dataset | ~43,000 images, annotated in Roboflow (SAM-assisted polygon labeling) |
| Training | 100 epochs · 640 px · batch 16 · mixed precision |
| Hardware | NVIDIA RTX 5070 Ti (local) |

We chose the **nano** model so detection stays fast enough for a live camera app.

<table>
  <tr>
    <td width="50%"><img src="assets/model/pr-curve.png" alt="Precision-recall curve, 0.871 mAP@0.5 across all classes"><br><sub>Precision–recall curve: 0.871 mAP@0.5 across all classes</sub></td>
    <td width="50%"><img src="assets/model/f1-curve.png" alt="F1-confidence curve, peak 0.83 at 0.296"><br><sub>F1–confidence curve: peak F1 0.83 at confidence 0.30</sub></td>
  </tr>
  <tr>
    <td colspan="2"><img src="assets/model/training-curves.png" alt="Training and validation loss and metrics over 100 epochs"><br><sub>Losses fall steadily and mAP climbs for all 100 epochs, with no sign of overfitting in the validation losses.</sub></td>
  </tr>
  <tr>
    <td colspan="2"><img src="assets/model/validation-samples.jpg" alt="Labeled validation images: guitars, deer, bananas, umbrellas, hangers"><br><sub>Validation images with their object labels</sub></td>
  </tr>
</table>

<details>
<summary>Normalized confusion matrix (99 classes)</summary>
<br>
<img src="assets/model/confusion-matrix-normalized.png" alt="Normalized confusion matrix with a strong diagonal across 99 classes">
<br><sub>A strong diagonal means most objects are classified correctly. The bottom row shows the objects most often missed as background.</sub>
</details>

## Mobile app

Children sign in through their teacher's 6-digit classroom PIN or a parent account, then scan objects and play short games built from the words they found.

<table>
  <tr>
    <td><img src="assets/mobile/welcome.png" alt="Welcome screen"></td>
    <td><img src="assets/mobile/home.png" alt="Home screen"></td>
    <td><img src="assets/mobile/learning-activities.png" alt="Learning activities"></td>
    <td><img src="assets/mobile/gallery.png" alt="Gallery of scanned objects"></td>
  </tr>
  <tr>
    <td><sub>Welcome: teacher or parent login</sub></td>
    <td><sub>Home: scan a new object</sub></td>
    <td><sub>Activities: Identification, Match the Pairs, Build a Sentence, Spell It</sub></td>
    <td><sub>Gallery: objects the child found, with model confidence</sub></td>
  </tr>
  <tr>
    <td><img src="assets/mobile/my-progress.png" alt="My progress"></td>
    <td><img src="assets/mobile/activity-performance.png" alt="Activity performance"></td>
    <td><img src="assets/mobile/teacher-login.png" alt="Teacher PIN login"></td>
    <td><img src="assets/mobile/more.png" alt="More: profile and sync"></td>
  </tr>
  <tr>
    <td><sub>Progress: learning score and mastered words</sub></td>
    <td><sub>Performance per activity</sub></td>
    <td><sub>Classroom PIN entry</sub></td>
    <td><sub>Profile, offline sync center and app lock</sub></td>
  </tr>
</table>

## Web portal

Three roles, each with its own portal and permissions.

| Role | What they can do |
| --- | --- |
| **Administrator** | Manage user accounts and classes, the bilingual vocabulary library (images, categories, audio), vocabulary suggestions, site content, consultations, support tickets, access control, activity logs and reports |
| **Teacher** | Enroll students, issue classroom PINs, track progress and spelling, set milestones and word sets, propose new words, publish consultation slots, message parents, file support tickets |
| **Parent** | Follow their child's words and pronunciation, manage the child's mobile-app password, message the teacher, book consultations |

<table>
  <tr>
    <td width="50%"><img src="assets/web/admin-dashboard.png" alt="Administrator dashboard"><br><sub>Administrator dashboard</sub></td>
    <td width="50%"><img src="assets/web/vocabulary-library.png" alt="Vocabulary library"><br><sub>Bilingual vocabulary library (Filipino ↔ English, image, category, audio)</sub></td>
  </tr>
  <tr>
    <td><img src="assets/web/user-management.png" alt="User management"><br><sub>User management by role</sub></td>
    <td><img src="assets/web/teacher-dashboard.png" alt="Teacher dashboard"><br><sub>Teacher dashboard with classroom PIN</sub></td>
  </tr>
  <tr>
    <td><img src="assets/web/consultation-availability.png" alt="Consultation availability"><br><sub>Teacher consultation scheduling</sub></td>
    <td><img src="assets/web/support-tickets.png" alt="Support tickets"><br><sub>Support tickets</sub></td>
  </tr>
  <tr>
    <td><img src="assets/web/parent-dashboard.png" alt="Parent dashboard"><br><sub>Parent dashboard: a child's progress</sub></td>
    <td><img src="assets/web/messaging.png" alt="Parent-teacher messaging"><br><sub>Real-time parent–teacher messaging</sub></td>
  </tr>
  <tr>
    <td colspan="2"><img src="assets/web/login.png" alt="Login page"><br><sub>Login</sub></td>
  </tr>
</table>

## Tech stack

| Part | Technology |
| --- | --- |
| Mobile | React Native (Expo) |
| Machine learning | Ultralytics YOLO11n, Roboflow, PyTorch (CUDA) |
| Inference API | Python, FastAPI |
| Web portal | Laravel 12, PHP 8.2, Blade, vanilla JavaScript |
| Data | MySQL (25 tables), Supabase |
| Audio | Google Cloud Text-to-Speech |
| Workflow | Git with protected `main`, `develop` and `feature/*` branches; every change merged through a pull request |

## My role — Rome Gabriel Capuno

- **Model:** trained the YOLO11n detector on my RTX 5070 Ti, reaching 87.1% mAP@50 across 99 classes. The dataset of ~43,000 images was annotated by the whole team in Roboflow.
- **Inference API:** wrote the FastAPI server that receives photos from the app, runs the model and logs each detection to Supabase.
- **Web portal:** built the User Management, Vocabulary Library, Teacher Portal and Parent Portal modules, plus real-time parent–teacher messaging, with role-based access control.
- **Database:** led a full normalization review that resolved 15 ERD issues and produced the 25-table MySQL schema.
- **Team lead:** set up the branching workflow and merged 15 pull requests in 7 weeks.

## Team

| Name | Role | GitHub |
| --- | --- | --- |
| Rome Gabriel Capuno | Team lead · ML · backend · web | [@1ensh1](https://github.com/1ensh1) |


Adviser: John Benedic R. Enriquez, FEU Institute of Technology

---

<sub>Screenshots use test accounts and demo data. Names, emails and photos of real people are blurred. © 2026 the iSpy World team. All rights reserved.</sub>
