---
title: Typowe problemy, aby rozwiązać problemy w programie Azure Stream Analytics
description: W tym artykule opisano kilka typowych problemów w Azure Stream Analytics i kroki, aby je rozwiązać.
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: e04d1072acee635235b0a5bd8465ca38c861017b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523527"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Typowe problemy Stream Analytics i kroki rozwiązywania problemów z

## <a name="troubleshoot-malformed-input-events"></a>Rozwiązywanie problemów z źle sformułowane zdarzenia wejściowe

 Strumień wejściowy zadania Stream Analytics zawiera źle sformułowane wiadomości są spowodowane problemów serializacji. Na przykład nieprawidłowo sformułowany komunikat może być spowodowane przez brak nawiasu lub w obiekcie JSON brakuje nawiasu klamrowego lub niepoprawny format sygnatury w polu czasu do czasu. 
 
 Gdy zadanie usługi Stream Analytics odbiera nieprawidłowo sformułowany komunikat z wartością wejściową, porzuca wiadomości i powiadamia użytkownika z ostrzeżeniem. Symbol ostrzeżenie jest wyświetlane na **dane wejściowe** kafelka zadania usługi analiza strumienia (znaku ostrzeżenie istnieje tak długo, jak zadanie jest w stanie uruchomienia):

![Dane wejściowe kafelka](media/stream-analytics-malformed-events/inputs_tile.png)

Aby uzyskać więcej informacji, Włącz dzienniki diagnostyczne, aby wyświetlić szczegóły ostrzeżenia. Źle sformułowane zdarzenia wejściowe, dzienniki wykonywania zawierać wpis ze komunikat, który wygląda następująco: "komunikat: nie można deserializować wejściowego zdarzenia z zasobu <blob URI> jako json". 

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

1. Przejdź do wprowadzania kafelka i kliknij, aby wyświetlić ostrzeżenia.

2. Dane wejściowe kafelka Wyświetla zestaw ostrzeżenia o szczegółowe informacje o problemie. Poniżej przedstawiono przykładowy komunikat ostrzegawczy, zawiera komunikat ostrzegawczy partycji, przesunięcia i liczby sekwencji przypadku źle sformułowane dane JSON. 

   ![Komunikat ostrzegawczy z przesunięciem](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Aby uzyskać dane JSON, który ma niepoprawny format, należy uruchomić kod CheckMalformedEvents.cs. Ten przykład jest dostępny w [repozytorium GitHub przykłady](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ten kod odczytuje identyfikator partycji, przesunięcie i odbitek dane, które znajduje się w tym przesunięcia. 

4. Po odczytaniu danych możesz przeanalizować i poprawić format serializacji. 

## <a name="delayed-output"></a>Opóźnione danych wyjściowych

### <a name="first-output-is-delayed"></a>Pierwszy danych wyjściowych jest opóźnione
Po uruchomieniu zadania usługi analiza strumienia zdarzenia wejściowe są odczytywane, ale w danych wyjściowych tworzonym w pewnych okolicznościach może następować z opóźnieniem.

Wartości czasu dużych danych czasowych zapytań elementów może przyczynić się do opóźnienia danych wyjściowych. Aby wygenerować poprawne dane wyjściowe w dużych czas systemu windows, zadanie przesyłania strumieniowego jest uruchamiany za odczytywanie danych z najnowszych możliwości czasu (do siedmiu dni temu) do wypełnienia przedział czasu. W tym okresie nie wygenerowania danych wyjściowych do czasu ukończenia wyrównującej odczytu zaległych zdarzeń wejściowych. Ten problem można powierzchni, gdy system uaktualnia zadań przesyłania strumieniowego, w związku z tym ponowne uruchamianie zadania. Takie uaktualnień zazwyczaj wykonywane raz co kilka miesięcy. 

Dlatego należy zachować ostrożność podczas projektowania zapytania Stream Analytics. Jeśli używasz okno czasu duże (więcej niż kilka godzin, maksymalnie 7 dni) dla danych czasowych elementów w składni zapytania zadania, może to prowadzić do opóźnienia na pierwszym dane wyjściowe po uruchomieniu lub ponownym uruchomieniu zadania.  

Jeden środki zaradcze dla tego rodzaju pierwszy opóźnienie danych wyjściowych jest przy użyciu technik paralelizacja kwerendy (partycjonowanie danych), lub Dodaj więcej jednostek przesyłania strumieniowego w celu zwiększenia wydajności, dopóki zadanie wyrównania.  Aby uzyskać więcej informacji, zobacz [zagadnień dotyczących tworzenia zadania usługi analiza strumienia](stream-analytics-concepts-checkpoint-replay.md)

Te czynniki mieć wpływ na osi czasu pierwszego danych wyjściowych, generowany jest:

1. Użyj agregacjami (grupy przez z wirowania, skaczące i przedłużanie systemu windows)
   - Wirowania lub skaczące agreguje okna, wyniki są generowane na koniec przedziału czasu okna. 
   - W metodzie przesuwanego okna wyniki są generowane, gdy zdarzenie wprowadza lub opuszcza metodzie przesuwanego okna. 
   - Jeśli planujesz użyć rozmiaru okna duże (> 1 godz.), najlepiej wybrać przeskoku lub metodzie przesuwanego okna, aby wyświetlić dane wyjściowe w częściej.

2. Użyj danych czasowych sprzężeń (połączenie z DATEDIFF)
   - Dopasowań są generowane zaraz po obu stronach dopasowane zdarzeń przychodzących.
   - Dane, które nie ma dopasowania (LEWEGO sprzężenia zewnętrznego) jest generowany na końcu okna DATEDIFF względem każdego zdarzenia po lewej stronie.

3. Użyj danych czasowych funkcje analityczne (ISFIRST, LAST i opóźnienie z LIMIT czasu trwania)
   - Funkcje analityczne dane wyjściowe jest generowany dla każdego zdarzenia, nie ma żadnego opóźnienia.

### <a name="output-falls-behind"></a>Dane wyjściowe wykracza poza
Podczas normalnego działania zadania Jeśli okaże się, że dane wyjściowe zadania jest objęte za (dłużej i dłuższego czasu oczekiwania), użytkownik może w określeniu głównych przyczyn, sprawdzając czynniki te:
- Określa, czy jest ograniczany zbiornika podrzędne
- Określa, czy jest ograniczany nadrzędnego źródła
- Określa, czy logika przetwarzania w zapytaniu jest obliczeniowych

Aby wyświetlić te szczegóły w portalu Azure, wybierz zadanie przesyłania strumieniowego, a następnie wybierz **diagram zadania**. Dla każdego wejścia istnieje na partycji zaległych zdarzeń metryki. Jeśli Metryka zdarzenia zaległości stale, jest wskaźnik, że zasoby systemu są ograniczone. Potencjalnie to z powodu ograniczania ujście danych wyjściowych lub wysokie procesora CPU. Aby uzyskać więcej informacji na temat używania diagram zadania zobacz [danymi debugowanie przy użyciu diagramu zadania](stream-analytics-job-diagram-with-metrics.md).

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Obsługi zduplikowanych rekordów w danych wyjściowych bazy danych SQL Azure

Po skonfigurowaniu bazy danych Azure SQL jako dane wyjściowe do zadania usługi analiza strumienia masowo wstawia rekordy do tabeli docelowej. Ogólnie rzecz biorąc, usługi Azure stream analytics zapewnia [co najmniej raz dostarczania]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) do ujścia danych wyjściowych jednego nadal [dokładnie osiągnięcia — raz dostarczania]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) SQL output, gdy tabeli SQL ma zdefiniowane ograniczenia unique. 

