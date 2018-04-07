---
title: Rozwiązywanie problemów z źle sformułowane zdarzenia wejściowe w Azure Stream Analytics
description: Jak sprawdzić, które zdarzenie w moich danych wejściowych danych powoduje problem w zadaniu Stream Analytics
services: stream-analytics
author: jasonwhowell
manager: Kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: fcbb03b4d9aed797cf99c374661c743d39f81276
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Typowe problemy Stream Analytics i kroki rozwiązywania problemów z

## <a name="troubleshoot-for-malformed-input-events"></a>Rozwiązywanie problemów z źle sformułowane zdarzenia wejściowe

Kiedy strumień wejściowy zadania Stream Analytics zawiera źle sformułowane wiadomości, powoduje problemy serializacji. Wadliwe komunikaty obejmują niepoprawne serializacji, takie jak brak nawiasów w obiekcie JSON lub format sygnatury czasowej niepoprawne. Zadanie Stream Analytics odebrania nieprawidłowo sformułowany komunikat porzuca wiadomości i powiadamia użytkownika z ostrzeżeniem. Symbol ostrzeżenie jest wyświetlane na **dane wejściowe** kafelka zadania usługi analiza strumienia (znaku ostrzeżenie istnieje tak długo, jak zadanie jest w stanie uruchomienia):

![Dane wejściowe kafelka](media/stream-analytics-malformed-events/inputs_tile.png)

Można włączyć dzienniki diagnostyczne, aby wyświetlić szczegóły ostrzeżenia. Malformatted zdarzeń wejściowych, dzienniki wykonywania zawierać wpis ze komunikat, który wygląda następująco: "komunikat: nie można deserializować zdarzenia wejściowego z zasobu <blob URI> formacie json)". 

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

1. Przejdź do wprowadzania kafelka i kliknij, aby wyświetlić ostrzeżenia.
2. Dane wejściowe kafelka Wyświetla zestaw ostrzeżenia o szczegółowe informacje o problemie. Poniżej przedstawiono przykładowy komunikat ostrzegawczy, zawiera komunikat ostrzegawczy partycji, przesunięcia i liczby sekwencji przypadku źle sformułowane dane JSON. 

   ![Komunikat ostrzegawczy z przesunięciem](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Aby uzyskać dane JSON, który ma niepoprawny format, uruchom kod CheckMalformedEvents.cs, możesz uzyskać it z [repozytorium GitHub przykłady](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ten kod odczytuje partycji identyfikator, przesunięcie i wyświetla dane, które znajduje się w tym przesunięcia. 

4. Po odczytaniu danych możesz przeanalizować i poprawić format serializacji. 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>Obsługa zduplikowanych rekordów, korzystając z bazy danych SQL Azure jako dane wyjściowe zadania usługi analiza strumienia

Po skonfigurowaniu bazy danych Azure SQL jako dane wyjściowe do zadania usługi analiza strumienia masowo wstawia rekordy do tabeli docelowej. Ogólnie rzecz biorąc, usługi Azure stream analytics zapewnia [co najmniej raz dostarczania]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) do ujścia danych wyjściowych jednego nadal [dokładnie osiągnięcia — raz dostarczania]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) SQL output, gdy tabeli SQL ma zdefiniowane ograniczenia unique. 

Po ograniczeń klucza unique są skonfigurowane w tabeli SQL, a istnieją zduplikowane rekordy wstawiane do tabeli SQL, usługi Azure Stream Analytics usuwa zduplikowany rekord, Podziel dane na partie i rekursywnie Wstawianie partii do pojedynczej Znaleziono zduplikowany rekord. W przypadku znaczną liczbę zduplikowane wiersze planowaną dzielenia i wstawianie danych ignorowanie duplikaty jeden po drugim rekursywnie jest czasochłonne. Jeśli widzisz wielu komunikaty ostrzegawcze naruszenie klucza w dzienniku aktywności w ciągu ostatniej godziny jest prawdopodobne, że dane wyjściowe SQL jest spowolnienie całego zadania. Aby rozwiązać ten problem, należy [skonfigurować indeks]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) który powoduje naruszenie klucza przez włączenie opcji IGNORE_DUP_KEY. Włączenie tej opcji umożliwia zduplikowanych wartości, które mają być ignorowane przez SQL podczas operacji wstawiania zbiorczego i SQL Azure po prostu tworzy komunikat ostrzegawczy zamiast błędu. Usługa Azure Stream Analytics nie tworzy już błędy naruszenia dotyczącego klucza podstawowego.

Podczas konfigurowania IGNORE_DUP_KEY dla kilku typów indeksów, należy zwrócić uwagę następujące uwagi:

* Nie można ustawić IGNORE_DUP_KEY klucza podstawowego lub ograniczenia unique, która używa ALTER INDEX, musisz porzucić i ponownie utwórz indeks.  
* Można ustawić dla opcji IGNORE_DUP_KEY wartość przy użyciu ALTER INDEX dla unikatowego indeksu, która różni się od klucza podstawowego/UNIQUE, ograniczenia i utworzyć za pomocą definicji CREATE INDEX lub INDEKSU.  
* IGNORE_DUP_KEY nie dotyczy indeksy magazynu kolumn, ponieważ nie można wymuszać unikatowość takich indeksów.  

## <a name="next-steps"></a>Kolejne kroki

* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

