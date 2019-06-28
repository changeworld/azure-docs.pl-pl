---
title: Rozwiązywanie problemów z danych wyjściowych usługi Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z połączeniami danych wyjściowych w zadań usługi Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7dbb04a9d002fdcff49d28f69ee0975500bb7ed0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340788"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Rozwiązywanie problemów z danych wyjściowych usługi Azure Stream Analytics

Ta strona zawiera opis typowych problemów z połączeniami danych wyjściowych i jak rozwiązać i je rozwiązać.

## <a name="output-not-produced-by-job"></a>Dane wyjściowe nie są wytwarzane przez zadania 
1.  Sprawdź łączność z danych wyjściowych za pomocą **Testuj połączenie** przycisku dla każdego danych wyjściowych.

2.  Przyjrzyj się [ **metryki monitorowania** ](stream-analytics-monitoring.md) na **Monitor** kartę. Ponieważ wartości są agregowane, metryki są opóźnione o kilka minut.
    - Jeśli zdarzenia wejściowe > 0, zadanie jest w stanie odczytać dane wejściowe. Jeśli zdarzeń wejściowych nie jest > 0, wtedy:
      - Aby sprawdzić, czy źródło danych zawiera prawidłowe dane, należy sprawdzić je za pomocą [Eksploratora usługi Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Ta sprawdzenie jest stosowane, jeśli zadanie używa Centrum zdarzeń jako dane wejściowe.
      - Sprawdź, czy format serializacji danych i kodowanie są zgodne z oczekiwaniami.
      - Jeśli zadanie używa Centrum zdarzeń, sprawdź, czy treść komunikatu jest *Null*.
      
    - Jeśli błędy konwersji danych > 0 i rośnie, następujące może mieć wartość true:
      - Zdarzenie wyjściowe nie jest zgodny ze schematem ujścia docelowego. 
      - Schemat zdarzeń mogą być niezgodne ze schematem zdefiniowanego lub oczekiwanego zdarzenia w zapytaniu.
      - Typy danych niektórych pól w zdarzeniu może być niezgodny oczekiwania.
      
    - Jeśli błędy czasu wykonywania > 0, oznacza to, że zadanie może odbierać dane, ale generuje błędy podczas przetwarzania zapytania.
      - Aby znaleźć błędy, przejdź do [dzienników inspekcji](../azure-resource-manager/resource-group-audit.md) i*odfiltrować* stanu.
      
    - Jeśli liczba > 0, a liczba zdarzeń wyjściowych = 0, oznacza to, że jest spełniony jeden z następujących czynności:
      - W wyniku przetwarzania zapytania nie zostało zwrócone żadne zdarzenie wyjściowe.
      - Zdarzenia lub ich pola mogą być źle sformułowane w efekcie zero wynik po zakończeniu przetwarzania zapytania.
      - Zadanie nie może wypychanie danych do ujścia danych wyjściowych ze względów łączności lub uwierzytelniania.
      
    - We wszystkich przypadkach błąd wspomniano wcześniej, komunikaty dziennika operacji zawierają dodatkowe szczegóły (włącznie, co dzieje się), z wyjątkiem przypadków, gdy logika zapytania odfiltrowała wszystkie zdarzenia. Jeśli przetwarzanie wielu zdarzeń generuje błędy, Stream Analytics rejestruje pierwsze trzy komunikaty tego samego typu w ciągu 10 minut do dzienników operacji. Następnie pomija dodatkowe identyczne błędy z komunikatem "Błędy występują zbyt często są one pomijane."
    
## <a name="job-output-is-delayed"></a>Dane wyjściowe zadania jest opóźnione

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

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Ostrzeżenie naruszenie klucza z danych wyjściowych usługi Azure SQL Database

Po skonfigurowaniu bazy danych Azure SQL jako wyjścia zadania usługi Stream Analytics zbiorczo wstawia rekordy w tabeli docelowej. Ogólnie rzecz biorąc, usługa Azure stream analytics gwarantuje [co najmniej jednokrotnego dostarczenia]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) do ujścia danych wyjściowych jednego nadal [osiągnąć dokładnie-jednokrotnego dostarczenia]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) do bazy danych SQL. dane wyjściowe po tabeli SQL zdefiniowane ograniczenia unique. 

Gdy unikatowych ograniczeń klucza są konfigurowane w tabeli SQL i zduplikowane rekordy, które są wstawiane do tabeli SQL, Azure Stream Analytics usuwa zduplikowany rekord. Dzieli dane na partie i rekursywnie wstawiania partii, dopóki nie zostanie znaleziony jeden zduplikowany rekord. Jeśli zadanie przesyłania strumieniowego ma znaczną liczbę zduplikowane wiersze, w tym podziału i Wstaw procesu ma ignorowanie duplikaty pojedynczo, który jest mniej wydajne i czasochłonny proces. Jeśli widzisz kilka komunikaty ostrzegawcze naruszenie klucza w dzienniku aktywności w ciągu ostatniej godziny, prawdopodobnie danych wyjściowych SQL spowalnia całego zadania. 

Aby rozwiązać ten problem, należy [skonfigurować indeks]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , powoduje naruszenie klucza po włączeniu opcji IGNORE_DUP_KEY. Włączenie tej opcji umożliwia zduplikowanych wartości, które mają być ignorowane przez SQL podczas operacji wstawiania zbiorczego i SQL Azure, po prostu tworzy komunikat ostrzegawczy, a nie błąd. Usługa Azure Stream Analytics nie generuje już błędów naruszenia klucza podstawowego.

Podczas konfigurowania IGNORE_DUP_KEY pod kątem kilku typów indeksów, należy zwrócić uwagę następujących obserwacjach:

* Nie można ustawić IGNORE_DUP_KEY klucza podstawowego lub ograniczenia unique, który używa ALTER INDEX, musisz porzucić i ponownie utwórz indeks.  
* Można ustawić opcji IGNORE_DUP_KEY przy użyciu ALTER INDEX dla unikatowego indeksu, który różni się od ograniczenia klucza podstawowego/unikatowe i utworzone za pomocą definicji CREATE INDEX lub INDEKSU.  
* IGNORE_DUP_KEY nie ma zastosowania do indeksy magazynu kolumn, ponieważ nie można wymuszać unikatowość takich indeksów.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Nazwy kolumn to dolna — z uwzględnieniem wielkości liter przez usługę Azure Stream Analytics
Korzystając z oryginalnego poziomu zgodności (1.0), Azure Stream Analytics umożliwia zmienianie nazw kolumn na małe litery. Ten problem został rozwiązany w nowszej poziomy zgodności. W celu zachowania w przypadku, zaleca się klientów do przejścia na poziom zgodności, 1.1 i nowszych. Więcej informacji można znaleźć na [poziom zgodności dla zadań usługi Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).


## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
