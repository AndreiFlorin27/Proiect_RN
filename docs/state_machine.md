stateDiagram-v2
    direction LR
    
    %% Stari Iniziale si de Baza
    state IDLE
    
    state FLUX_DATE {
        direction LR
        
        state S1 : 1. ACQUIRE_SENSORS<br>(ROS Topics: Image, LiDAR)
        state S2 : 2. RN_INFERENCE_2D<br>(YOLOv8 Detecție)
        state S3 : 3. FUSION_3D<br>(Localizare X, Y, Z)
        
        S3 --> DECISION : Poziție 3D Calculată
        
        state DECISION {
            state S4 : 4. ANALIZĂ RISC
            state S_UPDATE : 5. ACTUALIZARE TRAIECTORIE
            state S_CRITIC : 5. GENERARE COMANDĂ CRITICĂ (STOP)
            state S6 : 6. LOG_DISPLAY (UI Update)
            
            S4 --> S_CRITIC : Distanță <= Prag Critic
            S4 --> S_UPDATE : Distanță > Prag Critic (Safe)
            
            S_CRITIC --> S6 : Comandă STOP Generată
            S_UPDATE --> S6 : Poziție 3D Transmisă
        }
        
        S3 --> S4
        S6 --> S1 : Continuă Monitorizarea
        
        %% Tranziții principale (Bucla Continuă)
        S1 --> S2 : Cadru Sincronizat Primit
        S2 --> S3 : Detection2DArray Publicat
        
    }
    
    IDLE --> S1 : Sistem Lansat (ros2 launch)
    
    %% Tranzitii de Eroare
    S1 --> ERROR : Senzor Lipsă / Eroare ROS
    S2 --> ERROR : Eșec Inferință RN
    S3 --> ERROR : Date LiDAR Lipsă în BBox
    
    ERROR --> IDLE : Reset / Reîncercare
    
    S6 --> STOP : Comandă de Oprire