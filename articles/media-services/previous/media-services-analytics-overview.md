---
title: Analiza multimediów na platformie Media Services | Dokumenty firmy Microsoft
description: Przegląd publicznej wersji zapoznawczej usługi Media Analytics, kolekcji usług rozpoznawania mowy i przetwarzania obrazów w skali przedsiębiorstwa, zgodności, bezpieczeństwa i globalnego zasięgu
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
ms.date: 11/13/2019
ms.author: juliako
ms.reviewer: milanga; johndeu
ms.openlocfilehash: ab1eba3de474d9ff985e62f491c24fa63be0fa63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069629"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Analiza multimediów na platformie Media Services 

## <a name="retirement-plans"></a>Plany emerytalne

> [!IMPORTANT]
> Niektóre procesory multimediów są wycofywane. Aby uzyskać daty wycofania i więcej informacji, zobacz temat [starszych składników.](legacy-components.md) 

## <a name="overview"></a>Omówienie

Więcej organizacji używa wideo jako preferowanego medium do szkolenia swoich pracowników, angażowania swoich klientów i dokumentowania funkcji biznesowych. Chmura obliczeniowa umożliwia przechowywanie, przesyłanie strumieniowe i uzyskiwanie dostępu do tych dużych plików multimedialnych. Jednak wraz z rozwojem biblioteki treści wideo firmy, potrzebuje ona równie skutecznych sposobów wyodrębniania informacji z treści. 

Aby zaspokoić tę rosnącą potrzebę, usługa Azure Media Services oferuje usługę Azure Media Analytics. Analiza multimediów to kolekcja składników mowy i obrazu, które ułatwiają organizacjom i przedsiębiorstwom pozyskiwanie przydatnych informacji z posiadanych plików wideo. Aplikacja Media Analytics, oparta na podstawowych składnikach platformy usługi Media Services, może obsługiwać przetwarzanie multimediów na dużą skalę pierwszego dnia.

Dzięki usłudze Media Analytics deweloperzy mogą szybko wprowadzać zaawansowane funkcje wideo do aplikacji. Zapewnia środowisko przedsiębiorstwa o pełnej skali, zgodności, bezpieczeństwie i globalnym zasięgu wymaganym przez duże organizacje.

Na poniższym diagramie przedstawiono usługi Media Analytics i inne główne części platformy usługi Media Services. 