Po w tabeli SQL są skonfigurowane ograniczenia klucza unique i zduplikowane rekordy wstawiane do tabeli SQL, usługi Azure Stream Analytics usuwa zduplikowany rekord. Dzieli dane w partii i rekursywnie Wstawianie partii, dopóki nie zostanie znaleziony jeden zduplikowany rekord. Jeśli zadanie przesyłania strumieniowego ma znaczną liczbę zduplikowane wiersze, tego podziału i Wstaw procesu musi Ignoruj duplikaty pojedynczo, który jest mniej wydajne i czasochłonny. Jeśli widzisz wielu komunikaty ostrzegawcze naruszenie klucza w dzienniku aktywności w ciągu ostatniej godziny jest prawdopodobne, że dane wyjściowe SQL jest spowolnienie całego zadania. 

Aby rozwiązać ten problem, należy [skonfigurować indeks]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) który powoduje naruszenie klucza przez włączenie opcji IGNORE_DUP_KEY. Włączenie tej opcji umożliwia zduplikowanych wartości, które mają być ignorowane przez SQL podczas operacji wstawiania zbiorczego i SQL Azure po prostu tworzy komunikat ostrzegawczy zamiast błędu. Usługa Azure Stream Analytics nie tworzy już błędy naruszenia dotyczącego klucza podstawowego.

Podczas konfigurowania IGNORE_DUP_KEY dla kilku typów indeksów, należy zwrócić uwagę następujące uwagi:

* Nie można ustawić IGNORE_DUP_KEY klucza podstawowego lub ograniczenia unique, która używa ALTER INDEX, musisz porzucić i ponownie utwórz indeks.  
* Można ustawić dla opcji IGNORE_DUP_KEY wartość przy użyciu ALTER INDEX dla unikatowego indeksu, która różni się od klucza podstawowego/UNIQUE, ograniczenia i utworzyć za pomocą definicji CREATE INDEX lub INDEKSU.  
* IGNORE_DUP_KEY nie dotyczy indeksy magazynu kolumn, ponieważ nie można wymuszać unikatowość takich indeksów.  

## <a name="next-steps"></a>Kolejne kroki
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
