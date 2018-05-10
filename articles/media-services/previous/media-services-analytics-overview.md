---
title: Analiza multimediów na platformie Media Services | Dokumentacja firmy Microsoft
description: Omówienie publicznej wersji zapoznawczej z analizy multimediów, Kolekcja usług wizji mowy i komputera w skali przedsiębiorstwa, zgodności i zabezpieczeń oraz globalne
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2017
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: 0ac8b9fad35267ceaec5b5acec4722b6005f68a9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="media-analytics-on-the-media-services-platform"></a>Analiza multimediów na platformie Media Services
## <a name="overview"></a>Przegląd
Więcej organizacji korzystają wideo jako preferowany średni uczenia pracownikom, zaangażowania klientów i udokumentować znaczenie biznesowe. Przetwarzania umożliwia przechowywanie, strumienia i dostępu do tych duże pliki multimedialne w chmurze. Jednak wraz z rozwojem firmy biblioteki zawartości wideo, musi on równie skuteczne środki wyodrębniania wgląd w zawartość. 

Aby rozwiązać ten wzrasta potrzeba, usługi Azure Media Services udostępnia analizy multimediów Azure. Analiza multimediów to kolekcja składników mowy i obrazu, które ułatwiają organizacjom i przedsiębiorstwom pozyskiwanie przydatnych informacji z posiadanych plików wideo. Utworzony przy użyciu podstawowe składniki platformy Media Services, analiza multimediów może obsługiwać nośnika przetwarzania na dużą skalę na jeden dzień.

Z analizy multimediów deweloperzy mogą szybko Przełącz zaawansowane funkcje wideo do aplikacji. Przedsiębiorstw zapewnia skali, zgodności, zabezpieczeń oraz globalne wymagane przez dużych organizacji.

Na poniższym diagramie przedstawiono analizy multimediów i inne główne elementy platformy Media Services. 

