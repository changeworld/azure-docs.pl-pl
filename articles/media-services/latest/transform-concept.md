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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 214d4d3d11255e417f3df1e5f6e648b2a30225ea
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377312"
---
# <a name="transforms-and-jobs"></a>Transformacje i zadania

## <a name="overview"></a>Przegląd 

Najnowszą wersję programu Azure Media Services REST API (v3) wprowadzono nowy zasób z szablonem przepływu pracy dla kodowania i/lub analizowanie filmów wideo o nazwie **Przekształcanie**. **Przekształca** mogą być używane do konfigurowania typowe zadania kodowania lub analying filmów wideo. Każdy **Przekształcanie** opisuje prosty przepływ pracy zadań przetwarzania plików wideo lub audio. 

**Przekształcanie** obiekt jest przepisu i **zadania** jest rzeczywistego żądania do usługi Azure Media Services, aby zastosować **Przekształcanie** do danego wejściowego zawartości wideo lub audio. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych. Można określić lokalizację usługi wideo przy użyciu: adresy URL HTTP (s), adresów URL sygnatury dostępu Współdzielonego lub ścieżkę do plików znajdujących się lokalnie lub w usłudze Azure Blob storage. Możesz mieć maksymalnie 100 przekształceń na Twoim koncie usługi Azure Media Services i przesyłania zadań w ramach tych przekształceń. Można następnie subskrybować zdarzenia, takie jak zmiany stanu zadania, za pomocą powiadomień, które łączyć bezpośrednio z systemu powiadomień platformy Azure Event Grid. 

Ponieważ ten interfejs API jest wymuszany przez usługę Azure Resource Manager, można użyć szablonów usługi Resource Manager do tworzenia i wdrażania przekształceń na Twoim koncie usługi Media Services. Kontrola dostępu oparta na roli można również ustawić na poziomie zasobów, w tym interfejsie API, dzięki czemu możesz zablokować dostęp do określonych zasobów, takich jak przekształcenia.

## <a name="transform-definition"></a>Przekształcanie definicji

W poniższej tabeli przedstawiono właściwości dla transformacji i zapewnia ich definicje.

|Name (Nazwa)|Typ|Opis|
|---|---|---|
|Identyfikator|ciąg|W pełni kwalifikowanego Identyfikatora zasobu dla zasobu.|
|name|ciąg|Nazwa zasobu.|
|Properties.created |ciąg|Data i Godzina UTC po transformacji został utworzony, w "RRRR-MM-Ddtgg" format.|
|Properties.Description |ciąg|Opcjonalny opis pełne przekształcenia.|
|properties.lastModified |ciąg|Data i Godzina UTC po transformacji ostatniej aktualizacji, w "RRRR-MM-Ddtgg" format.|
|Properties.outputs |[] TransformOutput|Tablica jednego lub więcej TransformOutputs generujących transformacji.|
|type|ciąg|Typ zasobu.|

Pełna definicja można zobaczyć [przekształca](https://docs.microsoft.com/rest/api/media/transforms).

## <a name="job-definition"></a>Definicja zadania

W poniższej tabeli przedstawiono właściwości zadania oraz zapewnia ich definicje.

|Name (Nazwa)|Typ|Opis|
|---|---|---|
|Identyfikator|ciąg|W pełni kwalifikowanego Identyfikatora zasobu dla zasobu.|
|name|ciąg|Nazwa zasobu.|
|Properties.created |ciąg|Data i Godzina UTC po transformacji został utworzony, w "RRRR-MM-Ddtgg" format.|
|Properties.Description |ciąg|Opcjonalny opis pełne zadania.|
|properties.lastModified |ciąg|Data i Godzina UTC po transformacji ostatniej aktualizacji, w "RRRR-MM-Ddtgg" format.|
|Properties.outputs |JobOutput []: [JobOutputAsset] |Dane wyjściowe zadania.|
|Properties.Priority |Priorytet |Priorytet, z którym mają być przetwarzane zadania. Zadania o wyższym priorytecie są przetwarzane przed zadaniami o niższym priorytecie. Jeśli nie jest ustawiona, wartość domyślna to normalny.
|Properties.state |JobState |Bieżący stan zadania.
|type|ciąg|Typ zasobu.|

Pełna definicja można zobaczyć [zadań](https://docs.microsoft.com/rest/api/media/jobs).

## <a name="typical-workflow-and-example"></a>Typowy przepływ pracy i przykład

1. Utwórz przekształcenia 
2. Przesyłanie zadań w ramach tej transformacji 
3. Lista przekształceń 
4. Usuń przekształcenie, jeśli nie zamierzasz w przyszłości korzystać z tego "przepisu". 

Załóżmy, że chcesz wyodrębnić pierwszej ramki wszystkie filmy wideo dotyczące jako obraz miniatury — są kroki, które można wykonać: 

1. Zdefiniuj reguły dla przetwarzania — na przykład, należy użyć pierwszej ramki wideo jako miniatury 
2. Następnie dla każdego filmu wideo, których masz jako dane wejściowe do usługi, możesz skonfigurować usługę: 
    1. Gdzie można znaleźć tego wideo i 
    2. Gdzie można zapisywać dane wyjściowe — na przykład obraz miniatury 

## <a name="next-steps"></a>Kolejne kroki

[Stream plików wideo](stream-files-dotnet-quickstart.md)
