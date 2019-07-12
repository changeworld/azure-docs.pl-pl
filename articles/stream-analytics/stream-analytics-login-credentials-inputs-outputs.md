---
title: Zamiana poświadczeń logowania w zadaniach usługi Azure Stream Analytics
description: W tym artykule opisano, jak można zaktualizować poświadczeń w danych wejściowych i danych wyjściowych wychwytywanie w usłudze Azure Stream Analytics, zadania.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: aad6f237a37ce4782e2c103a7a14e0a3951093de
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612362"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Zamiana poświadczeń logowania na potrzeby wejść i wyjść zadania usługi Stream Analytics

Zawsze, gdy zostaną ponownie wygenerowane poświadczenia dla danych wejściowych lub wyjściowych zadania usługi Stream Analytics, należy zaktualizować zadania za pomocą nowych poświadczeń. Przed zaktualizowaniem poświadczeń należy zatrzymać zadanie, nie można zamienić poświadczeń, gdy zadanie jest uruchomione. Aby zmniejszyć opóźnienie między zatrzymania i ponownego uruchomienia zadania, Stream Analytics obsługuje wznawianie zadania z jej ostatniej danych wyjściowych. W tym temacie opisano proces rotacji poświadczenia logowania i ponowne uruchamianie zadania za pomocą nowych poświadczeń.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Generuj nowe poświadczenia i zaktualizuj zadania przy użyciu nowych poświadczeń 

W tej sekcji omówimy proces zostanie wygenerowane ponownie poświadczenia dla usługi Blob Storage, usługi Event Hubs, SQL Database i Table Storage. 

