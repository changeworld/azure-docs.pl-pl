---
title: Rozwiązywanie problemów z danymi wyjściowymi usługi Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z połączeniami wyjściowymi w zadaniach usługi Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d40157523a074547885a14a3d92379f8e8b6f351
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254289"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Rozwiązywanie problemów z danymi wyjściowymi usługi Azure Stream Analytics

Na tej stronie opisano typowe problemy z połączeniami wyjściowymi oraz sposób rozwiązywania problemów z nimi i rozwiązywania ich.

## <a name="output-not-produced-by-job"></a>Produkcja nieprodukcowa według pracy
1.  Sprawdź łączność z wyjściami za pomocą przycisku **Testuj połączenie** dla każdego wyjścia.

2.  Spójrz na [**monitorowanie metryki**](stream-analytics-monitoring.md) **na** monitorze kartę. Ponieważ wartości są agregowane, metryki są opóźnione o kilka minut.
    - Jeśli zdarzenia wejściowe > 0, zadanie jest w stanie odczytać dane wejściowe. Jeśli zdarzenia wejściowe nie są > 0, wówczas:
      - Aby sprawdzić, czy źródło danych zawiera prawidłowe dane, sprawdź to za pomocą [Eksploratora usługi Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Ta kontrola ma zastosowanie, jeśli zadanie używa Centrum zdarzeń jako danych wejściowych.
      - Sprawdź, czy format serializacji danych i kodowanie danych są zgodnie z oczekiwaniami.
      - Jeśli zadanie korzysta z Centrum zdarzeń, sprawdź, czy treść wiadomości ma *wartość Null*.

    - Jeśli błędy konwersji danych > 0 i wspinaczki, mogą być spełnione następujące warunki:
      - Zdarzenie danych wyjściowych nie jest zgodne ze schematem docelowej akcesji.
      - Schemat zdarzenia może nie odpowiadać zdefiniowanemu lub oczekiwanemu schematowi zdarzeń w kwerendzie.
      - Typy danych niektórych pól w zdarzeniu mogą nie odpowiadać oczekiwaniom.

    - Jeśli błędy środowiska uruchomieniowego > 0, oznacza to, że zadanie może odbierać dane, ale generuje błędy podczas przetwarzania kwerendy.
      - Aby znaleźć błędy, przejdź do [dzienników inspekcji](../azure-resource-manager/management/view-activity-logs.md) i filtruj stan *Niepowodzenie.*

    - Jeśli InputEvents > 0 i OutputEvents = 0, oznacza to, że jedna z następujących czynności jest spełniony:
      - W wyniku przetwarzania zapytania nie zostało zwrócone żadne zdarzenie wyjściowe.
      - Zdarzenia lub jego pola mogą być zniekształcone, co powoduje zero danych wyjściowych po przetworzeniu kwerendy.
      - Zadanie nie może wypchnąć danych do ujścia danych wyjściowych ze względu na łączność lub uwierzytelnianie.

    - We wszystkich wcześniej wymienionych przypadkach błędów komunikaty dziennika operacji wyjaśnić dodatkowe szczegóły (w tym co się dzieje), z wyjątkiem przypadków, gdy logika kwerendy odfiltrowane wszystkie zdarzenia. Jeśli przetwarzanie wielu zdarzeń generuje błędy, usługa Stream Analytics rejestruje pierwsze trzy komunikaty o błędach tego samego typu w ciągu 10 minut do dzienników operacji. Następnie pomija dodatkowe identyczne błędy z komunikatem z napisem "Błędy występują zbyt szybko, są one pomijane."

## <a name="job-output-is-delayed"></a>Job output is delayed (Dane wyjściowe zadania są opóźnione)

### <a name="first-output-is-delayed"></a>Pierwsze wyjście jest opóźnione
Po uruchomieniu zadania usługi Stream Analytics odczytywane są zdarzenia wejściowe, ale w pewnych okolicznościach generowanie danych wyjściowych może być opóźnione.

Duże wartości czasu w elementach zapytania czasowego może przyczynić się do opóźnienia danych wyjściowych. Aby uzyskać poprawne dane wyjściowe w dużych oknach czasu, zadanie przesyłania strumieniowego uruchamia się przez odczyt danych z ostatniego możliwego czasu (do siedmiu dni temu), aby wypełnić okno czasu. W tym czasie nie jest produkowanych żadnych danych wyjściowych, dopóki nie zostanie ukończony odczyt zaległych zdarzeń wejściowych. Ten problem może pojawić się, gdy system uaktualnia zadania przesyłania strumieniowego, a tym samym uruchom ponownie zadanie. Takie ulepszenia zazwyczaj występują raz na kilka miesięcy.

W związku z tym należy użyć dyskrecji podczas projektowania zapytania usługi Stream Analytics. Jeśli używasz dużego przedziału czasu (więcej niż kilka godzin, do siedmiu dni) dla elementów czasowych w składni kwerendy zadania, może to prowadzić do opóźnienia na pierwszym wyjściu po uruchomieniu lub ponownym uruchomieniu zadania.  

Jednym z czynników ograniczających zagrożenie dla tego rodzaju pierwszego opóźnienia wyjściowego jest użycie technik równoległości zapytania (partycjonowanie danych) lub dodanie większej liczby jednostek przesyłania strumieniowego w celu zwiększenia przepływności, dopóki zadanie nie zostanie nadrobić zaległości.  Aby uzyskać więcej informacji, zobacz [Zagadnienia podczas tworzenia zadań usługi Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)

Te czynniki mają wpływ na terminowość pierwszego generowanego wyjścia:

1. Korzystanie z agregatów okiennych (GROUP BY okien tumbling, hopping i przesuwnych)
   - W przypadku agregacji okien tumbling lub hopping wyniki są generowane na końcu przedziału czasowego okna.
   - W przypadku okna przesuwnego wyniki są generowane, gdy zdarzenie wchodzi lub wychodzi z okna przesuwnego.
   - Jeśli planujesz użyć dużego rozmiaru okna (> 1 godzinę), najlepiej wybrać okno przeskakiwania lub przesuwne, aby częściej wyświetlać dane wyjściowe.

2. Używanie sprzężeń czasowych (DOŁĄCZ z DATEDIFF)
   - Dopasowania są generowane natychmiast po przybyciu obu stron dopasowanych zdarzeń.
   - Dane, które nie mają dopasowania (LEFT OUTER JOIN) jest generowany na końcu okna DATEDIFF w odniesieniu do każdego zdarzenia po lewej stronie.

3. Korzystanie z czasowych funkcji analitycznych (ISFIRST, LAST i LAG z LIMIT DURATION)
   - Dla funkcji analitycznych dane wyjściowe są generowane dla każdego zdarzenia, nie ma opóźnienia.

### <a name="output-falls-behind"></a>Produkcja pozostaje w tyle
Podczas normalnego działania zadania, jeśli okaże się, że dane wyjściowe zadania są w tyle (dłuższe i dłuższe opóźnienia), można wskazać główne przyczyny, badając następujące czynniki:
- Czy zlew w dolnym biegu rzeki jest ograniczany
- Czy źródło nadrzędne jest ograniczane
- Określa, czy logika przetwarzania w kwerendzie jest wymagająca dużej mocy obliczeniowej

Aby wyświetlić te szczegóły, w witrynie Azure Portal wybierz zadanie przesyłania strumieniowego i wybierz **diagram zadań**. Dla każdego wejścia istnieje metryka zdarzenia zaległości na partycje. Jeśli metryka zdarzenia zaległości stale rośnie, jest wskaźnikiem, że zasoby systemowe są ograniczone. Potencjalnie jest to spowodowane ograniczaniem ujścia wyjściowego lub wysokim procesorem CPU. Aby uzyskać więcej informacji na temat korzystania z diagramu zadań, zobacz [Debugowanie oparte na danych przy użyciu diagramu zadań](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Ostrzeżenie o naruszeniu klucza z danymi wyjściowymi usługi Azure SQL Database

Podczas konfigurowania bazy danych SQL platformy Azure jako dane wyjściowe do zadania usługi Stream Analytics, to zbiorczo wstawia rekordy do tabeli docelowej. Ogólnie rzecz biorąc analizy strumienia platformy Azure gwarantuje [co najmniej raz dostawy](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) do ujścia danych wyjściowych, nadal można osiągnąć dokładnie raz [dostarczanie]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) do danych wyjściowych SQL, gdy tabela SQL ma zdefiniowane ograniczenie unikatowe.

Po skonfigurowaniu unikatowych ograniczeń klucza w tabeli SQL i są wstawiane zduplikowane rekordy do tabeli SQL, usługa Azure Stream Analytics usuwa zduplikowany rekord. Dzieli dane na partie i rekursywnie wstawiając partie, dopóki nie zostanie znaleziony pojedynczy zduplikowany rekord. Jeśli zadanie przesyłania strumieniowego ma znaczną liczbę zduplikowanych wierszy, ten proces podziału i wstawiania musi ignorować duplikaty jeden po drugim, co jest mniej wydajne i czasochłonne. Jeśli w dzienniku aktywności zostanie wyświetlonych wiele komunikatów o naruszeniu zasad naruszenia kluczy w ciągu ostatniej godziny, prawdopodobnie dane wyjściowe SQL spowalniają całe zadanie.

Aby rozwiązać ten problem, należy [skonfigurować indeks,]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) który powoduje naruszenie klucza, włączając opcję IGNORE_DUP_KEY. Włączenie tej opcji umożliwia zduplikowane wartości, które mają być ignorowane przez SQL podczas wstawia zbiorczych i SQL Azure po prostu tworzy komunikat ostrzegawczy zamiast błędu. Usługa Azure Stream Analytics nie generuje już błędów naruszenia podstawowych kluczy.

