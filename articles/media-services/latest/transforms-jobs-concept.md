---
title: Transformacje i zadania w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Korzystając z usługi Media Services, musisz utworzyć przekształcenie do opisu reguły lub specyfikacje dotyczące przetwarzania filmów wideo. Ten artykuł zawiera omówienie przekształcenie jest i jak z niej korzystać.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 95079813cf3ade41d17393168116e4767ca26e99
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742783"
---
# <a name="transforms-and-jobs"></a>Przekształcenia i zadania
 
Użyj [przekształca](https://docs.microsoft.com/rest/api/media/transforms) skonfigurować typowych zadań związanych z kodowaniem lub analizowanie filmów wideo. Każdy **Przekształcanie** opisuje młyna lub przepływu pracy zadań przetwarzania plików wideo lub audio. 

A [zadania](https://docs.microsoft.com/rest/api/media/jobs) jest rzeczywistego żądania do usługi Azure Media Services, aby zastosować **Przekształcanie** do danego wejściowego zawartości wideo lub audio. **Zadania** określa informacje, takie jak lokalizacja wejściowych plików wideo i lokalizację danych wyjściowych. Można określić lokalizację je wideo przy użyciu: Adresy URL HTTPS, adresów URL sygnatury dostępu Współdzielonego, lub [zasoby usługi Media Services](https://docs.microsoft.com/rest/api/media/assets).  

## <a name="typical-workflow"></a>Typowy przepływ pracy

1. Utwórz przekształcenia 
2. Przesyłanie zadań w ramach tej transformacji 
3. Lista przekształceń 
4. Usuń przekształcenie, jeśli nie zamierzasz używać go w przyszłości. 

Załóżmy, że chcesz wyodrębnić pierwszej ramki wszystkie filmy wideo dotyczące jako obraz miniatury — są kroki, które można wykonać: 

1. Zdefiniuj przepisu lub reguły dla przetwarzania plików wideo — "jako miniatury przy użyciu pierwszej ramki wideo". 
2. Dla każdego pliku wideo może poinformować usługi: 
    1. Gdzie można znaleźć tego wideo  
    2. Gdzie należy zapisać miniaturę danych wyjściowych. 

A **Przekształcanie** ułatwia tworzenie przepisu raz (krok 1) i przesyłanie zadań za pomocą tego przepisu (krok 2).

## <a name="transforms"></a>Przekształcenia

Przekształcenia można tworzyć w ramach konta usługi Media Services przy użyciu interfejsu API w wersji 3, bezpośrednio lub przy użyciu dowolnej z opublikowanych zestawów SDK. Przekształca Media Services v3, które interfejs API jest wymuszany przez usługę Azure Resource Manager, dzięki czemu można również użyć szablonów usługi Resource Manager do tworzenia i wdrażania w ramach konta usługi Media Services. Kontrola dostępu oparta na rolach może służyć do blokowania dostępu do przekształcenia.

### <a name="transform-definition"></a>Przekształcanie definicji

W poniższej tabeli przedstawiono właściwości transformacji i zapewnia ich definicje.

|Name (Nazwa)|Opis|
|---|---|
|Identyfikator|W pełni kwalifikowanego Identyfikatora zasobu dla zasobu.|
|name|Nazwa zasobu.|
|Properties.created |Data i Godzina UTC po transformacji został utworzony, w "RRRR-MM-Ddtgg" format.|
|Properties.Description |Opcjonalny opis pełne przekształcenia.|
|properties.lastModified |Data i Godzina UTC po transformacji ostatniej aktualizacji, w "RRRR-MM-Ddtgg" format.|
|Properties.outputs |Tablica jednego lub więcej TransformOutputs generujących transformacji.|
|type|Typ zasobu.|

Pełna definicja można zobaczyć [przekształca](https://docs.microsoft.com/rest/api/media/transforms).

Jak wyjaśniono powyżej, przekształcenia pomaga utworzyć przepisu lub regułę dla przetwarzania filmów wideo. Pojedynczy przekształcenia można zastosować więcej niż jedną regułę. Przekształcenie można na przykład określić, że każdy plik wideo można zakodowany w postaci pliku MP4 o danej szybkości transmisji bitów i wygenerowania obraz miniatury z pierwszej ramki filmu wideo. Należy dodać jeden wpis TransformOutput dla każdej reguły, które chcesz uwzględnić w swojej transformacji.

> [!NOTE]
> Natomiast definicji przekształcenia obsługuje operacji aktualizacji, należy się zająć do upewnij się, że wszystkie zadania w toku ukończyć przed wprowadzeniem zmiany. Zazwyczaj będzie zaktualizować istniejące transformacji, aby zmienić opis lub zmodyfikuj priorytety TransformOutputs bazowego. Jeśli chcesz ponownie zapisać przepisu, następnie należy utworzyć nowe przekształcenie.

## <a name="jobs"></a>Stanowiska

Po utworzeniu przekształcenia można przesłać zadania przy użyciu interfejsów API usług Media Services lub dowolny z opublikowanych zestawów SDK. Postęp i stan zadania można uzyskać poprzez monitorowanie zdarzeń za pomocą usługi Event Grid. Aby uzyskać więcej informacji, zobacz [monitorowania zdarzeń za pomocą EventGrid](job-state-events-cli-how-to.md ).

### <a name="jobs-definition"></a>Definicja zadania

W poniższej tabeli przedstawiono właściwości dla zadania oraz zapewnia ich definicje.

|Name (Nazwa)|Opis|
|---|---|
|id|W pełni kwalifikowanego Identyfikatora zasobu dla zasobu.|
|name   |Nazwa zasobu.|
|properties.correlationData |Klienta pod warunkiem, że dane korelacji (niezmiennego), które są zwracane jako część zadania i JobOutput stanu powiadomień o zmianie. Aby uzyskać więcej informacji, zobacz [schematy zdarzeń](media-services-event-schemas.md)<br/><br/>Właściwości można również uzyskać użycie wielu obcych dzierżaw usługi Media Services. Możesz umieścić identyfikatorów dzierżawy w zadania. |
|Properties.created |Data i Godzina UTC utworzenia zadania, w "RRRR-MM-Ddtgg" format.|
|Properties.Description |Opcjonalne klienta dostarczony opis zadania.|
|Properties.Input|Dane wejściowe dla zadania.|
|properties.lastModified    |Data i Godzina UTC podczas zadania ostatniej aktualizacji, w "RRRR-MM-Ddtgg" format.|
|Properties.outputs|Dane wyjściowe zadania.|
|Properties.Priority    |Priorytet, z którym mają być przetwarzane zadania. Zadania o wyższym priorytecie są przetwarzane przed zadaniami o niższym priorytecie. Jeśli nie jest ustawiona, wartość domyślna to normalny.|
|Properties.state   |Bieżący stan zadania.|
|type   |Typ zasobu.|

Pełna definicja można zobaczyć [zadań](https://docs.microsoft.com/rest/api/media/jobs).

> [!NOTE]
> Definicja zadania obsługuje operacji aktualizacji, jedyne właściwości, które mogą być modyfikowane po przesłaniu zadania są opis i priorytet. Ponadto zmiana priorytetu jest efektywne tylko wtedy, gdy zadanie jest nadal w stanie umieszczenia w kolejce. Jeśli zadania rozpoczął przetwarzanie lub zakończył, zmiana priorytetu nie ma znaczenia.

### <a name="pagination"></a>Paginacja

Podział na strony zadania jest obsługiwane w Media Services v3.

> [!TIP]
> Łącze do następnej zawsze należy używać wyliczania kolekcji i nie są zależne od wielkości określonej strony.

Jeśli odpowiedzi na zapytanie zawiera wiele elementów, usługa zwraca "\@odata.nextLink" właściwości do pobrania następnej strony wyników. Może to służyć do strony za pomocą cały zestaw wyników. Nie można skonfigurować rozmiaru strony. 

Zadania są tworzone lub usuwane podczas stronicować kolekcji, zmiany będą uwzględniane w zwróconych wyników (Jeśli te zmiany w części w kolekcji, która nie została pobrana.) 

Następujące C# przykład pokazuje, jak wyliczyć za pośrednictwem zadań w ramach konta.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

POZOSTAŁE przykłady można znaleźć [zadania — lista](https://docs.microsoft.com/rest/api/media/jobs/list)


## <a name="next-steps"></a>Kolejne kroki

[Stream plików wideo](stream-files-dotnet-quickstart.md)