![Wideo na żądanie — przepływ pracy](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Procesory multimediów usługi Analiza multimediów tworzą pliki MP4 lub JSON. Jeśli procesor multimediów tworzy plik MP4, można pobrać progresywnie pliku. Jeśli procesor multimediów powoduje utworzenie pliku JSON, możesz pobrać plik z magazynu obiektów Blob platformy Azure. 

## <a name="media-analytics-services"></a>Usług analizy multimediów

### <a name="indexer"></a>Indeksator
Z usługi Azure Media indeksatora możesz wprowadzić zawartości można wyszukiwać i generować-kodowane ścieżki. W porównaniu do poprzednich wersji Preview 2 indeksatora multimediów Azure ma szybciej indeksowania i szerszych języka obsługuje. Obsługiwane języki angielski, hiszpański, francuski, niemiecki, włoski, chiński, portugalski i arabski. Aby uzyskać szczegółowe informacje i przykłady, zobacz [przetwarzanie plików wideo z usługi Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="hyperlapse"></a>Hyperlapse
Microsoft Hyperlapse łączy stabilizacji wideo i ujęć poklatkowych możliwości tworzenia szybkie, eksploatacyjny filmy wideo z zawartości długiej. Oprócz tworzenia ujęć poklatkowych wideo, przyspieszonych ujęć poklatkowych służy również do tworzenia stabilna filmy wideo z obraz wideo przechwycone przez telefony komórkowe i kamery. Aby uzyskać szczegółowe informacje i przykłady, zobacz [przyspieszonych ujęć poklatkowych plików multimedialnych na pliki z usługi Azure Media Hyperlapse](media-services-hyperlapse-content.md).
### <a name="motion-detector"></a>Wykrywanie ruchu
Czujnik ruchu można użyć do wykrywania ruchu na obrazie wideo z nieruchomy tła. Dzięki temu można wyszukać fałszywych alarmów zdarzeń ruchu wykrywane przez kamer nadzoru. Aby uzyskać szczegółowe informacje i przykłady, zobacz [wykrywanie na potrzeby analizy multimediów Azure ruchu](media-services-motion-detection.md).
### <a name="face-detector"></a>Wykrywanie twarzy
Przy użyciu detektora krój, można wykryć powierzchni użytkowników i ich emocji, w tym szczęście, sadness i niespodziewanego. To ma kilka przydatne branży aplikacji, w dalszej części, tym agregowanie i analizowanie danych reakcji uczestników zdarzenia. Aby uzyskać szczegółowe informacje i przykłady, zobacz [wykrywania twarzy na obrazie i emocji dla analizy multimediów Azure](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Podsumowanie wideo
Podsumowanie wideo mogą pomóc tworzyć podsumowania długich filmów wideo, wybierając automatycznie interesujące fragmenty kodu ze źródła wideo. Ta możliwość jest przydatne, gdy chcesz zapewnić szybki przegląd czego można oczekiwać w długich wideo. Aby uzyskać szczegółowe informacje i przykłady, zobacz [miniatur wideo multimediów Azure Użyj utworzyć podsumowanie wideo](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optyczne rozpoznawanie znaków
Z usługi Azure Media Rozpoznawania (OCR) zawartość tekstu w plikach wideo można przekonwertować na tekst cyfrowy można edytować, wyszukiwanie. Następnie można zautomatyzować wyodrębniania łatwy do rozpoznania metadanych z sygnału wideo multimediów.
### <a name="scalable-face-redaction"></a>Skalowalna krój redakcyjne
Azure Media Redactor jest procesora nośnika analizy multimediów, które oferuje skalowalne krój redakcyjne w chmurze. Za pomocą redakcyjne krój, można zmodyfikować wideo do rozmycia kroje wybrane osoby. Można korzystać z usługi redakcyjne krój, na nośniku wiadomości lub uczestniczy bezpieczeństwa publicznego. Kilka minut najmniejszym zawiera wiele kroje może zająć godzin redagowanie ręcznie, ale z tą usługą redakcyjne krój trwa tylko kilka prostych kroków. Aby uzyskać więcej informacji, zobacz [redagowanie krojów z analizy multimediów Azure](media-services-face-redaction.md) artykułu.
### <a name="content-moderation"></a>Moderowanie zawartości
Azure moderatora zawartości pozwala na użycie łagodzenia wspierana maszyny dla plików wideo. Na przykład można wykryć możliwe zawartość dla dorosłych i luksusowych w wideo i przejrzeć zawartość oflagowane przez zespoły łagodzenia człowieka. Ręcznie Moderowanie wideo niepożądanych zawartości jest czasochłonne i kosztowne zadania. Z tej usługi i przejrzyj skojarzone narzędzia wydajne i ekonomiczne łączyć się przy pomocy maszyny łagodzenia o możliwości ludzkich w pętli, aby uzyskać najlepsze wyniki. Aby dowiedzieć się więcej, zobacz [przetwarzać pliki wideo z moderatora zawartości Azure](media-services-content-moderation.md) artykułu.

## <a name="common-scenarios"></a>Typowe scenariusze
Analiza multimediów może ułatwić organizacjom i przedsiębiorstwom zgromadzonych nowych danych z wideo i więcej efektywnie zarządzać dużych ilości zawartości wideo. Poniżej przedstawiono kilka scenariuszy:

* **Wywołaj centrów**. Nawet w przypadku pojawienie się mediów społecznościowych odbiorcy biura obsługi ułatwienia nadal znaczną część transakcji obsługi klienta. Zakodowane w tych danych audio jest duża ilość informacji klienta, który można przeprowadzić analizę w celu osiągnięcia wyższej zadowolenia klienta. Przy użyciu nośnika indeksatora, organizacje można wyodrębnić tekst i twórz indeksów wyszukiwania i pulpity nawigacyjne. Następnie mogli wyodrębnić informacji dotyczących typowych utrudnień, źródeł utrudnień i inne odpowiednie dane.
* **Łagodzenie zawartość wygenerowaną przez użytkowników**. Z nośnika wiadomości gniazda do działów polecenie w wielu organizacjach istnieją portale publicznych, które akceptują wygenerowaną przez użytkowników nośniki, takie jak pliki wideo i obrazów. Wielkość zawartości można zwiększyć z powodu nieoczekiwanego zdarzenia. W tych scenariuszach jest trudne do przeprowadzenia skuteczne recenzje ręczne zawartości dla właściwości. Klienci mogą polegać na usługę łagodzenia zawartości, aby skupić się na zawartość, która jest odpowiednia.
* **Nadzoru**. Wzrost używany kamer IP pochodzą rosnącym spisu nadzoru wideo. Ręcznie recenzowania nadzoru wideo jest czas znacznym i podatne na błędy człowieka. Analiza multimediów udostępnia usługi, takie jak wykrywanie ruchu, wykrywania twarzy na obrazie i przyspieszonych ujęć poklatkowych w celu uproszczenia procesu przeglądu, zarządzania i tworzenie pochodnych łatwiejsze.

## <a name="media-analytics-media-processors"></a>Procesory multimediów usługi analiza multimediów
W tej sekcji wymieniono procesory multimediów usługi analiza multimediów i przedstawia sposób użycia platformy .NET lub REST można pobrać obiektu procesora (MP) nośnika.

### <a name="mp-names"></a>Nazwy pakietu administracyjnego
* Podgląd indeksatora 2 multimediów Azure
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media moderatora zawartości

### <a name="net"></a>.NET
Następująca funkcja przyjmuje jeden z określonych nazw pakietu administracyjnego i zwraca obiekt pakietu administracyjnego.

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

## <a name="demos"></a>Pokazy
Zobacz [pokazy analizy multimediów Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
Zobacz [anonsu analiza usługi multimediów](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
