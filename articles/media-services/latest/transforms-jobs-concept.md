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
ms.date: 04/29/2019
ms.author: juliako
ms.openlocfilehash: 3c3687ceff10baec028435d1e6c513e72ca5da86
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149081"
---
# <a name="transforms-and-jobs"></a>Przekształcenia i zadania

Ten temat zawiera szczegółowe informacje o [przekształca](https://docs.microsoft.com/rest/api/media/transforms) i [zadania](https://docs.microsoft.com/rest/api/media/jobs) i opisano relację między tymi jednostkami. 

## <a name="overview"></a>Omówienie 

### <a name="transformsjobs-workflow"></a>Przekształcenia/zadania przepływu pracy

Na poniższym diagramie przedstawiono przekształcenia/zadania przepływu pracy.

![Przekształcenia](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Typowy przepływ pracy

1. Tworzenie przekształcenia 
2. Przesyłanie zadań w ramach tej transformacji 
3. Lista przekształceń 
4. Usuń przekształcenie, jeśli nie zamierzasz używać go w przyszłości. 

#### <a name="example"></a>Przykład

Załóżmy, że chcesz wyodrębnić pierwszej ramki wszystkie filmy wideo dotyczące jako obraz miniatury — są kroki, które można wykonać: 

1. Zdefiniuj przepisu lub reguły dla przetwarzania plików wideo — "jako miniatury przy użyciu pierwszej ramki wideo". 
2. Dla każdego pliku wideo może poinformować usługi: 
    1. Gdzie można znaleźć tego wideo  
    2. Gdzie należy zapisać miniaturę danych wyjściowych. 

A **Przekształcanie** ułatwia tworzenie przepisu raz (krok 1) i przesyłanie zadań za pomocą tego przepisu (krok 2).

> [!NOTE]
> Właściwości **Przekształcanie** i **zadania** będące daty/godziny są zawsze w formacie UTC.

## <a name="transforms"></a>Przekształcenia

Na poniższym diagramie przedstawiono **Przekształcanie** obiektu i obiektów, odwołuje się do, w tym relacje pochodnym. Szara strzałki oznaczają typ, czy odwołania zadania i zielone strzałki pokazują relacje tworzenie wartości pochodnych klas.<br/>Kliknij obraz, aby go wyświetlić w pełnym rozmiarze.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

Użyj **przekształca** skonfigurować typowych zadań związanych z kodowaniem lub analizowanie filmów wideo. Każdy **Przekształcanie** opisuje młyna lub przepływu pracy zadań przetwarzania plików wideo lub audio. Pojedynczy przekształcenia można zastosować więcej niż jedną regułę. Przekształcenie można na przykład określić, że każdy plik wideo można zakodowany w postaci pliku MP4 o danej szybkości transmisji bitów i wygenerowania obraz miniatury z pierwszej ramki filmu wideo. Należy dodać jeden wpis TransformOutput dla każdej reguły, które chcesz uwzględnić w swojej transformacji. Przekształcenia można tworzyć w ramach konta usługi Media Services przy użyciu interfejsu API usługi Media Services v3 lub przy użyciu dowolnej z opublikowanych zestawów SDK. Przekształca Media Services v3, które interfejs API jest wymuszany przez usługę Azure Resource Manager, dzięki czemu można również użyć szablonów usługi Resource Manager do tworzenia i wdrażania w ramach konta usługi Media Services. Kontrola dostępu oparta na rolach może służyć do blokowania dostępu do przekształcenia.

Operacja aktualizacji na [Przekształcanie](https://docs.microsoft.com/rest/api/media/transforms) jednostki jest przeznaczony do wprowadzania zmian w lub priorytety TransformOutputs podstawowy opis. Zaleca się, że takie aktualizacje można wykonać po zakończeniu wszystkich zadań w toku. Jeśli planujesz ponowne zapisywanie adresów przepisu, musisz utworzyć nowe przekształcenie.

## <a name="jobs"></a>Stanowiska

Na poniższym diagramie przedstawiono **zadania** obiektu i obiektów, odwołuje się do, w tym relacje pochodnym.<br/>Kliknij obraz, aby go wyświetlić w pełnym rozmiarze.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

A **zadania** jest rzeczywistego żądania do usługi Azure Media Services, aby zastosować **Przekształcanie** do danego wejściowego zawartości wideo lub audio. Po utworzeniu przekształcenia można przesłać zadania przy użyciu interfejsów API usług Media Services lub dowolny z opublikowanych zestawów SDK. **Zadania** określa informacje, takie jak lokalizacja wejściowych plików wideo i lokalizację danych wyjściowych. Można określić lokalizację je wideo przy użyciu: Adresy URL HTTPS, adresów URL sygnatury dostępu Współdzielonego, lub [zasoby](https://docs.microsoft.com/rest/api/media/assets).  

Użyj [zadania danych wejściowych z protokołu HTTPS](job-input-from-http-how-to.md) Jeśli zawartość jest już dostępna za pośrednictwem adresu URL i nie trzeba przechowywać plik źródłowy na platformie Azure (na przykład zaimportować z S3). Ta metoda jest przydatna również w przypadku, jeśli masz zawartość w usłudze Azure Blob storage, ale nie potrzebujesz pliku do elementu zawartości. Ta metoda obsługuje obecnie tylko pojedynczy plik na dane wejściowe.
 
Użyj [zasobów jako dane wejściowe zadania](job-input-from-local-file-how-to.md) zawartość danych wejściowych jest już w zasobie lub zawartość jest przechowywana w pliku lokalnym. Jest to również to dobry wybór jeśli planujesz publikować wejściowego elementu dla przesyłania strumieniowego lub pobierania (Załóżmy, że chcesz opublikować do pobrania w formacie mp4, ale także w celu zamiany mowy na tekst lub na liście twarzy, wykrywanie). Ta metoda obsługuje wielu plików zasobów (na przykład MBR przesyłanie strumieniowe zestawów, które zostały zakodowane lokalnie).
 
Postęp i stan zadania można uzyskać poprzez monitorowanie zdarzeń za pomocą usługi Event Grid. Aby uzyskać więcej informacji, zobacz [monitorowania zdarzeń za pomocą EventGrid](job-state-events-cli-how-to.md).

Operacja aktualizacji na [zadania](https://docs.microsoft.com/rest/api/media/jobs) jednostki może służyć do modyfikowania *opis*i *priorytet* właściwości po przesłaniu zadania. Zmiana *priorytet* właściwość jest efektywne tylko wtedy, gdy zadanie jest nadal w stanie umieszczenia w kolejce. Jeśli zadanie rozpoczął przetwarzanie lub zostało zakończone, zmiana priorytetu nie ma wpływu.

## <a name="configure-media-reserved-units"></a>Konfigurowanie jednostki zarezerwowane multimediów

Analiza Audio i wideo zadania analizy, które są wyzwalane przez usługi Media Services v3 lub Video Indexer, aby uzyskać zaleca aprowizację swojego konta za pomocą 10 jednostek zarezerwowanych multimediów S3 (lokalizacje MRU). Jeśli potrzebujesz więcej niż 10 S3 lokalizacje MRU, otwórz bilet pomocy technicznej przy użyciu [witryny Azure portal](https://portal.azure.com/).

Aby uzyskać więcej informacji, zobacz [skalowanie przetwarzania multimediów z użyciem interfejsu wiersza polecenia](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Prześlij opinię i pobieranie aktualizacji

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="see-also"></a>Zobacz także

* [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Stronicowanie filtrowania, sortowania, jednostek usługi Media Services](entities-overview.md)

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Przekazywanie, kodowanie i przesyłanie strumieniowe wideo za pomocą platformy .NET](stream-files-tutorial-with-api.md)
- [Samouczek: Analizowanie klipów wideo za pomocą usługi Media Services v3 przy użyciu platformy .NET](analyze-videos-tutorial-with-api.md)
