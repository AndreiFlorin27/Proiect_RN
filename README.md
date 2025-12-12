# 📘 README – Etapa 3: Analiza și Pregătirea Setului de Date pentru Rețele Neuronale

**Disciplina:** Rețele Neuronale  
**Instituție:** UNSTPB
**Student:** Pufu Andrei-Florin
**Data:** 12/12/2025

---

## Introducere

Acest document descrie activitățile realizate în Etapa 3, în care se analizează și se preprocesează setul de date necesar proiectului „Rețele Neuronale". Scopul etapei este pregătirea corectă a datelor pentru instruirea modelului RN, respectând bunele practici privind calitatea, consistența și reproductibilitatea datelor.

---

##  1. Structura Repository-ului Github (versiunea Etapei 3)

```
project-name/
├── README.md
├── docs/
│   └── datasets/          # descriere seturi de date, surse, diagrame
├── data/
│   ├── raw/               # date brute
│   ├── processed/         # date curățate și transformate
│   ├── train/             # set de instruire
│   ├── validation/        # set de validare
│   └── test/              # set de testare
├── src/
│   ├── preprocessing/     # funcții pentru preprocesare
│   ├── data_acquisition/  # generare / achiziție date (dacă există)
│   └── neural_network/    # implementarea RN (în etapa următoare)
├── config/                # fișiere de configurare
└── requirements.txt       # dependențe Python (dacă aplicabil)
```

---

##  2. Descrierea Setului de Date

Setul de date este destinat detectiei preliminare folosind YOLO urmata de antrenarea propriei retele neuronale pentru sarcina de actualizare a treiectoriei in functie de pozitia oamenilor.

### 2.1 Sursa datelor

* **Origine:** Senzori reali (Lidar, Cameră, Odometrie, IMU, Baterie) în format sincronizat.
* **Modul de achiziție:** Înregistrare ROS 2 Bag (topicuri multiple, capturate Off-Board).
* **Perioada / condițiile colectării:** Decembrie 2025

### 2.2 Caracteristicile dataset-ului

* **Număr total de observații:** 300 cadre (pentru imagini) / ~2 ore de înregistrare.
* **Număr de caracteristici (features):** 11 Topicuri ROS 2 (pe lângă TF).
* **Tipuri de date:** Imagini, LaserScan, Odometrie, Stare Baterie, IMU.
* **Format fișiere:** Data Base

### 2.3 Descrierea fiecărei caracteristici

| **Caracteristică (Topic)** | **Tip** | **Unitate** | **Descriere** | **Domeniu valori** |
|---------------------------|---------|-------------|---------------|--------------------|
| /camera/image_raw/compressed | `sensor_msgs/Image` | Pixeli | Fluxul video al camerei (sursa principală YOLO). | 0–255 |
| /scan | `sensor_msgs/LaserScan` | m / rad | Date de distanță de la senzorul LiDAR. | 0.1 – MaxRange |
| /tf | `tf2_msgs/TFMessage` | Diverse | Transformări dinamice (mișcare). | Variază |
| **/tf_static** | `tf2_msgs/TFMessage` | Diverse | **Calibrarea statică** (Camera-LiDAR). Date extrase. | Fixed (R/T) |
| /diff_drive_controller/odom | `nav_msgs/Odometry` | m / rad | Odometria robotului (poziție estimată local). | Variază |
| /amcl_pose | `geometry_msgs/Pose...`| m / rad | Poziția robotului estimată global. | Variază |
| /imu/data | `sensor_msgs/Imu` | rad/s, m/s² | Date de la unitatea de măsură inerțială. | Variază |
| /rear_battery_state | `sensor_msgs/BatteryState`| V / % | Starea bateriei din spate. | 0–100% |
| /front_battery_state | `sensor_msgs/BatteryState`| V / % | Starea bateriei din față. | 0–100% |
| /cmd_vel | `geometry_msgs/Twist` | m/s, rad/s | Comenzi de viteză. | -Max – +Max |
| /joint_states | `sensor_msgs/JointState` | rad | Starea roților. | Variază |

**Fișier recomandat:**  `data/README.md`

---

##  3. Analiza Exploratorie a Datelor (EDA) – Sintetic

### 3.1 Statistici descriptive aplicate

* **Medie, mediană, deviație standard**
* **Min–max și quartile**
* **Distribuții pe caracteristici** (histograme)
* **Identificarea outlierilor** (IQR / percentile)

### 3.2 Analiza calității datelor

* **Detectarea valorilor lipsă** (% pe coloană)
* **Detectarea valorilor inconsistente sau eronate**
* **Identificarea caracteristicilor redundante sau puternic corelate**

### 3.3 Probleme identificate

* [exemplu] Feature X are 8% valori lipsă
* [exemplu] Distribuția feature Y este puternic neuniformă
* [exemplu] Variabilitate ridicată în clase (class imbalance)

---

##  4. Preprocesarea Datelor

### 4.1 Curățarea datelor

* **Eliminare duplicatelor**
* **Tratarea valorilor lipsă:**
  * Feature A: imputare cu mediană
  * Feature B: eliminare (30% valori lipsă)
* **Tratarea outlierilor:** IQR / limitare percentile

### 4.2 Transformarea caracteristicilor

* **Normalizare:** Min–Max / Standardizare
* **Encoding pentru variabile categoriale**
* **Ajustarea dezechilibrului de clasă** (dacă este cazul)

### 4.3 Structurarea seturilor de date

**Împărțire recomandată:**
* 70–80% – train
* 10–15% – validation
* 10–15% – test

**Principii respectate:**
* Stratificare pentru clasificare
* Fără scurgere de informație (data leakage)
* Statistici calculate DOAR pe train și aplicate pe celelalte seturi

### 4.4 Salvarea rezultatelor preprocesării

* Date preprocesate în `data/processed/`
* Seturi train/val/test în foldere dedicate
* Parametrii de preprocesare în `config/preprocessing_config.*` (opțional)

---

##  5. Fișiere Generate în Această Etapă

* `data/raw/` – date brute
* `data/processed/` – date curățate & transformate
* `data/train/`, `data/validation/`, `data/test/` – seturi finale
* `src/preprocessing/` – codul de preprocesare
* `data/README.md` – descrierea dataset-ului

---

##  6. Stare Etapă (de completat de student)

- [ ] Structură repository configurată
- [ ] Dataset analizat (EDA realizată)
- [ ] Date preprocesate
- [ ] Seturi train/val/test generate
- [ ] Documentație actualizată în README + `data/README.md`

---
