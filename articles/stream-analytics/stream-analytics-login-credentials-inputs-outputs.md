---
title: Obracanie poświadczeń logowania w zadaniach usługi Azure Stream Analytics
description: W tym artykule opisano sposób aktualizowania poświadczeń danych wejściowych i pochłanianych danych wyjściowych w zadaniach usługi Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 3ae639dd7c5a42fc6880240988f0fb2817b09f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425972"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Obracanie poświadczeń logowania dla danych wejściowych i wyjściowych zadania usługi Stream Analytics

Za każdym razem, gdy ponownie generujesz poświadczenia dla danych wejściowych lub wyjściowych zadania usługi Stream Analytics, należy zaktualizować zadanie za pomocą nowych poświadczeń. Należy zatrzymać zadanie przed zaktualizowaniem poświadczeń, nie można zastąpić poświadczenia, gdy zadanie jest uruchomione. Aby zmniejszyć opóźnienie między zatrzymaniem i ponownym uruchomieniem zadania, usługa Stream Analytics obsługuje wznawianie zadania z jego ostatniego wyjścia. W tym temacie opisano proces obracania poświadczeń logowania i ponownego uruchamiania zadania przy użyciu nowych poświadczeń.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Ponowne generowanie nowych poświadczeń i aktualizowanie zadania przy użyciu nowych poświadczeń 

W tej sekcji firma We przejdzie przez ponowne generowanie poświadczeń dla magazynu obiektów Blob, centrów zdarzeń, bazy danych SQL i magazynu tabel. 

