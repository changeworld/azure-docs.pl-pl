---
title: Obróć poświadczenia logowania w zadania usługi analiza strumienia Azure
description: W tym artykule opisano sposób aktualizacji poświadczeń danych wejściowych i wyjściowych sink w Azure Stream Analytics zadania.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: b49b4fecb6be70987e7e6736d78f224c03f719bf
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Obróć poświadczenia logowania dla danych wejściowych i wyjściowych zadania usługi analiza strumienia

Zawsze, gdy zostanie wygenerowany ponownie poświadczenia dla danych wejściowych lub wyjściowych zadania Stream Analytics, należy zaktualizować zadania z nowymi poświadczeniami. Musisz zatrzymać zadanie przed zaktualizowaniem poświadczenia, nie można zamienić poświadczenia podczas uruchamiania zadania. Aby zmniejszyć opóźnienia między zatrzymanie i ponowne uruchomienie zadania, analiza strumienia obsługuje wznawianie zadania z ostatnich danych wyjściowych. W tym temacie opisano proces obracanie poświadczenia logowania i ponownego uruchamiania zadania z nowymi poświadczeniami.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Generuj nowe poświadczenia i zaktualizuj zadania z nowymi poświadczeniami 

W tej sekcji firma Microsoft pomoże Trwa ponowne generowanie poświadczenia dla magazynu obiektów Blob, usługa Event Hubs, bazy danych SQL i Magazyn tabel. 

### <a name="blob-storagetable-storage"></a>Magazyn obiektów blob magazynu/tabeli.
1. Zaloguj się do portalu Azure > Przeglądaj konta magazynu, które są używane jako danych wejściowych/wyjściowych zadania usługi analiza strumienia.    
2. W sekcji Ustawienia Otwórz **klucze dostępu**. Między kluczami dwóch domyślnych (klucz1, key2) wybierz jedną, która nie jest używany przez zadanie i wygenerowany ponownie:  
   ![Ponowne generowanie kluczy dla konta magazynu](media/stream-analytics-login-credentials-inputs-outputs/image1.png)
3. Skopiuj klucz nowo wygenerowane.    
4. W portalu Azure Przejdź zadania usługi analiza strumienia > Wybierz **zatrzymać** i poczekaj na zatrzymanie zadania.    
5. Zlokalizuj obiekt Blob/tabeli magazynu wejścia/wyjścia, dla którego chcesz zaktualizować poświadczenia.    
6. Znajdź **klucz konta magazynu** pól i Wklej klucz wygenerowanym > kliknij **zapisać**.    
7. Test połączenia zostaną automatycznie uruchomione po zapisaniu zmian, możesz je wyświetlić na karcie powiadomienia. Brak jednego powiadomienia w dwóch odpowiada zapisywania aktualizacji oraz innych odnosi się do testowania połączenia:  
   ![Powiadomienia po klucz do edycji](media/stream-analytics-login-credentials-inputs-outputs/image4.png)
