markdown
# Diagram Przypadków Użycia MDS

Poniżej znajduje się wizualizacja głównych przypadków użycia systemu:

```mermaid
graph TD
    %% Define Actors
    Administrator
    Mobile_App

    %% Define Use Cases (UC)
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
    'FR_Collect_Diag' ..> UC002 : <<include>>
    'FR_Collect_SMS' ..> UC002 : <<include>>
    'FR_Send_Data' ..> UC002 : <<include>>
    UC003 .> UC002 : <<include>>