Należy zwrócić uwagę na następujące obserwacje podczas konfigurowania IGNORE_DUP_KEY dla kilku typów indeksów:

* Nie można ustawić IGNORE_DUP_KEY klucza podstawowego lub unikatowego ograniczenia, które używa ALTER INDEX, należy upuścić i odtworzyć indeks.  
* Można ustawić opcję IGNORE_DUP_KEY przy użyciu ALTER INDEX dla unikatowego indeksu, który różni się od ograniczenia KLUCZ PODSTAWOWY/UNIKATOWY i został utworzony przy użyciu definicji CREATE INDEX lub INDEX.  
* IGNORE_DUP_KEY nie ma zastosowania do indeksów magazynu kolumn, ponieważ nie można wymusić unikatowość w takich indeksach.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Nazwy kolumn są małe litery przez usługę Azure Stream Analytics
Podczas korzystania z oryginalnego poziomu zgodności (1.0), usługa Azure Stream Analytics służy do zmiany nazw kolumn na małe litery. To zachowanie zostało ustalone w późniejszych poziomach zgodności. Aby zachować sprawę, zalecamy klientom przejście do poziomu zgodności 1.1 i nowszych. Więcej informacji na temat [poziomu zgodności dla zadań usługi Azure Stream Analytics można](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)znaleźć .


## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