### <a name="blob-storagetable-storage"></a>Usługi blob storage i Table storage
1. Zaloguj się do witryny Azure portal > Przejdź do konta magazynu używany jako dane wejściowe i wyjściowe dla zadania usługi Stream Analytics.    
2. W sekcji Ustawienia Otwórz **klucze dostępu**. Pomiędzy kluczami dwóch domyślnych (klucz1, klucz2) wybierz ten, który nie jest używany przez zadania i ponownie je wygenerować:  
   ![Ponowne generowanie kluczy konta magazynu](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Skopiuj klucz nowo wygenerowane.    
4. W witrynie Azure portal, przeglądać zadania usługi Stream Analytics > Wybierz **zatrzymać** i zaczekaj na zatrzymanie zadania.    
5. Znajdź obiekt Blob/Table storage wejścia/wyjścia, dla której chcesz zaktualizować poświadczenia.    
6. Znajdź **klucza konta magazynu** pola, a następnie wklej swój klucz nowo wygenerowane > kliknij **Zapisz**.    
7. Test połączenia zostanie automatycznie uruchomiony po zapisaniu zmian, można je wyświetlić, korzystając z karty powiadomienia. Istnieją dwa jednego powiadomienia odnosi się do zapisywania aktualizacji i innych odnosi się do testowania połączenia:  
   ![Powiadomienia po wprowadzeniu zmian klucza](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Przejdź do [Uruchom zadanie z ostatniego czasu zatrzymania](#start-your-job-from-the-last-stopped-time) sekcji.

### <a name="event-hubs"></a>Usługa Event Hubs

1. Zaloguj się do witryny Azure portal > Przejdź do Centrum zdarzeń, które jest używane jako dane wejściowe/wyjściowe jako zadania usługi Stream Analytics.    
2. W sekcji Ustawienia Otwórz **zasady dostępu współdzielonego** i wybierz zasady wymaganego dostępu. Między **klucza podstawowego** i **klucz pomocniczy**, wybierz ten, który nie jest używany przez zadania i ponownie je wygenerować:  
   ![Ponowne generowanie kluczy dla usługi Event Hubs](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Skopiuj klucz nowo wygenerowane.    
4. W witrynie Azure portal, przeglądać zadania usługi Stream Analytics > Wybierz **zatrzymać** i zaczekaj na zatrzymanie zadania.    
5. Znajdź zdarzenie hubs wejścia/wyjścia, dla której chcesz zaktualizować poświadczenia.    
6. Znajdź **klucz zasad Centrum zdarzeń** pola, a następnie wklej swój klucz nowo wygenerowane > kliknij **Zapisz**.    
7. Test połączenia zostanie automatycznie uruchomiony po zapisaniu zmian, upewnij się, że został przekazany pomyślnie.    
8. Przejdź do [Uruchom zadanie z ostatniego czasu zatrzymania](#start-your-job-from-the-last-stopped-time) sekcji.

### <a name="sql-database"></a>SQL Database

Należy połączyć się z bazą danych SQL, aby zaktualizować poświadczenia logowania dla istniejącego użytkownika. Należy zaktualizować poświadczenia, za pomocą witryny Azure portal lub narzędzi po stronie klienta, takich jak SQL Server Management Studio. W tej sekcji przedstawiono proces aktualizowania poświadczeń przy użyciu witryny Azure portal.

1. Zaloguj się do witryny Azure portal > Przejdź do bazy danych SQL, które jest używane jako dane wyjściowe jako zadanie usługi Stream Analytics.    
2. Z **Eksplorator danych**, logowania/połączeniem się z bazy danych > Wybierz typ autoryzacji jako **uwierzytelniania programu SQL server** > wpisz swoje **logowania** i  **Hasło** szczegóły > Wybierz **Ok**.  
   ![Wygeneruj ponownie poświadczenia dla bazy danych SQL](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. Na karcie zapytania, należy zmienić hasło dla jednego użytkownika, uruchamiając następujące zapytanie (Upewnij się zastąpić `<user_name>` ze swoją nazwą użytkownika i `<new_password>` przy użyciu nowego hasła):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Zanotuj nowe hasło.    
5. W witrynie Azure portal, przeglądać zadania usługi Stream Analytics > Wybierz **zatrzymać** i zaczekaj na zatrzymanie zadania.    
6. Znajdź danych wyjściowych z bazy danych SQL, dla którego chcesz obrócić poświadczeń. Zaktualizuj hasło, a następnie zapisz zmiany.    
7. Test połączenia zostanie automatycznie uruchomiony po zapisaniu zmian, upewnij się, że został przekazany pomyślnie.    
8. Przejdź do [Uruchom zadanie z ostatniego czasu zatrzymania](#start-your-job-from-the-last-stopped-time) sekcji.

### <a name="power-bi"></a>Power BI
1. Zaloguj się do witryny Azure portal > Przeglądaj zadania usługi Stream Analytics > Wybierz **zatrzymać** i zaczekaj na zatrzymanie zadania.    
2. Znajdź dane wyjściowe usługi Power BI, dla którego chcesz odnowić poświadczenia > kliknij **Odnów autoryzację** (wyświetlony komunikat o powodzeniu) > **Zapisz** zmiany.    
3. Test połączenia zostanie automatycznie uruchomiony po zapisaniu zmian, upewnij się, że jej został pomyślnie przekazany.    
4. Przejdź do [Uruchom zadanie z ostatniego czasu zatrzymania](#start-your-job-from-the-last-stopped-time) sekcji.

## <a name="start-your-job-from-the-last-stopped-time"></a>Uruchom zadanie z ostatniego czasu zatrzymania

1. Przejdź do zadania **Przegląd** okienko > Wybierz **Start** Aby uruchomić zadanie.    
2. Wybierz **ostatnio zatrzymane** > kliknij **Start**. Należy zauważyć, że opcja "ostatnio zatrzymane" pojawia się tylko, jeśli potrzebujesz wcześniej uruchomiono zadanie i pewne dane wyjściowe generowane. Zadanie jest uruchamiane ponownie oparte na od czasu ostatniej wartości danych wyjściowych.
   ![Uruchamianie zadania usługi Stream Analytics](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
