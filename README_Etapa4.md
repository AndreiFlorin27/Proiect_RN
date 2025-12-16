# 📘 README – Etapa 4: Arhitectura Completă a Aplicației SIA bazată pe Rețele Neuronale

**Disciplina:** Rețele Neuronale  
**Instituție:** POLITEHNICA București – FIIR  
**Student:** Pufu Andrei-Florin  
**Link Repository GitHub:** [ADĂUGAȚI LINK-UL REPOSITORY-ULUI AICI]
**Data:** 03.12.2025  
---

## Scopul Etapei 4

Această etapă corespunde punctului **5. Dezvoltarea arhitecturii aplicației software bazată pe RN** din lista de 9 etape - slide 2 **RN Specificatii proiect.pdf**.

**Trebuie să livrați un SCHELET COMPLET și FUNCȚIONAL al întregului Sistem cu Inteligență Artificială (SIA). In acest stadiu modelul RN este doar definit și compilat (fără antrenare serioasă).**

### IMPORTANT - Ce înseamnă "schelet funcțional":

 **CE TREBUIE SĂ FUNCȚIONEZE (Realizat)**:
- Nodul RN (`yolov8_detector.py`) pornește fără erori, folosind mediul virtual corect.
- Nodul RN comunică pe topicurile ROS 2 (`/yolov8/debug_image`, `/yolov8/person_detections`).
- Pipeline-ul de bază de detecție 2D funcționează.

## Livrabile Obligatorii

### 1. Tabelul Nevoie Reală → Soluție SIA → Modul Software

| **Nevoie reală concretă** | **Cum o rezolvă SIA-ul vostru** | **Modul software responsabil** |
|:---------------------------|:--------------------------------|:--------------------------------|
| Evitarea coliziunilor cu persoanele din jur (AMR/AGV) | **Detecție Persoană (2D)** în < 50ms + **Localizare 3D** prin fuziunea datelor LiDAR-Camera. | RN + Control Module (ROS 2 Node) |
| Optimizarea traiectoriilor robotului mobil | **Predicție poziție 3D Persoană** → Reducere risc coliziune și planificare traseu alternativ. | RN + Control Module |

---

### 2. Contribuția Voastră Originală la Setul de Date – MINIM 40% din Totalul Observațiilor Finale

#### Contribuția originală la setul de date:

**Total observații finale:** [N] (cadre imagine/LiDAR sincronizate din ROS Bag)
**Observații originale:** [M] ([X]%) (Asigurați-vă că M/N $\ge 40\%$)

**Tipul contribuției:**
[ ] Date generate prin simulare fizică
[X] Date achiziționate cu senzori proprii (Date Brute ROS Bag)
[X] Etichetare/adnotare manuală (Adnotarea datelor ROS Bag)
[ ] Date sintetice prin metode avansate

**Descriere detaliată:**
Contribuția originală se bazează pe **colectarea și etichetarea manuală** a datelor.
1. **Achiziția cu Senzori Proprii:** Am înregistrat datele brute (fișierul **ROS 2 Bag**) utilizând senzorii robotului (Camera și LiDAR), asigurând o sursă de date unică. Aceste date conțin stream-urile sincronizate `/camera/image_raw` și `/scan`.
2. **Etichetarea Manuală:** Contribuția de 40% este dată de munca de **adnotare manuală** a cadrelor video extrase din ROS Bag. Această etichetare (bounding box-uri în format YOLO) este esențială pentru a transforma datele brute în setul de date *supervizat* necesar antrenării modelului YOLOv8n.

**Locația codului:**
**Locația datelor:**

**Dovezi:**
- Grafic comparativ:
- Setup experimental:
- Tabel statistici:

---

### 3. Diagrama State Machine a Întregului Sistem (OBLIGATORIE)

*Diagrama State Machine este salvată în `docs/state_machine.png`.* 

#### Justificarea State Machine-ului ales:

Am ales arhitectura de **Monitorizare Continuă și Predicție în Timp Real** pentru că proiectul nostru (Detecție Persoane și Fuziune 3D) necesită o buclă de feedback rapidă pe o platformă mobilă (ROS 2). Sistemul trebuie să răspundă la schimbările de mediu în timp real, prioritar pentru siguranță.

Stările principale includ:
1. **ACQUIRE_SENSORS:** Nodurile ROS 2 colectează pachetele de date sincronizate (Imagine + LiDAR).
2. **RN_INFERENCE_2D:** Modulul YOLOv8 rulează detecția Persoană, producând **Detecții 2D**.
3. **FUSION_3D:** Modulul de fuziune utilizează **Transformările Statice (R|T)** extrase din ROS Bag (topic `/tf_static`) pentru a calcula poziția 3D (X, Y, Z) a persoanei.
4. **TRIGGER_ALERT/EXECUTE_SEGMENT:** Decizia este luată pe baza distanței calculate.

Tranzițiile critice sunt:
- **ACQUIRE_SENSORS** → **FUSION_3D**: După ce nodul YOLOv8 publică detecțiile 2D pe topicul `/yolov8/person_detections`.
- **FUSION_3D** → **ERROR**: Dacă datele LiDAR lipsesc în zona Bounding Box-ului (nu se poate calcula poziția 3D).

Starea **ERROR** este esențială pentru a gestiona întreruperile fluxului de date ROS 2 (topicuri care nu mai publică) și a asigura o oprire controlată. Bucla de feedback asigură monitorizarea continuă.

---

### 4. Scheletul Complet al celor 3 Module Cerute la Curs (slide 7)

Toate cele 3 module trebuie să **pornească și să ruleze fără erori** la predare.

| **Modul** | **Python (exemple tehnologii)** | **Cerință minimă funcțională (la predare)** |
|:-----------|:----------------------------------|:----------------------------------------------|
| **1. Data Logging / Acquisition** | `src/data_acquisition/[numele_scriptului]` | **MUST:** Produce minimum 40% date originale adnotate în `data/generated/`. |
| **2. Neural Network Module** | `src/neural_network/yolov8_detector.py` | **MUST:** Nodul rulează stabil; Modelul YOLOv8n definit și încărcat. |
| **3. Web Service / UI** | Streamlit (`src/app/dashboard.py`) | **MUST:** Propunerea de Interfață pornește și afișează un output simulat. |

#### Detalii per modul:

#### **Modul 2: Neural Network Module**

**Funcționalități obligatorii:**
- [X] Arhitectura RN (YOLOv8n) definită și compilată.
- [X] Nodul ROS 2 (`yolov8_detector.py`) pornește fără erori.
- [ ] Modelul poate fi salvat și reîncărcat (locație: `models/yolov8n_untrained.pt`).

---

## Structura Repository-ului la Finalul Etape 4 (OBLIGATORIE)


