graph TD
    %% Define Actors
    actor Administrator
    actor Mobile_App as "Aplikacja Mobilna"

    %% Define Use Cases (UC)
    %% Group Use Cases within the system boundary
    subgraph System MDS
        UC001("UC-001 Rejestracja Urządzenia")
        UC002("UC-002 Cykliczna Synchronizacja Danych")
        UC003("UC-003 Wymuszenie Synchronizacji")
        UC004("UC-004 Przeglądanie Danych Diagnostycznych")
        UC005("UC-005 Zarządzanie Użytkownikami/Urządzeniami")
    end

    %% Define Relationships (Associations)
    Mobile_App -- (UC001)
    Mobile_App -- (UC002)

    Administrator -- (UC003)
    Administrator -- (UC004)
    Administrator -- (UC005)

    %% Define Includes/Extends
    FR_Collect_Diag("Zbieranie danych diagnostycznych")
    FR_Collect_SMS("Zbieranie SMS/Połączeń")
    FR_Send_Data("Wysyłka danych do API")

    UC002 ..> FR_Collect_Diag : <<include>>
    UC002 ..> FR_Collect_SMS : <<include>>
    UC002 ..> FR_Send_Data : <<include>>

    UC003 .> UC002 : <<include>>
