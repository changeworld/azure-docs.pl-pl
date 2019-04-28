---
title: Typowe problemy można rozwiązać w usłudze Azure Stream Analytics
description: W tym artykule opisano kilka typowych problemów z usługi Azure Stream Analytics i kroki, aby rozwiązać te problemy.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 0191c56e1140870b1710b48c4fa1189fd92a337b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61362120"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Typowe problemy w usłudze Stream Analytics i kroki umożliwiające rozwiązywanie problemów

## <a name="troubleshoot-malformed-input-events"></a>Rozwiązywanie problemów z źle sformułowane zdarzenia wejściowe

 Przyczyną są problemy z serializacją, gdy strumień wejściowy zadania usługi Stream Analytics zawiera źle sformułowane komunikaty. Na przykład nieprawidłowo sformułowany komunikat może być spowodowane przez brak nawiasu lub Brak nawiasu klamrowego w obiekcie JSON lub niepoprawny format sygnatury w polu czasu do czasu. 
 
 Gdy zadanie usługi Stream Analytics otrzymuje nieprawidłowo sformułowany komunikat z danych wejściowych, porzuca wiadomość i powiadamia użytkownika z ostrzeżeniem. Symbol ostrzeżenia jest wyświetlany na **dane wejściowe** Kafelek zadania usługi Stream Analytics (ta oznaczeniem ostrzeżenia istnieje tak długo, jak długo zadanie jest w stanie uruchomienia):

