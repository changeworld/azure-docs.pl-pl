---
title: Monitorowanie stanu i wyników indeksatora
titleSuffix: Azure Cognitive Search
description: Monitoruj stan, postęp i wyniki indeksatorów usługi Azure Cognitive Search w witrynie Azure portal, korzystając z interfejsu API REST lub sdk .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112975"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Jak monitorować stan i wyniki indeksatora usługi Azure Cognitive Search

Usługa Azure Cognitive Search udostępnia informacje o stanie i monitorowaniu bieżących i historycznych przebiegów każdego indeksatora.

Monitorowanie indeksatora jest przydatne, gdy chcesz:

* Śledzenie postępu indeksatora podczas trwającego przebiegu.
* Przejrzyj wyniki trwającego lub poprzedniego uruchomienia indeksatora.
* Identyfikowanie błędów indeksatora najwyższego poziomu oraz błędów lub ostrzeżeń dotyczących indeksowanych poszczególnych dokumentów.

## <a name="get-status-and-history"></a>Uzyskaj status i historię

Informacje o monitorowaniu indeksatora można uzyskać na różne sposoby, w tym:

* W [witrynie Azure portal](#portal)
* Korzystanie z [interfejsu API REST](#restapi)
* Korzystanie z [sdk .NET](#dotnetsdk)

Dostępne informacje o monitorowaniu indeksatora obejmują wszystkie następujące informacje (chociaż formaty danych różnią się w zależności od zastosowanej metody dostępu):

* Informacje o stanie samego indeksatora
* Informacje o najnowszym uruchomieniu indeksatora, w tym jego stan, godziny rozpoczęcia i zakończenia oraz szczegółowe błędy i ostrzeżenia.
* Lista historycznych uruchomień indeksatora oraz ich stanów, wyników, błędów i ostrzeżeń.

Indeksatory, które przetwarzają duże ilości danych może zająć dużo czasu, aby uruchomić. Na przykład indeksatory, które obsługują miliony dokumentów źródłowych można uruchomić przez 24 godziny, a następnie uruchomić niemal natychmiast. Stan indeksatorów dużej liczby może zawsze powiedzieć **w toku** w portalu. Nawet wtedy, gdy indeksator jest uruchomiony, szczegóły są dostępne na temat bieżącego postępu i poprzednich przebiegów.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Monitoruj za pomocą portalu

Możesz zobaczyć bieżący stan wszystkich indeksatorów na liście **Indeksatory** na stronie Przegląd usługi wyszukiwania.

   ![Lista indeksatorów](media/search-monitor-indexers/indexers-list.png "Lista indeksatorów")

Podczas wykonywania indeksatora stan na liście jest wyświetlany **w toku,** a wartość **Dokumenty powiodło się** pokazuje liczbę dokumentów przetwarzanych do tej pory. Może upłynąć kilka minut, aby portal zaktualizować wartości stanu indeksatora i liczby dokumentów.

Indeksator, którego ostatnie uruchomienie zakończyło się sukcesem, pokazuje **sukces**. Uruchomienie indeksatora może zakończyć się pomyślnie, nawet jeśli poszczególne dokumenty mają błędy, jeśli liczba błędów jest mniejsza niż ustawienie **max nie powiodło się elementami** indeksatora.

Jeśli ostatnie uruchomienie zakończyło się błędem, stan pokazuje **niepowodzenie**. Stan **Reset oznacza,** że stan śledzenia zmian indeksatora został zresetowany.

Kliknij indeksator na liście, aby wyświetlić więcej szczegółów na temat bieżących i ostatnich przebiegów indeksatora.

   ![Podsumowanie indeksatora i historia wykonywania](media/search-monitor-indexers/indexer-summary.png "Podsumowanie indeksatora i historia wykonywania")

Wykres **podsumowujący indeksatora** wyświetla wykres liczby dokumentów przetwarzanych w jego ostatnich przebiegach.

Lista **szczegółów wykonania** zawiera maksymalnie 50 najnowszych wyników wykonania.

Kliknij wynik wykonania na liście, aby wyświetlić szczegółowe informacje na temat tego uruchomienia. Obejmuje to jego czas rozpoczęcia i zakończenia oraz wszelkie błędy i ostrzeżenia, które wystąpiły.

   ![Szczegóły wykonania indeksatora](media/search-monitor-indexers/indexer-execution.png "Szczegóły wykonania indeksatora")

Jeśli podczas uruchamiania wystąpiły problemy specyficzne dla dokumentu, zostaną one wyświetlone w polach Błędy i ostrzeżenia.

   ![Szczegóły indeksatora z błędami](media/search-monitor-indexers/indexer-execution-error.png "Szczegóły indeksatora z błędami")

Ostrzeżenia są wspólne dla niektórych typów indeksatorów i nie zawsze wskazują na problem. Na przykład indeksatory korzystające z usług cognitive services mogą zgłaszać ostrzeżenia, gdy pliki obrazów lub plików PDF nie zawierają żadnego tekstu do przetworzenia.

Aby uzyskać więcej informacji na temat badania błędów i ostrzeżeń indeksatora, zobacz [Rozwiązywanie typowych problemów z indeksatorem w usłudze Azure Cognitive Search.](search-indexer-troubleshooting.md)

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Monitoruj przy użyciu interfejsów API REST

Historię stanu i wykonywania indeksatora można pobrać za pomocą [polecenia Pobierz stan indeksatora:](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Odpowiedź zawiera ogólny stan indeksatora, ostatnie wywołanie indeksatora (lub w toku) oraz historię ostatnich wywołań indeksatora.

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

Historia wykonywania zawiera maksymalnie 50 najnowszych przebiegów, które są sortowane w odwrotnej kolejności chronologicznej (najpierw najnowsze).

Należy zauważyć, że istnieją dwie różne wartości stanu. Stan najwyższego poziomu jest dla samego indeksatora. Stan indeksatora **uruchamiania** oznacza, że indeksator jest skonfigurowany poprawnie i dostępny do uruchomienia, ale nie jest aktualnie uruchomiony.

Każde uruchomienie indeksatora ma również swój własny stan, który wskazuje, czy to wykonanie jest w toku **(uruchomione),** czy już zakończone z **powodzeniem**, **przejściowyPowłascz**lub **persistentFailure** status. 

Gdy indeksator jest resetowany, aby odświeżyć jego stan śledzenia zmian, zostanie dodany osobny wpis historii wykonywania ze stanem **Reset.**

Aby uzyskać więcej informacji na temat kodów stanu i danych monitorowania indeksatora, zobacz [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Monitor przy użyciu sdk .NET

Harmonogram indeksatora można zdefiniować przy użyciu narzędzia Azure Cognitive Search .NET SDK. Aby to zrobić, należy uwzględnić właściwość **harmonogram** podczas tworzenia lub aktualizowania indeksatora.

Poniższy przykład języka C# zapisuje informacje o stanie indeksatora i wyniki jego ostatni (lub w toku) uruchomić do konsoli.

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

Należy zauważyć, że istnieją dwie różne wartości stanu. Stan najwyższego poziomu jest stanem samego indeksatora. Stan indeksatora **Uruchamianie** oznacza, że indeksator jest skonfigurowany poprawnie i dostępne do wykonania, ale nie, że jest obecnie wykonywany.

Każde uruchomienie indeksatora ma również swój własny stan, czy to wykonanie jest w toku **(Running),** czy zostało już zakończone ze stanem **Sukces** lub **TransientError.** 

Po zresetowaniu indeksatora, aby odświeżyć jego stan śledzenia zmian, zostanie dodany osobny wpis historii ze stanem **Reset.**

Aby uzyskać więcej informacji na temat kodów stanu i informacji o monitorowaniu indeksatora, zobacz [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) w interfejsie API REST.

Szczegółowe informacje o błędach lub ostrzeżeniach specyficznych dla dokumentu `IndexerExecutionResult.Errors` `IndexerExecutionResult.Warnings`można pobrać, wyliczając listy i .

Aby uzyskać więcej informacji na temat klas SDK .NET używanych do monitorowania indeksatorów, zobacz [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) i [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
