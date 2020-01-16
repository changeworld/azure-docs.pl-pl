---
title: Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z połączeniami wyjściowymi w Azure Stream Analytics zadaniach.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d40157523a074547885a14a3d92379f8e8b6f351
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980260"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics

Na tej stronie opisano typowe problemy związane z połączeniami wyjściowymi oraz sposoby rozwiązywania problemów i ich rozwiązania.

## <a name="output-not-produced-by-job"></a>Dane wyjściowe nie zostały utworzone przez zadanie
1.  Sprawdź łączność z danymi wyjściowymi przy użyciu przycisku **Testuj połączenie** dla każdego elementu wyjściowego.

2.  Sprawdź [**metryki monitorowania**](stream-analytics-monitoring.md) na karcie **monitorowanie** . Ponieważ wartości są agregowane, metryki są opóźnione o kilka minut.
    - Jeśli zdarzenia wejściowe > 0, zadanie może odczytywać dane wejściowe. Jeśli zdarzenia wejściowe nie są > 0, wówczas:
      - Aby sprawdzić, czy źródło danych ma prawidłowe dane, sprawdź je za pomocą [eksploratora Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Ten test ma zastosowanie, jeśli zadanie używa centrum zdarzeń jako dane wejściowe.
      - Sprawdź, czy format serializacji danych i kodowanie danych są zgodnie z oczekiwaniami.
      - Jeśli zadanie korzysta z centrum zdarzeń, sprawdź, czy treść wiadomości jest *równa null*.

    - Jeśli błędy konwersji danych > 0 i wspinanie się, mogą być spełnione następujące warunki:
      - Zdarzenie wyjściowe jest niezgodne ze schematem docelowego ujścia.
      - Schemat zdarzenia może być niezgodny ze zdefiniowanym lub oczekiwanym schematem zdarzeń w zapytaniu.
      - Typy danych niektórych pól w zdarzeniu mogą być niezgodne z oczekiwaniami.

    - Jeśli błędy środowiska uruchomieniowego > 0, oznacza to, że zadanie może odbierać dane, ale generuje błędy podczas przetwarzania zapytania.
      - Aby znaleźć błędy, przejdź do [dzienników inspekcji](../azure-resource-manager/management/view-activity-logs.md) i*odfiltrować* stanu.

    - Jeśli InputEvents > 0 i OutputEvents = 0, oznacza to, że jest spełniony jeden z następujących warunków:
      - W wyniku przetwarzania zapytania nie zostało zwrócone żadne zdarzenie wyjściowe.
      - Zdarzenia lub jego pola mogą być źle sformułowane, co powoduje wyjście z zera po przetworzeniu zapytania.
      - Zadanie nie może wypchnąć danych do ujścia wyjściowego w celu uzyskania łączności lub przyczyny uwierzytelnienia.

    - We wszystkich wyżej wymienionych przypadkach błędów komunikaty dzienników operacji wyjaśniają dodatkowe szczegóły (w tym informacje o tym, co się dzieje), z wyjątkiem przypadków, gdy logika zapytań odfiltrowana wszystkie zdarzenia. Jeśli przetwarzanie wielu zdarzeń generuje błędy, Stream Analytics rejestruje pierwsze trzy komunikaty o błędach tego samego typu w ciągu 10 minut do dzienników operacji. Następnie pomija dodatkowe identyczne błędy z komunikatem "błędy są wykonywane zbyt szybko, są pomijane".

## <a name="job-output-is-delayed"></a>Dane wyjściowe zadania są opóźnione

### <a name="first-output-is-delayed"></a>Pierwsze dane wyjściowe jest opóźnione
Po uruchomieniu zadania usługi Stream Analytics odczytywane są zdarzenia wejściowe, ale w pewnych okolicznościach generowanie danych wyjściowych może być opóźnione.

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

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Ostrzeżenie o naruszeniu klucza z danymi wyjściowymi Azure SQL Database

Po skonfigurowaniu bazy danych Azure SQL jako wyjścia zadania usługi Stream Analytics zbiorczo wstawia rekordy w tabeli docelowej. Ogólnie rzecz biorąc, usługa Azure stream analytics gwarantuje [co najmniej jednokrotnego dostarczenia](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) do ujścia danych wyjściowych jednego nadal [osiągnąć dokładnie-jednokrotnego dostarczenia]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) do bazy danych SQL. dane wyjściowe po tabeli SQL zdefiniowane ograniczenia unique.

Gdy unikatowych ograniczeń klucza są konfigurowane w tabeli SQL i zduplikowane rekordy, które są wstawiane do tabeli SQL, Azure Stream Analytics usuwa zduplikowany rekord. Dzieli dane na partie i rekursywnie wstawiania partii, dopóki nie zostanie znaleziony jeden zduplikowany rekord. Jeśli zadanie przesyłania strumieniowego ma znaczną liczbę zduplikowane wiersze, w tym podziału i Wstaw procesu ma ignorowanie duplikaty pojedynczo, który jest mniej wydajne i czasochłonny proces. Jeśli widzisz kilka komunikaty ostrzegawcze naruszenie klucza w dzienniku aktywności w ciągu ostatniej godziny, prawdopodobnie danych wyjściowych SQL spowalnia całego zadania.

Aby rozwiązać ten problem, należy [skonfigurować indeks]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , powoduje naruszenie klucza po włączeniu opcji IGNORE_DUP_KEY. Włączenie tej opcji umożliwia zduplikowanych wartości, które mają być ignorowane przez SQL podczas operacji wstawiania zbiorczego i SQL Azure, po prostu tworzy komunikat ostrzegawczy, a nie błąd. Usługa Azure Stream Analytics nie generuje już błędów naruszenia klucza podstawowego.

Podczas konfigurowania IGNORE_DUP_KEY pod kątem kilku typów indeksów, należy zwrócić uwagę następujących obserwacjach:

* Nie można ustawić IGNORE_DUP_KEY klucza podstawowego lub ograniczenia unique, który używa ALTER INDEX, musisz porzucić i ponownie utwórz indeks.  
* Można ustawić opcji IGNORE_DUP_KEY przy użyciu ALTER INDEX dla unikatowego indeksu, który różni się od ograniczenia klucza podstawowego/unikatowe i utworzone za pomocą definicji CREATE INDEX lub INDEKSU.  
* IGNORE_DUP_KEY nie ma zastosowania do indeksy magazynu kolumn, ponieważ nie można wymuszać unikatowość takich indeksów.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Nazwy kolumn są mniejsze niż w przypadku Azure Stream Analytics
Przy użyciu oryginalnego poziomu zgodności (1,0) Azure Stream Analytics używany do zmiany nazw kolumn na małe litery. Takie zachowanie zostało rozwiązane na późniejszych poziomach zgodności. Aby zachować sprawność, firma Microsoft zaleca klientom przejście do poziomu zgodności 1,1 i nowszych. Więcej informacji na temat poziomu zgodności można znaleźć w [Azure Stream Analytics zadaniach](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).


## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
