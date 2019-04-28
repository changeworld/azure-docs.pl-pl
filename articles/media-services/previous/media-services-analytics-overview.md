---
title: Analiza multimediów na platformie Media Services | Dokumentacja firmy Microsoft
description: Omówienie publicznej wersji zapoznawczej usługi Media Analytics to zbiór usług przetwarzania mowy i komputera na skalę przedsiębiorstwa, zgodność, bezpieczeństwo i globalny zasięg
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: aac9719f8d74c4b7bc283745ee2b8e01365a81f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245275"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Analiza multimediów na platformie Media Services 

## <a name="overview"></a>Omówienie
Więcej organizacji używasz wideo jako preferowany średni szkolenie pracowników, angażować swoich klientów i udokumentować funkcje biznesowe. Przetwarzanie zapewnia sposób przechowywania, przesyłanie strumieniowe i dostępu do tych duże pliki multimedialne w chmurze. Ale wraz z rozwojem firmy biblioteki zawartości filmu wideo muszą równie skutecznym sposobem przeprowadzanie analizy zawartości. 

Aby zaspokoić te potrzeby rosnący, usługi Azure Media Services oferuje analizy multimediów Azure. Analiza multimediów to kolekcja składników mowy i obrazu, które ułatwiają organizacjom i przedsiębiorstwom pozyskiwanie przydatnych informacji z posiadanych plików wideo. Utworzony za pomocą podstawowe składniki platformy Media Services, usługi Media Analytics może obsłużyć przetwarzanie na dużą skalę, od pierwszego dnia multimediów.

Za pomocą usługi Media Analytics deweloperom szybkie przenoszenie zaawansowane funkcje wideo w aplikacjach. Zapewnia środowiskach korporacyjnych przy użyciu pełnej skali, zgodność, bezpieczeństwo i globalny zasięg, o których wymagane przez duże organizacje.

Na poniższym diagramie przedstawiono usługi Media Analytics i inne główne elementy platformy Media Services. 

