mermaid
```mermaid

sequenceDiagram
    participant B_Admin as Panel Admina (Backend UI)
    participant B_API_GW as API Gateway (Backend)
    participant B_ASYNC_SVC as Async Service (Backend)
    participant M_SERVICE as Aplikacja Mobilna (Usługa w tle)
    participant M_DB as Lokalna Baza Danych (Room)
    participant B_INGEST_SVC as Ingestion Service (Backend)
    participant B_DB as Główna BD (PostgreSQL)


    activate B_Admin
    B_Admin -->> B_API_GW: 1. Żądanie: "Wymuś wysyłkę SMSów z urządzenia X"
    activate B_API_GW
    B_API_GW -->> B_ASYNC_SVC: 2. Przekazanie żądania do kolejki zadań
    deactivate B_API_GW

    activate B_ASYNC_SVC
    B_ASYNC_SVC -->> M_SERVICE: 3. Wysyłka PUSH (Firebase Cloud Messaging - FCM)
    deactivate B_ASYNC_SVC

    activate M_SERVICE
    M_SERVICE -->> M_SERVICE: 4. Aplikacja odbiera PUSH i inicjuje zadanie (WorkManager)

    M_SERVICE -->> M_DB: 5. Zapytanie: "Pobierz wszystkie SMSy od ostatniej synchronizacji"
    activate M_DB
    M_DB -->> M_SERVICE: 6. Zwrócenie listy obiektów SMS (POJO)
    deactivate M_DB

    M_SERVICE -->> M_SERVICE: 7. Serializacja danych do formatu JSON i kompresja

    M_SERVICE -->> B_INGEST_SVC: 8. Żądanie POST /api/sms/upload (JSON, HTTPS)
    activate B_INGEST_SVC

    B_INGEST_SVC -->> B_INGEST_SVC: 9. Walidacja danych i klucza API

    B_INGEST_SVC -->> B_DB: 10. Zapis/Aktualizacja rekordów SMS w bazie głównej
    activate B_DB
    B_DB -->> B_INGEST_SVC: 11. Potwierdzenie zapisu
    deactivate B_DB

    B_INGEST_SVC -->> M_SERVICE: 12. Odpowiedź HTTP 200 OK
    deactivate B_INGEST_SVC

    M_SERVICE -->> M_DB: 13. Aktualizacja: "Oznacz SMSy jako zsynchronizowane"
    activate M_DB
    M_DB -->> M_SERVICE: 14. Potwierdzenie
    deactivate M_DB

    M_SERVICE -->> M_SERVICE: 15. Zakończenie zadania (WorkManager success)
    deactivate M_SERVICE

    B_INGEST_SVC -->> B_Admin: 16. (Opcjonalnie) Powiadomienie admina o zakończeniu synchronizacji
    deactivate B_Admin