![Dane wejściowe kafelka na pulpicie nawigacyjnym usługi Azure Stream Analytics](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Aby uzyskać więcej informacji, Włącz dzienniki diagnostyczne wyświetlić szczegóły ostrzeżenia. Źle sformułowane zdarzenia wejściowe dzienniki wykonywania zawiera wpis z komunikat, który wygląda następująco: "Komunikat: Nie można przeprowadzić deserializacji zdarzeń wejściowych z zasobu \<identyfikator URI obiektu blob > jako dane json ". 

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

1. Przejdź do kafelka danych wejściowych i kliknij, aby wyświetlić ostrzeżenia.

2. Na kafelku dane wejściowe, wyświetla zestaw ostrzeżeń wraz ze szczegółów o problemie. Poniżej przedstawiono przykładowy komunikat ostrzegawczy, komunikat ostrzegawczy pokazuje partycji, przesunięcie i sekwencji cyfry w przypadku, gdy są źle sformułowane dane JSON. 

   ![Wejściowy komunikat ostrzegawczy z przesunięciem](media/stream-analytics-malformed-events/warning-message-with-offset.png)

3. Aby uzyskać dane JSON, który ma niepoprawny format, należy uruchomić kod CheckMalformedEvents.cs. Ten przykład jest dostępny w [repozytorium przykładów usługi GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ten kod odczytuje identyfikator partycji: przesunięcie i drukuje dane, które znajdują się w tym przesunięciu. 

4. Po odczytaniu danych możesz przeanalizować i poprawić format serializacji.

5. Możesz również [odczytywać zdarzenia z usługi IoT Hub za pomocą Eksploratora usługi Service Bus](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="delayed-output"></a>Opóźnione danych wyjściowych

### <a name="first-output-is-delayed"></a>Pierwsze dane wyjściowe jest opóźnione
Po uruchomieniu zadania usługi Stream Analytics, zdarzenia wejściowe są odczytywane, ale w danych wyjściowych jest generowany w pewnych okolicznościach może nastąpić z opóźnieniem.

Wartości czasu duże w elementach danych czasowych zapytań można przekazywać do opóźnienie danych wyjściowych. Aby wygenerować poprawne dane wyjściowe za pośrednictwem okna czasowe dużych, zadanie przesyłania strumieniowego jest uruchamiany za odczytywanie danych z najnowszych możliwości czasu (maksymalnie siedem dni temu) do wypełnienia przedziału czasu. W tym czasie żadnych danych wyjściowych jest generowany, aż do zakończenia wyrównującej odczytu zaległe zdarzenia wejściowe. System uaktualnia zadania przesyłania strumieniowego, w związku z tym ponowne uruchamianie zadania, może pojawić się ten problem. Takie uaktualnień są zazwyczaj wykonywane raz co kilka miesięcy. 

Dlatego należy zachować ostrożność podczas projektowania zapytania usługi Stream Analytics. Jeśli używasz dużym oknie czasowym (więcej niż kilka godzin, maksymalnie siedem dni) dla danych czasowych elementów w składni zapytania zadania, może to prowadzić do opóźnienia na pierwszym dane wyjściowe po uruchomieniu lub ponownym uruchomieniu zadania.  

Jeden środki zaradcze dla tego rodzaju pierwszy opóźnienie danych wyjściowych jest przy użyciu technik przetwarzania równoległego zapytań (partycjonowanie danych) lub dodanie większej liczby jednostek przesyłania strumieniowego w celu zwiększenia przepływności, dopóki zadanie wyrównywane.  Aby uzyskać więcej informacji, zobacz [zagadnień dotyczących tworzenia zadania usługi Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)

Te czynniki mają wpływ aktualności pierwszy danych wyjściowych, który jest generowany:

1. Użycie okna agregacji (grupy według elementu wirowania, przeskokiem i przesuwanie systemu windows)
   - Wirowania lub przeskokiem oknie, agregacje, wyniki są generowane na końcu przedział czasu okna. 
   - Przesuwającego się okna wyniki są generowane, gdy zdarzenie wnoszone lub zamyka przesuwającego się okna. 
   - Jeśli planujesz użyć rozmiaru okna duży (> 1 godzina), najlepiej wybrać przeskoku lub przesuwanego okna, aby wyświetlić dane wyjściowe w częściej.

2. Korzystanie z danych czasowych sprzężenia (POŁĄCZ za pomocą funkcji DATEDIFF)
   - Dopasowania są generowane, zaraz po obu stronach dopasowane zdarzenia odbierane.
   - Dane, które nie ma dopasowania (po lewej stronie OUTER JOIN) jest generowany na końcu okna DATEDIFF w odniesieniu do każdego zdarzenia po lewej stronie.

3. Korzystanie z danych czasowych funkcje analityczne (ISFIRST, LAST i OPÓŹNIENIEM przy użyciu LIMIT DURATION)
   - Dla funkcji analitycznych danych wyjściowych jest generowany dla każdego zdarzenia, nie ma żadnego opóźnienia.

### <a name="output-falls-behind"></a>Dane wyjściowe wykracza poza
Podczas normalnego działania zadania Jeśli okaże się, że dane wyjściowe zadania jest objęte za (dłużej i dłuższego opóźnienia), można wyznaczyć głównych przyczyn, sprawdzając te czynniki:
- Czy jest ograniczany podrzędne obiektu sink
- Czy jest ograniczany nadrzędne źródło
- Czy logikę przetwarzania w zapytaniu jest intensywnych obliczeń

Aby zobaczyć te szczegółowe informacje w witrynie Azure portal, wybierz zadanie przesyłania strumieniowego, a następnie wybierz **diagram zadania**. Dla każdego danych wejściowych, nie istnieje na partycji zaległości zdarzeń metrykę. Jeśli Metryka zdarzenia zaległości stale rośnie, jest wskaźnik, że zasoby systemu są ograniczone. Potencjalnie to ze względu na ograniczenie ujścia danych wyjściowych lub wysokie użycie procesora CPU. Aby uzyskać więcej informacji na temat korzystania z diagramu zadania, zobacz [opartych na danych debugowanie za pomocą diagramu zadania](stream-analytics-job-diagram-with-metrics.md).

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Obsługa zduplikowanych rekordów w danych wyjściowych usługi Azure SQL Database

Po skonfigurowaniu bazy danych Azure SQL jako wyjścia zadania usługi Stream Analytics zbiorczo wstawia rekordy w tabeli docelowej. Ogólnie rzecz biorąc, usługa Azure stream analytics gwarantuje [co najmniej jednokrotnego dostarczenia]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) do ujścia danych wyjściowych jednego nadal [osiągnąć dokładnie-jednokrotnego dostarczenia]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) do bazy danych SQL. dane wyjściowe po tabeli SQL zdefiniowane ograniczenia unique. 

Gdy unikatowych ograniczeń klucza są konfigurowane w tabeli SQL i zduplikowane rekordy, które są wstawiane do tabeli SQL, Azure Stream Analytics usuwa zduplikowany rekord. Dzieli dane na partie i rekursywnie wstawiania partii, dopóki nie zostanie znaleziony jeden zduplikowany rekord. Jeśli zadanie przesyłania strumieniowego ma znaczną liczbę zduplikowane wiersze, w tym podziału i Wstaw procesu ma ignorowanie duplikaty pojedynczo, który jest mniej wydajne i czasochłonny proces. Jeśli widzisz kilka komunikaty ostrzegawcze naruszenie klucza w dzienniku aktywności w ciągu ostatniej godziny, prawdopodobnie danych wyjściowych SQL spowalnia całego zadania. 

Aby rozwiązać ten problem, należy [skonfigurować indeks]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , powoduje naruszenie klucza po włączeniu opcji IGNORE_DUP_KEY. Włączenie tej opcji umożliwia zduplikowanych wartości, które mają być ignorowane przez SQL podczas operacji wstawiania zbiorczego i SQL Azure, po prostu tworzy komunikat ostrzegawczy, a nie błąd. Usługa Azure Stream Analytics nie generuje już błędów naruszenia klucza podstawowego.

Podczas konfigurowania IGNORE_DUP_KEY pod kątem kilku typów indeksów, należy zwrócić uwagę następujących obserwacjach:

* Nie można ustawić IGNORE_DUP_KEY klucza podstawowego lub ograniczenia unique, który używa ALTER INDEX, musisz porzucić i ponownie utwórz indeks.  
* Można ustawić opcji IGNORE_DUP_KEY przy użyciu ALTER INDEX dla unikatowego indeksu, który różni się od ograniczenia klucza podstawowego/unikatowe i utworzone za pomocą definicji CREATE INDEX lub INDEKSU.  
* IGNORE_DUP_KEY nie ma zastosowania do indeksy magazynu kolumn, ponieważ nie można wymuszać unikatowość takich indeksów.  

## <a name="next-steps"></a>Kolejne kroki
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
