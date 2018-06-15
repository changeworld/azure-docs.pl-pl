---
title: Przekształca i zadania w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Korzystając z usługi Media Services, musisz utworzyć transformacji dla opisu zasady lub wymagania dotyczące przetwarzania plików wideo. Ten artykuł zawiera omówienie przekształcenie jest i jak z niego korzystać.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: b755e0573098d3dbed1bea18a40af634be609f76
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34272084"
---
# <a name="transforms-and-jobs"></a>Transformacje i zadania

## <a name="overview"></a>Przegląd 

Najnowszą wersję programu Azure Media Services REST API (v3) wprowadzono nowy zasób szablonem przepływu pracy do kodowania i/lub analizowania wideo o nazwie **przekształcenie**. **Przekształca** może służyć do konfiguracji typowych zadań związanych z kodowania lub analying wideo. Każdy **przekształcenie** opisuje proste przepływu pracy zadań przetwarzania plików wideo lub audio. 

**Przekształcenie** obiekt jest przepisu i **zadania** rzeczywistego żądania do usługi Azure Media Services do zastosowania, który jest **przekształcenie** do danej wejściowej zawartości wideo lub audio. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych. Można określić lokalizację wideo użycia: adresy URL HTTP (s), adresy URL SAS lub ścieżka do plików znajdujących się lokalnie lub w magazynie obiektów Blob Azure. Można mieć maksymalnie 100 przekształcenia na koncie usługi Azure Media Services i przesyłania zadań w tych transformacji. Można następnie subskrybować zdarzenia, takie jak zmiany stanu zadania, za pomocą powiadomień, które łączyć bezpośrednio z systemu powiadomień Azure zdarzeń siatki. 

Ponieważ ten interfejs API jest wymuszany przez Menedżera zasobów Azure, można użyć szablonów usługi Resource Manager do tworzenia i wdrażania przekształcenia na koncie usługi Media Services. Kontrola dostępu oparta na rolach można również ustawić na poziomie zasobów, w tym interfejsie API, co umożliwia zablokowanie dostępu do określonych zasobów, takich jak transformacji.

## <a name="transform-definition"></a>Przekształć definicji

W poniższej tabeli przedstawiono właściwości przekształcenia oraz zapewnia ich definicje.

|Name (Nazwa)|Typ|Opis|
|---|---|---|
|Identyfikator|ciąg|Identyfikator FQDN zasobu dla zasobu.|
|name|ciąg|Nazwa zasobu.|
|Properties.created |ciąg|Data i Godzina UTC po przekształceniu został utworzony, w "RRRR-MM-Ddtgg" format.|
|Properties.Description |ciąg|Opcjonalny opis pełne przekształcenia.|
|properties.lastModified |ciąg|Data i Godzina UTC po przekształceniu ostatniej aktualizacji, w "RRRR-MM-Ddtgg" format.|
|Properties.outputs |[TransformOutput]|Tablica co najmniej jeden TransformOutputs generujących transformacji.|
|type|ciąg|Typ zasobu.|

Dla pełnej definicji [przekształca](https://docs.microsoft.com/rest/api/media/transforms).

## <a name="job-definition"></a>Definicja zadania

W poniższej tabeli przedstawiono właściwości zadania oraz zapewnia ich definicje.

|Name (Nazwa)|Typ|Opis|
|---|---|---|
|Identyfikator|ciąg|Identyfikator FQDN zasobu dla zasobu.|
|name|ciąg|Nazwa zasobu.|
|Properties.created |ciąg|Data i Godzina UTC po przekształceniu został utworzony, w "RRRR-MM-Ddtgg" format.|
|Properties.Description |ciąg|Opcjonalny opis pełne zadania.|
|properties.lastModified |ciąg|Data i Godzina UTC po przekształceniu ostatniej aktualizacji, w "RRRR-MM-Ddtgg" format.|
|Properties.outputs |[JobOutput]: [JobOutputAsset] |Dane wyjściowe zadania.|
|Properties.Priority |Priorytet |Priorytet, z którym zadanie powinno być przetworzone. Zadania o wyższym priorytecie są przetwarzane przed zadania o niższym priorytecie. Jeśli nie jest ustawiona, wartość domyślna to normalne.
|Properties.state |JobState |Bieżący stan zadania.
|type|ciąg|Typ zasobu.|

Dla pełnej definicji [zadania](https://docs.microsoft.com/rest/api/media/jobs).

## <a name="typical-workflow-and-example"></a>Typowy przepływ pracy i przykład

1. Utwórz transformacji 
2. Przesyłanie zadań w tej transformacji. 
3. Lista transformacji 
4. Usuń transformacji, jeśli nie zamierzasz używać w przyszłości ten "przepisu". 

Załóżmy, że chcesz wyodrębnić pierwszej ramki pliki wideo jako obraz miniatury — przedstawiono kroki, które można wykonać: 

1. Zdefiniuj reguły przetwarzania — na przykład, należy użyć pierwszej ramki wideo jako miniatury 
2. Następnie dla każdego wideo, które jako dane wejściowe do usługi, można sprawdzić usługi: 
    1. Gdzie można znaleźć wideo, a 
    2. Miejsca do zapisywania danych wyjściowych — na przykład obraz miniatury 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przesyłanie strumieniowe plików wideo](stream-files-dotnet-quickstart.md)
