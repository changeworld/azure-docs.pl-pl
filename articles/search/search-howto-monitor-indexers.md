---
title: Jak monitorować stan indeksatora i wyniki — usługa Azure Search
description: Monitoruj stan, postęp i wyniki indeksatorów usługi Azure Search w witrynie Azure portal przy użyciu interfejsu API REST lub zestawu .NET SDK.
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486287"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Jak monitorować stan indeksatora usługi Azure Search i wyniki

Usługa Azure Search udostępnia stan i monitorowanie informacji o bieżące i historyczne uruchomień każdego indeksatora.

Monitorowanie indeksatora jest przydatne w przypadku, gdy chcesz:

* Śledź postęp indeksatora podczas ciągłego uruchamiania.
* Przejrzyj wyniki bieżących lub poprzednie uruchomienie indeksatora.
* Identyfikowanie indeksatora najwyższego poziomu błędów i błędy lub ostrzeżenia dotyczące poszczególnych dokumentów indeksowane.

## <a name="find-indexer-status-and-history-details"></a>Informacje indeksatora status i Historia

Aby uzyskać dostęp indeksatora informacji monitorowania na różne sposoby, w tym:

* W witrynie [Azure Portal](#portal)
* Za pomocą [interfejsu API REST](#restapi)
* Za pomocą [zestawu SDK platformy .NET](#dotnetsdk)

Indeksator dostępne informacje o monitorowaniu obejmuje wszystkie następujące (chociaż dane, które formaty różnią się na podstawie metody dostępu użyć):

* Informacje o stanie o samego indeksatora
* Informacje o najnowszych, ostatnio uruchomić indeksatora, w tym jego stan, rozpoczęcia i zakończenia, również szczegółowe błędy i ostrzeżenia.
* Lista uruchomień historycznych indeksatora i ich stany, wyniki, błędy i ostrzeżenia.

Indeksatory, które przetwarzają duże ilości danych może zająć dużo czasu do uruchomienia. Na przykład indeksatory, które obsługują milionami dokumentów źródła można uruchomić przez 24 godziny i następnie ponownie uruchom niemal natychmiast. Stan dla indeksatorów mocno obciążające zawsze powiedzieć **w toku** w portalu. Nawet wtedy, gdy działa indeksatora, dostępnych szczegółów o aktualnych i poprzednich przebiegów.

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>Monitor indeksatorów w portalu

Można wyświetlić bieżący stan wszystkich indeksatorów usługi w **indeksatory** listy na stronie przeglądu usługi wyszukiwania.

   ![Listę indeksatorów](media/search-monitor-indexers/indexers-list.png "listę indeksatorów")

Podczas wykonywania indeksatora, jego stan w przedstawiono listę **w toku**i **Docs zakończyło się pomyślnie** wartość jest wyświetlana liczba przetworzonych do tej pory dokumentów. Może upłynąć kilka minut, zanim portalu Aby zaktualizować wartości stanu indeksatora i liczby dokumentów.

Indeksator, którego ostatniego przebiegu został pomyślnie pokazuje **Powodzenie**. Uruchomienie indeksatora może odnieść sukces, nawet jeśli pojedyncze dokumenty zawierają błędy, jeśli liczba błędów jest mniejsza niż indeksatora **maksymalna liczba elementów zakończonych niepowodzeniem** ustawienie.

Jeśli ostatnie uruchomienie zakończone z błędem, jest wyświetlany stan **niepowodzenie**. Stan **resetowania** oznacza, że zresetowano stanu śledzenia zmian indeksatora.

Kliknij w indeksatorze, na liście, aby wyświetlić więcej szczegółowych informacji dotyczących indeksatora bieżące i ostatnie działa.

   ![Historia Podsumowanie i wykonywanie indeksatora](media/search-monitor-indexers/indexer-summary.png "historii Podsumowanie i wykonywanie indeksatora")

**Podsumowanie indeksatora** wykres przedstawia wykres liczba przetworzonych w jego najnowszych przebiegów dokumentów.

**Szczegóły wykonania** lista zawiera maksymalnie 50 ostatnich wyników wykonania.

Kliknij wynik wykonania na liście, aby wyświetlić szczegóły dotyczące uruchomienia. W tym swoje uruchomienie i czas zakończenia i wszelkie błędy i ostrzeżenia, które wystąpiły.

   ![Szczegóły wykonania indeksatora](media/search-monitor-indexers/indexer-execution.png "szczegóły wykonania indeksatora")

Jeśli wystąpiły problemy specyficzne dla dokumentu, podczas uruchamiania, będą one wyświetlane w polach błędy i ostrzeżenia.

   ![Szczegóły indeksator z błędami](media/search-monitor-indexers/indexer-execution-error.png "szczegóły indeksator z błędami")

Ostrzeżenia są wspólne w niektórych typach indeksatory i zawsze nie wskazują na problem. Na przykład indeksatorów, które używają usług cognitive services może zgłaszać ostrzeżenia, gdy obraz lub plików PDF nie zawiera tekstu w celu przetworzenia.

Aby uzyskać więcej informacji na temat badania indeksatora błędy i ostrzeżenia, zobacz [Rozwiązywanie typowych problemów indeksatora w usłudze Azure Search](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>Monitorowanie odbywać się przy użyciu interfejsu API REST

Można pobrać stanu i wykonywania historii usługi za pomocą indeksatora [polecenie pobierania stanu indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Odpowiedź zawiera ogólny stan indeksatora, wywołanie indeksatora ostatniego (lub w toku) i historię ostatnich wywołania indeksatora.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Historia wykonywania zawiera maksymalnie 50 ostatnich przebiegów, które są sortowane w kolejności chronologicznej odwrotnej (najnowsze na początku).

Należy pamiętać, że istnieją dwie wartości inny stan. Stan najwyższego poziomu jest sam indeksatora. Stan indeksatora **systemem** oznacza, że indeksator jest prawidłowo skonfigurowane i dostępna do uruchamiania, ale nie w aktualnie uruchomione.

Każde uruchomienie indeksatora ma również swój własny stan, który wskazuje, czy tego określonego wykonania jest ciągły (**systemem**), lub już została ukończona z **Powodzenie**, **transientFailure**, lub **persistentFailure** stanu. 

Gdy indeksator jest ustawiany na odświeżenie jej stanu śledzenia zmian, wpisem historii wykonywania oddzielnych zostanie dodany z **resetowania** stanu.

Aby uzyskać więcej informacji na temat kodów stanu i indeksowania danych monitorowania, zobacz [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>Monitorowanie odbywać się przy użyciu zestawu .NET SDK

Można zdefiniować harmonogram dla indeksatora przy użyciu zestawu .NET SDK usługi Azure Search. Aby to zrobić, należy dołączyć **harmonogram** właściwości podczas tworzenia lub aktualizowania indeksatora.

Następujące C# przykład zapisuje informacje o stanie indeksatora, a wyniki jego ostatnich (lub stałe) uruchom w konsoli.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

Dane wyjściowe w konsoli będą wyglądać mniej więcej tak:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Należy pamiętać, że istnieją dwie wartości inny stan. Stan najwyższego poziomu jest stan indeksatora, sam. Stan indeksatora **systemem** oznacza, że indeksator jest prawidłowo skonfigurowane i dostępne do wykonywania, ale nie jest w trakcie wykonywania.

Każde uruchomienie indeksatora ma również swój własny stan czy trwa tego określonego wykonania (**systemem**), lub zostało już zakończone z **Powodzenie** lub **TransientError** Stan. 

Gdy indeksator jest ustawiany na odświeżenie jej stanu śledzenia zmian, wpis historię zostanie dodany z **resetowania** stanu.

Aby uzyskać więcej informacji na temat kodów stanu i informacje o monitorowaniu indeksatora, zobacz [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) w interfejsie API REST.

Szczegółowe informacje o specyficzne dla dokumentu błędy lub ostrzeżenia mogą być pobierane przez wyliczenie list `IndexerExecutionResult.Errors` i `IndexerExecutionResult.Warnings`.

Aby uzyskać więcej informacji na temat klasy zestawu SDK platformy .NET, służy do monitorowania indeksatorów, zobacz [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) i [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
