---
title: Przekształca i zadania w usługach multimedialnych
titleSuffix: Azure Media Services
description: Dowiedz się, jak utworzyć transformacje, aby opisać reguły przetwarzania klipów wideo w usłudze Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571239"
---
# <a name="transforms-and-jobs-in-media-services"></a>Przekształca i zadania w usługach multimedialnych

Ten temat zawiera szczegółowe informacje na temat [przekształceń](https://docs.microsoft.com/rest/api/media/transforms) i [zadań](https://docs.microsoft.com/rest/api/media/jobs) i wyjaśnia relację między tymi jednostkami.

## <a name="overview"></a>Omówienie

### <a name="transformsjobs-workflow"></a>Przepływ pracy Przekształcenia/Zadania

Na poniższym diagramie przedstawiono przepływ pracy przekształceń/zadań:

![Przekształcania i zadania przepływu pracy w usłudze Azure Media Services](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Typowy przepływ pracy

1. Tworzenie transformacji.
2. Prześlij zadania w ramach tego przekształcenia.
3. Przekształcenia listy.
4. Usuń transformację, jeśli nie planujesz go używać w przyszłości.

#### <a name="example"></a>Przykład

Załóżmy, że chcesz wyodrębnić pierwszą klatkę wszystkich filmów jako obraz miniatury — kroki, które należy wykonać, to:

1. Zdefiniuj przepis lub regułę przetwarzania filmów: "użyj pierwszej klatki filmu jako miniatury".
2. Dla każdego filmu, należy poinformować usługę:
    1. Gdzie znaleźć ten film.
    2. Gdzie zapisać obraz miniatury wyjściowej.

**Transformacja** pomaga utworzyć przepis raz (krok 1) i przesłać zadania przy użyciu tego przepisu (Krok 2).

> [!NOTE]
> Właściwości **przekształcania** i **zadania** typu Datetime są zawsze w formacie UTC.

## <a name="transforms"></a>Przekształcenia

Użyj **przekształceń,** aby skonfigurować typowe zadania kodowania lub analizowania klipów wideo. Każda **transformacja** opisuje przepis lub przepływ pracy zadań przetwarzania plików wideo lub audio. Pojedynczy transformator może zastosować więcej niż jedną regułę. Na przykład przekształcenie można określić, że każdy film wideo mają być zakodowane w pliku MP4 z określoną szybkością transmisji bitów i że obraz miniatury są generowane z pierwszej klatki wideo. Należy dodać jeden wpis TransformOutput dla każdej reguły, które chcesz uwzględnić w transformacji. Ustawienia predefiniowane służy do informowania transformuj sposób przetwarzania wejściowych plików multimedialnych.

### <a name="viewing-schema"></a>Wyświetlanie schematu

W usłudze Media Services w wersji 3 ustawienia wstępne są silnie typizowanymi jednostkami w samym interfejsie API. Definicję "schematu" dla tych obiektów można znaleźć w [specyfikacji otwartego interfejsu API (lub Swagger).](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) Można również wyświetlić wstępnie ustawione definicje (takie jak **StandardEncoderPreset)** w [interfejsie API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)lub innej dokumentacji referencyjnej SDK zestawu SDK usług Media Services w wersji 3.

### <a name="creating-transforms"></a>Tworzenie przekształceń

Transformacje można tworzyć przy użyciu REST, CLI lub dowolnego z opublikowanych SDK. Interfejs API usługi Media Services w wersji 3 jest sterowany przez usługę Azure Resource Manager, dzięki czemu można również użyć szablonów Menedżera zasobów do tworzenia i wdrażania przekształceń na koncie usługi Media Services. Kontrola dostępu oparta na rolach może służyć do blokowania dostępu do przekształceń.

### <a name="updating-transforms"></a>Aktualizowanie przekształceń

Jeśli chcesz zaktualizować [transformację,](https://docs.microsoft.com/rest/api/media/transforms)użyj operacji **Aktualizuj.** Jest przeznaczony do wprowadzania zmian w opisie lub priorytetów podstawowych TransformOutputs. Zaleca się, aby takie aktualizacje były wykonywane po zakończeniu wszystkich zadań w toku. Jeśli zamierzasz przepisać przepis, musisz utworzyć nowy transform.

### <a name="transform-object-diagram"></a>Przekształcanie diagramu obiektów

Na poniższym diagramie przedstawiono **Transform** obiektu i obiektów, do których odwołuje się, w tym relacje pochodne. Szare strzałki pokazują typ, dla którego odwołania do zadania i zielone strzałki pokazują relacje pochodne klasy.

Wybierz obraz, aby wyświetlić go w pełnym rozmiarze.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>Stanowiska

Zadanie **Job** to rzeczywiste żądanie do usługi Media Services, aby zastosować **przekształcenie** do danej wejściowej zawartości wideo lub audio. Po utworzeniu transformu można przesyłać zadania przy użyciu interfejsów API usługi Media Services lub dowolnych opublikowanych sks. **Zadanie** określa informacje, takie jak lokalizacja wejściowego wideo i lokalizacja danych wyjściowych. Lokalizację wejściowego wideo można określić za pomocą: adresów URL HTTPS, adresów URL [sygnatury](https://docs.microsoft.com/rest/api/media/assets)dostępu Współdzielonego lub zasobów .  

### <a name="job-input-from-https"></a>Wprowadzanie zadania z protokołu HTTPS

Użyj [danych wejściowych zadania z protokołu HTTPS,](job-input-from-http-how-to.md) jeśli zawartość jest już dostępna za pośrednictwem adresu URL i nie trzeba przechowywać pliku źródłowego na platformie Azure (na przykład importuj z S3). Ta metoda jest również odpowiednia, jeśli masz zawartość w magazynie obiektów Blob platformy Azure, ale nie ma potrzeby, aby plik był w zasobie. Obecnie ta metoda obsługuje tylko jeden plik do wprowadzania danych.

### <a name="asset-as-job-input"></a>Zasób jako dane wejściowe zadania

Użyj [zasobu jako wprowadzania zadania,](job-input-from-local-file-how-to.md) jeśli zawartość wejściowa znajduje się już w zasobie lub zawartość jest przechowywana w pliku lokalnym. Jest to również dobra opcja, jeśli planujesz opublikować zasób wejściowy do przesyłania strumieniowego lub pobierania (powiedzmy, że chcesz opublikować mp4 do pobrania, ale także chcesz zrobić mowy do tekstu lub wykrywania twarzy). Ta metoda obsługuje zasoby wielokońcowe (na przykład zestawy przesyłania strumieniowego MBR, które zostały zakodowane lokalnie).

### <a name="checking-job-progress"></a>Sprawdzanie postępu zadania

Postęp i stan zadań można uzyskać, monitorując zdarzenia za pomocą usługi Event Grid. Aby uzyskać więcej informacji, zobacz [Monitorowanie zdarzeń przy użyciu programu EventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Aktualizowanie zadań

Operacja Aktualizuj w encji [Zadanie](https://docs.microsoft.com/rest/api/media/jobs) może służyć do modyfikowania *opisu* i właściwości *priorytetu* po przesłaniu zadania. Zmiana właściwości *priority* jest skuteczna tylko wtedy, gdy zadanie jest nadal w stanie kolejki. Jeśli zadanie rozpoczęło przetwarzanie lub zostało zakończone, zmiana priorytetu nie ma wpływu.

### <a name="job-object-diagram"></a>Diagram obiektów zadania

Na poniższym diagramie przedstawiono **Job** obiektu i obiektów, do których odwołuje się, w tym relacje pochodne.

Kliknij obraz, aby go wyświetlić w pełnym rozmiarze.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>Konfigurowanie jednostek zarezerwowanych multimediów

W przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez usługi Media Services w wersji 3 lub indeksator wideo, zdecydowanie zaleca się aprowizowania konta za pomocą 10 jednostek zarezerwowanych nośników S3 (MRU). Jeśli potrzebujesz więcej niż 10 mru S3, otwórz bilet pomocy technicznej za pomocą [witryny Azure portal](https://portal.azure.com/).

Aby uzyskać szczegółowe informacje, zobacz [Skalowanie przetwarzania multimediów za pomocą interfejsu wiersza polecenia](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="see-also"></a>Zobacz też

* [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtrowanie, zamawianie, stronicowanie encji usługi Media Services](entities-overview.md)

## <a name="next-steps"></a>Następne kroki

- Przed rozpoczęciem tworzenia, przegląd [tworzenie za pomocą interfejsów API usługi Media Services w wersji 3](media-services-apis-overview.md) (zawiera informacje na temat uzyskiwania dostępu do interfejsów API, konwencje nazewnictwa, itp.)
- Sprawdź te tutoriale:

    - [Samouczek: Kodowanie zdalnego pliku na podstawie adresu URL i przesyłanie strumieniowe wideo](stream-files-tutorial-with-rest.md)
    - [Samouczek: Przesyłanie, kodowanie i przesyłanie strumieniowe filmów](stream-files-tutorial-with-api.md)
    - [Samouczek: Analizowanie klipów wideo za pomocą usługi Media Services w wersji 3](analyze-videos-tutorial-with-api.md)