8. Przejdź do [Uruchom zadanie od czasu ostatniego zatrzymania] sekcji (#start-your-job-from-the-last-stopped-time).

### <a name="event-hubs"></a>Usługa Event Hubs

1. Zaloguj się do portalu Azure > Przeglądaj Centrum zdarzeń, używany jako danych wejściowych/wyjściowych zadania usługi analiza strumienia.    
2. W sekcji Ustawienia Otwórz **zasady dostępu współużytkowanego** i wybierz zasady, wymaganego dostępu. Między **klucza podstawowego** i **klucza pomocniczego**, wybierz jedną, która nie jest używany przez zadanie i wygenerowany ponownie:  
   ![Ponowne generowanie kluczy dla Centrum zdarzeń](media/stream-analytics-login-credentials-inputs-outputs/image2.png)
3. Skopiuj klucz nowo wygenerowane.    
4. W portalu Azure Przejdź zadania usługi analiza strumienia > Wybierz **zatrzymać** i poczekaj na zatrzymanie zadania.    
5. Znajdź zdarzenie koncentratory wejścia/wyjścia, dla którego chcesz zaktualizować poświadczenia.    
6. Znajdź **klucza zasad Centrum zdarzeń** pól i Wklej klucz wygenerowanym > kliknij **zapisać**.    
7. Test połączenia zostaną automatycznie uruchomione po zapisaniu zmian, upewnij się, że został przekazany pomyślnie.    
8. Przejdź do [Rozpocznij zadania od ostatniego zatrzymania](#start-your-job-from-the-last-stopped-time) sekcji.

### <a name="sql-database"></a>SQL Database

Wymagane do nawiązania połączenia z bazą danych SQL, aby zaktualizować poświadczenia logowania dla istniejącego użytkownika. Należy zaktualizować poświadczenia, za pomocą portalu Azure lub narzędzia po stronie klienta, takiego jak SQL Server Management Studio. W tej sekcji przedstawiono proces aktualizowania poświadczeń przy użyciu portalu Azure.

1. Zaloguj się do portalu Azure > Przeglądaj użyte jako dane wyjściowe zadania usługi analiza strumienia bazy danych SQL.    
2. Z **Eksploratora danych**, logowania/połączenia z bazą danych > Wybierz typ autoryzacji jako **uwierzytelniania programu SQL server** > wpisz w Twojej **logowania** i  **Hasło** szczegóły > Wybierz **Ok**.  
   ![Wygeneruj ponownie poświadczenia dla bazy danych SQL](media/stream-analytics-login-credentials-inputs-outputs/image3.png)

3. Na karcie zapytania należy zmienić hasło dla jednego użytkownika, uruchamiając następujące zapytanie (Upewnij się zastąpić `<user_name>` z nazwy użytkownika i `<new_password>` przy użyciu nowego hasła):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Zanotuj nowe hasło.    
5. W portalu Azure Przejdź zadania usługi analiza strumienia > Wybierz **zatrzymać** i poczekaj na zatrzymanie zadania.    
6. Znajdź dane wyjściowe bazy danych SQL, dla którego chcesz obrócić poświadczeń. Zaktualizuj hasło i Zapisz zmiany.    
7. Test połączenia zostaną automatycznie uruchomione po zapisaniu zmian, upewnij się, że został przekazany pomyślnie.    
8. Przejdź do [Rozpocznij zadania od ostatniego zatrzymania](#start-your-job-from-the-last-stopped-time) sekcji.

### <a name="power-bi"></a>Power BI
1. Zaloguj się do portalu Azure > Przeglądaj zadania usługi analiza strumienia > Wybierz **zatrzymać** i poczekaj na zatrzymanie zadania.    
2. Znajdź dane wyjściowe usługi Power BI, dla którego chcesz odnowić poświadczenia > kliknij **odnowić autoryzacji** (powinny pojawić komunikat z potwierdzeniem) > **zapisać** zmiany.    
3. Test połączenia zostaną automatycznie uruchomione po zapisaniu zmian, upewnij się, że jej został pomyślnie przekazany.    
4. Przejdź do [Rozpocznij zadania od ostatniego zatrzymania](#start-your-job-from-the-last-stopped-time) sekcji.

## <a name="start-your-job-from-the-last-stopped-time"></a>Uruchom zadanie od czasu ostatniego zatrzymania

1. Przejdź do zadania **omówienie** okienko > Wybierz **Start** można uruchomić zadania.    
2. Wybierz **podczas ostatnio zatrzymano** > kliknij **Start**. Należy pamiętać, że opcja "gdy ostatnio zatrzymano" pojawia się tylko, jeśli wcześniej uruchomione zadania i ma niektóre dane wyjściowe generowane. Zadanie zostanie ponownie zależności od czasu ostatniego wartość wyjściowa.
   ![Uruchom zadanie](media/stream-analytics-login-credentials-inputs-outputs/image5.png)

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