![Wideo na żądanie — przepływ pracy](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Procesory multimediów usługi Analiza multimediów tworzą pliki MP4 lub JSON. Procesor multimediów tworzy plik w formacie MP4, można pobrać progresywnie pliku. Jeśli procesor multimediów generuje plik w formacie JSON, możesz pobrać plik z magazynu obiektów Blob platformy Azure. 

## <a name="media-analytics-services"></a>Usługi analizy multimediów

### <a name="indexer"></a>Indeksator
Za pomocą usługi Azure Media Indexer, który ułatwia zawartości można wyszukiwać i generowanie napisów ścieżki. W porównaniu do poprzedniej wersji usługi Azure Media Indexer 2 w wersji zapoznawczej ma języka szybciej indeksowania i szerszej pomocy technicznej. Obsługiwane języki angielski, hiszpański, francuski, niemiecki, włoski, chiński, Portugalskiej i arabskim. Aby uzyskać szczegółowe informacje i przykłady, zobacz [przetwarzanie plików wideo za pomocą usługi Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="motion-detector"></a>Wykrywanie ruchu
Wykrywanie ruchu służy do wykrywania ruchu w wideo z nieruchomym. Umożliwia pod kątem wyników fałszywie dodatnich zdarzeń ruchu wykrywane przez kamery nadzoru. Aby uzyskać szczegółowe informacje i przykłady, zobacz [wykrywanie na potrzeby analizy multimediów Azure ruchu](media-services-motion-detection.md).
### <a name="face-detector"></a>Wykrywanie twarzy
Za pomocą wykrywanie twarzy, można wykryć osób twarzy i emocji, w tym szczęście, smutek i zaskoczenie. Ma to kilka przydatne w branży aplikacji, opisanym w dalszej części w tym agregowania i jednoczesnej reakcje uczestników zdarzenia. Aby uzyskać szczegółowe informacje i przykłady, zobacz [wykrywanie twarzy i emocji na potrzeby analizy multimediów Azure](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Podsumowanie wideo
Podsumowanie wideo może pomóc w tworzenie podsumowań długich wideo, automatycznie wybierając interesujący fragmenty kodu z źródłowy plik wideo. Ta możliwość jest przydatne, gdy chcesz zapewnić szybki przegląd czego można oczekiwać w długich wideo. Aby uzyskać szczegółowe informacje i przykłady, zobacz [miniatur wideo multimediów Azure Użyj do utworzenia podsumowanie wideo](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optyczne rozpoznawanie znaków
Za pomocą usługi Azure Media optyczne rozpoznawanie znaków (OCR) zawartości tekstowej w plikach wideo można przekonwertować na tekst cyfrowy można edytować, którą można przeszukiwać. Następnie można zautomatyzować ekstrakcji istotne metadane z multimediów sygnału wideo.
### <a name="scalable-face-redaction"></a>Pomocą redakcji twarzy skalowalne
Usługa Azure Media Redactor to procesor multimediów usługi Media Analytics, który oferuje pomocą redakcji twarzy skalowalny w chmurze. Za pomocą redakcji twarzy, można zmodyfikować filmu wideo, aby Rozmycie twarze wybranych osób. Można korzystać z niej redakcji twarzy w mediami lub gdy uczestniczy bezpieczeństwa publicznego. Kilka minut materiał, który zawiera wiele powierzchni może zająć godzin redagowanie ręcznie, ale w tej usłudze pomocą redakcji twarzy trwa zaledwie kilku prostych krokach. Aby uzyskać więcej informacji, zobacz [redagowanie twarze za pomocą usługi Azure Media Analytics](media-services-face-redaction.md) artykułu.
### <a name="content-moderation"></a>Moderowanie zawartości
Usługa Azure Content Moderator pozwala na potrzeby wspomagane maszynowo Moderowanie filmów wideo. Na przykład chcesz wykrywać potencjalną zawartość dla dorosłych i nieodpowiednią w filmach wideo oraz przesyłać zawartość oznaczoną flagą do przejrzenia przez zespoły ds. moderowania obsługiwanego przez ludzi. Ręcznie Moderowanie filmów wideo niepożądanych zawartości to zadania czasochłonne i kosztowne. Z usługą i narzędziom do przeglądu skojarzonych możesz połączyć wspomaganego maszynowo moderowania możliwości człowieka w pętli, aby uzyskać najlepsze wyniki wydajnie i ekonomicznie. Aby dowiedzieć się więcej, zobacz [przetwarzać pliki wideo przy użyciu usługi Azure Content Moderator](media-services-content-moderation.md) artykułu.

## <a name="common-scenarios"></a>Typowe scenariusze
Analiza multimediów może pomóc organizacjom i przedsiębiorstwom gromadzić nowe analizy z wideo więcej wydajne zarządzanie dużymi ilościami zawartości wideo. Poniżej przedstawiono kilka scenariuszy:

* **Wywołaj centra**. Nawet w przypadku pojawienie się mediów społecznościowych centra wywołania klienta nadal ułatwić znaczną część transakcji obsługi klienta. Zakodowane w tym dane audio jest dużych ilości informacji klientów, które mogą być analizowane w celu osiągnięcia wyższej zadowolenie klientów. Za pomocą Media Indexer, organizacje mogą wyodrębniania tekstu i twórz indeksy wyszukiwania i pulpity nawigacyjne. Następnie mogli wyodrębnić analizy dotyczące typowych utrudnień, źródeł skarg i inne odpowiednie dane.
* **Moderowanie zawartości wygenerowanej przez użytkowników**. Z mediami gniazda do działów policji, w wielu organizacjach portale publicznych, które zaakceptować wygenerowaną przez użytkowników nośników, takich jak filmy wideo i obrazy. Ilość zawartości można skacze z powodu nieoczekiwanych zdarzeń. W tych scenariuszach jest trudne do przeprowadzenia skuteczne przeglądy ręczne zawartości dla właściwości. Klienci mogą polegać na usługę moderowanie zawartości, aby skupić się na treści, która jest odpowiednia.
* **Nadzór**. Za pomocą wzrost użycia kamer IP pochodzą rosnącą spisu nadzoru wideo. Ręcznie przeglądanie nadzoru wideo jest intensywnie i podatne na błędy człowieka. Usługa Media Analytics zapewnia usług, takich jak wykrywanie ruchu, wykrywanie twarzy i przyspieszone ujęcia Poklatkowe w celu uproszczenia procesu recenzowania, zarządzania i tworzenie pochodnych łatwiejsze.

## <a name="media-analytics-media-processors"></a>Procesory multimediów usługi analizy multimediów
W tej sekcji wymieniono procesory multimediów usługi analizy multimediów i pokazuje, jak użyć .NET lub REST, aby uzyskać obiekt multimedialny procesora (MP).

### <a name="mp-names"></a>Nazwy pakietu administracyjnego
* Azure Media Indexer 2 w wersji zapoznawczej
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
Następującą funkcję, ma jedną z określonej nazwy pakietu administracyjnego i zwraca obiekt pakietu administracyjnego.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
Żądanie:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

Odpowiedź:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Wersje demonstracyjne
Zobacz [pokazy analizy multimediów Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
Zobacz [ogłoszenie analizy usługi multimediów](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