### <a name="blob-storagetable-storage"></a>Magazyn obiektów blob/magazyn tabel
1. Zaloguj się do witryny Azure portal > przeglądać konto magazynu, które były używane jako dane wejściowe/wyjściowe dla zadania usługi Stream Analytics.    
2. W sekcji Ustawienia otwórz **klawisze programu Access**. Między dwoma kluczami domyślnymi (key1, key2) wybierz ten, który nie jest używany przez zadanie, i ponownie wygeneruj go:  
   ![Ponowne generowanie kluczy dla konta magazynu](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Skopiuj nowo wygenerowany klucz.    
4. W witrynie Azure portal przejrzyj zadanie usługi Stream Analytics > wybierz **pozycję Zatrzymaj** i poczekaj na zatrzymanie zadania.    
5. Zlokalizuj dane wejściowe/wyjściowe magazynu obiektów blob/table, dla których chcesz zaktualizować poświadczenia.    
6. Znajdź pole **Klucz konta magazynu** i wklej nowo wygenerowany klucz > kliknij przycisk **Zapisz**.    
7. Test połączenia rozpocznie się automatycznie po zapisaniu zmian, można go wyświetlić na karcie powiadomień. Istnieją dwa powiadomienia — jedno odpowiada zapisywaniu aktualizacji, a inne odpowiada testowaniu połączenia:  
   ![Powiadomienia po edycji klucza](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Przejdź do pracy, aby rozpocząć pracę od sekcji [czas ostatniego zatrzymania.](#start-your-job-from-the-last-stopped-time)

### <a name="event-hubs"></a>Usługa Event Hubs

1. Zaloguj się do witryny Azure portal > przeglądać Centrum zdarzeń, które były używane jako dane wejściowe/wyjściowe dla zadania usługi Stream Analytics.    
2. W sekcji ustawienia otwórz **zasady dostępu współdzielonego** i wybierz wymagane zasady dostępu. Między **kluczem podstawowym** a **kluczem pomocniczym**wybierz ten, który nie jest używany przez zadanie i ponownie go wygeneruj:  
   ![Ponowne generowanie kluczy dla centrów zdarzeń](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Skopiuj nowo wygenerowany klucz.    
4. W witrynie Azure portal przejrzyj zadanie usługi Stream Analytics > wybierz **pozycję Zatrzymaj** i poczekaj na zatrzymanie zadania.    
5. Znajdź wejścia/wyjścia centrum zdarzeń, dla których chcesz zaktualizować poświadczenia.    
6. Znajdź pole **Klucz zasad Centrum zdarzeń** i wklej nowo wygenerowany klucz > kliknij przycisk **Zapisz**.    
7. Test połączenia rozpocznie się automatycznie po zapisaniu zmian, upewnij się, że pomyślnie przeszedł.    
8. Przejdź do pracy, aby rozpocząć pracę od sekcji [czas ostatniego zatrzymania.](#start-your-job-from-the-last-stopped-time)

### <a name="sql-database"></a>Baza danych SQL

Musisz połączyć się z bazą danych SQL, aby zaktualizować poświadczenia logowania istniejącego użytkownika. Poświadczenia można aktualizować za pomocą witryny Azure portal lub narzędzia po stronie klienta, takiego jak SQL Server Management Studio. W tej sekcji przedstawiono proces aktualizowania poświadczeń przy użyciu witryny Azure portal.

1. Zaloguj się do witryny Azure portal > przeglądać bazę danych SQL, która została użyta jako dane wyjściowe dla zadania usługi Stream Analytics.    
2. Z **Eksploratora danych**zaloguj się/połącz się z bazą danych > wybierz opcję Typ autoryzacji jako **uwierzytelnianie serwera SQL** > wpisz szczegóły **logowania** i **hasła** > Wybierz **ok**.  
   ![Ponowne generowanie poświadczeń dla bazy danych SQL](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. Na karcie kwerendy zmień hasło jednego z użytkowników, uruchamiając następującą kwerendę (pamiętaj, aby zastąpić `<user_name>` swoją nazwą użytkownika i `<new_password>` nowym hasłem):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Zanotuj nowe hasło.    
5. W witrynie Azure portal przejrzyj zadanie usługi Stream Analytics > wybierz **pozycję Zatrzymaj** i poczekaj na zatrzymanie zadania.    
6. Znajdź dane wyjściowe bazy danych SQL, dla których chcesz obrócić poświadczenia. Zaktualizuj hasło i zapisz zmiany.    
7. Test połączenia rozpocznie się automatycznie po zapisaniu zmian, upewnij się, że pomyślnie przeszedł.    
8. Przejdź do pracy, aby rozpocząć pracę od sekcji [czas ostatniego zatrzymania.](#start-your-job-from-the-last-stopped-time)

### <a name="power-bi"></a>Power BI
1. Zaloguj się do witryny Azure portal > przeglądać zadanie usługi Stream Analytics > wybierz **pozycję Zatrzymaj** i poczekaj na zatrzymanie zadania.    
2. Znajdź dane wyjściowe usługi Power BI, dla których chcesz odnowić poświadczenia > kliknij **przycisk Odnów autoryzację** (powinien zostać wyświetlony komunikat o powodach) > **Zapisz** zmiany.    
3. Test połączenia rozpocznie się automatycznie po zapisaniu zmian, upewnij się, że pomyślnie przeszedł.    
4. Przejdź do pracy, aby rozpocząć pracę od sekcji [czas ostatniego zatrzymania.](#start-your-job-from-the-last-stopped-time)

## <a name="start-your-job-from-the-last-stopped-time"></a>Rozpocznij pracę od ostatniego zatrzymania

1. Przejdź do okienka **Przegląd** zadania > wybierz pozycję **Start,** aby rozpocząć zadanie.    
2. Wybierz pozycję **Kiedy ostatnio zatrzymano** > kliknij przycisk **Start**. Należy zauważyć, że opcja "Kiedy ostatnio zatrzymana" pojawia się tylko wtedy, gdy wcześniej uruchomiono zadanie i wygenerowano pewne dane wyjściowe. Zadanie zostanie ponownie uruchomione na podstawie czasu ostatniej wartości wyjściowej.
   ![Uruchamianie zadania usługi Stream Analytics](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