![Wideo na żądanie — przepływ pracy](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Procesory multimediów usługi Analiza multimediów tworzą pliki MP4 lub JSON. Jeśli procesor multimediów tworzy plik MP4, można go stopniowo pobierać. Jeśli procesor multimediów tworzy plik JSON, można pobrać plik z magazynu obiektów Blob platformy Azure. 

## <a name="media-analytics-services"></a>Usługi analizy multimediów

### <a name="indexer"></a>Indeksator
Za pomocą usługi Azure Media Indexer można przeszukiwać zawartość i generować ścieżki podpisów kodowych. Aby uzyskać szczegółowe informacje i przykłady, zobacz [Indeksowanie plików multimedialnych za pomocą programu Azure Media Indexer](media-services-index-content.md).

### <a name="motion-detector"></a>Wykrywanie ruchu
Za pomocą motion detector można wykrywać ruch w filmie ze stacjonarnymi tłami. Umożliwia to sprawdzenie fałszywych alarmów zdarzeń ruchu wykrytych przez kamery monitoringu. Aby uzyskać szczegółowe informacje i przykłady, zobacz [Wykrywanie ruchu dla usługi Azure Media Analytics](media-services-motion-detection.md).

### <a name="face-detector"></a>Wykrywanie twarzy
Za pomocą Face Detector, można wykryć twarze ludzi i ich emocje, w tym szczęście, smutek i zaskoczenie. Ma to kilka przydatnych aplikacji branżowych, opisanych później, w tym agregowanie i analizowanie reakcji osób uczestniczących w wydarzeniu. Aby uzyskać szczegółowe informacje i przykłady, zobacz [Wykrywanie twarzy i emocji dla usługi Azure Media Analytics](media-services-face-and-emotion-detection.md).

### <a name="video-summarization"></a>Podsumowanie filmu
Podsumowanie wideo może pomóc w tworzeniu podsumowań długich filmów, automatycznie wybierając interesujące fragmenty z źródłowego filmu. Ta możliwość jest przydatna, gdy chcesz zapewnić szybki przegląd tego, czego można się spodziewać w długim filmie. Aby uzyskać szczegółowe informacje i przykłady, zobacz [Tworzenie podsumowania wideo za pomocą miniatur wideo w usłudze Azure Media Video](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optyczne rozpoznawanie znaków
Dzięki funkcji Azure Media OCR (optyczne rozpoznawanie znaków) można konwertować zawartość tekstową w plikach wideo na edytowalny, zbywalny tekst cyfrowy. Następnie można zautomatyzować wyodrębnianie znaczących metadanych z sygnału wideo multimediów.
### <a name="scalable-face-redaction"></a>Skalowalna redakcja twarzy
Usługa Azure Media Redactor to procesor multimediów usługi Media Analytics, który oferuje skalowalną redakcję twarzy w chmurze. Za pomocą funkcji do redakcji twarzy można zmodyfikować film, aby rozmyć twarze wybranych osób. Możesz chcieć korzystać z usługi redagowania twarzy w mediach informacyjnych lub w przypadku bezpieczeństwa publicznego. Kilka minut materiału, który zawiera wiele twarzy może potrwać wiele godzin, aby redagować ręcznie, ale dzięki tej usłudze, redakcja twarzy zajmuje tylko kilka prostych kroków. Aby uzyskać więcej informacji, zobacz [Redact twarze z usługi Azure Media Analytics](media-services-face-redaction.md) artykułu.

### <a name="content-moderation"></a>Moderowanie zawartości
Moderator zawartości platformy Azure umożliwia korzystanie z moderacji wspomaganej maszynowo dla twoich filmów. Na przykład chcesz wykrywać potencjalną zawartość dla dorosłych i nieodpowiednią w filmach wideo oraz przesyłać zawartość oznaczoną flagą do przejrzenia przez zespoły ds. moderowania obsługiwanego przez ludzi. Ręczne moderowanie filmów dla niepożądanych treści jest czasochłonnym i kosztownym zadaniem. Dzięki tej usłudze i powiązanym narzędziom do przeglądu można połączyć moderację wspomaganą maszynowo z możliwościami człowieka w pętli, aby uzyskać najlepsze wyniki wydajnie i ekonomicznie. Aby dowiedzieć się więcej, zobacz [artykuł Przetwarzanie klipów wideo za pomocą moderatora zawartości platformy Azure.](media-services-content-moderation.md)

## <a name="common-scenarios"></a>Typowe scenariusze
Analiza multimediów może pomóc organizacjom i przedsiębiorstwom w zbieraniu nowych informacji z wideo i skuteczniejszym zarządzaniu dużą ilością treści wideo. Oto kilka scenariuszy:

* **Centra wywoławcze**. Nawet wraz z pojawieniem się mediów społecznościowych, centra obsługi klienta nadal ułatwiają duży procent transakcji obsługi klienta. Zakodowane w tych danych audio jest duża ilość informacji o klientach, które mogą być analizowane w celu osiągnięcia większego zadowolenia klienta. Za pomocą indeksatora multimediów organizacje mogą wyodrębniać tekst i tworzyć indeksy wyszukiwania i pulpity nawigacyjne. Następnie mogą wyodrębnić informacje na temat typowych skarg, źródeł skarg i innych istotnych danych.
* **Moderowanie treści generowane przez użytkowników**. Od mediów informacyjnych po departamenty policji , wiele organizacji ma publiczne portale, które akceptują media generowane przez użytkowników, takie jak filmy i obrazy. Ilość zawartości może wzrosnąć z powodu nieoczekiwanych zdarzeń. W tych scenariuszach trudno jest przeprowadzić skuteczne ręczne przeglądy treści pod kątem stosowności. Klienci mogą polegać na usłudze moderowania zawartości, aby skupić się na odpowiedniej zawartości.

## <a name="media-analytics-media-processors"></a>Procesory multimediów Media Analytics
W tej sekcji wymieniono procesory multimediów usługi Media Analytics i pokazano, jak uzyskać obiekt procesora multimediów (MP) za pomocą funkcji .NET lub REST.

### <a name="mp-names"></a>Nazwy MP

* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Moderator zawartości multimediów platformy Azure

### <a name="net"></a>.NET
Następująca funkcja przyjmuje jedną z określonych nazw MP i zwraca obiekt MP.

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
    x-ms-version: 2.19
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

## <a name="demos"></a>Dema
Zobacz [prezentacje usługi Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
Zobacz [Anons usługi Media Services Analytics](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
