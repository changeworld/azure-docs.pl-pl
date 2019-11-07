---
title: Tworzenie klipów za pomocą usługi Azure Media Clipper | Microsoft Docs
description: Omówienie usługi Azure Media Clipper — narzędzia do tworzenia klipów multimedialnych z zasobów
services: media-services
keywords: Clip; subclip; kodowanie; nośnik
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51f85dffd48e451b477018ef20491f8619a30f25
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685016"
---
# <a name="create-clips-with-azure-media-clipper"></a>Tworzenie klipów za pomocą usługi Azure Media Clipper 

Azure Media Clipper to bezpłatna biblioteka języka JavaScript, która umożliwia deweloperom sieci Web udostępnianie użytkownikom interfejsu do tworzenia klipów multimedialnych. To narzędzie można zintegrować z dowolną stroną sieci Web i dostarcza interfejsy API do ładowania zasobów i przesyłania wycinków zadań.

Usługa Azure Media Clipper umożliwia:
- Przytnij przedplanszowe i końcowe z archiwów na żywo 
- Twórz fragmenty wideo ze zdarzeń na żywo, archiwów na żywo lub plików VOD fMP4 
- Łączenie filmów wideo z wielu źródeł 
- Tworzenie klipów podsumowujących z zasobów multimediów usługi AMS 
- Klip wideo z dokładnością do ramek 
- Generuj filtry manifestu dynamicznego dla istniejących elementów zawartości na żywo i VOD z dokładnością do grup obrazów (grupę GOP) 
- Tworzenie zadań kodowania względem zasobów na koncie usługi Media Services

Aby zażądać nowych funkcji, podaj pomysły lub opinie, Prześlij do witryny [UserVoice, aby uzyskać Azure Media Services](https://aka.ms/amsvoice/). W przypadku wystąpienia i określonych problemów, pytań lub wyszukania błędów Porzuć zespół Media Services linię w amcinfo@microsoft.com.

Na poniższej ilustracji przedstawiono interfejs Clipper: ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Informacje o wersji
Na poniższej liście znajduje się wpis w blogu dla programu Clipper, różne znane problemy i dziennik zmian dla najnowszej wersji programu Clipper:
- [Wpis w blogu](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Lista znanych problemów](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Dziennika zmian](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Obsługa przeglądarki
Usługa Azure Media Clipper została skompilowana przy użyciu nowoczesnych technologii HTML5 i obsługuje następujące przeglądarki:

- Microsoft Edge 13 +
- Internet Explorer 11 +
- Chrome 54 +
- Przeglądarka Safari 10 +
- Firefox 50 +

> [!NOTE]
> Obecnie obsługiwane jest tylko odtwarzanie w języku HTML5 strumieni z Azure Media Services.

## <a name="language-support"></a>Obsługa języków
Widżet Clipper jest dostępny w następujących 18 językach:
- Chiński (uproszczony)
- Chiński (tradycyjny)
- Czeski
- Holenderski, Flemish
- Polski
- Francuski
- Niemiecki
- Węgierski
- Włoski
- Japoński
- Koreański
- Polski
- Portugalski (Brazylia)
- Portugalski (Portugalia)
- Rosyjski
- Hiszpański
- Szwedzki
- Turecki

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć korzystanie z usługi Azure Media Clipper, przeczytaj artykuł [wprowadzenie](media-services-azure-media-clipper-getting-started.md) , aby uzyskać szczegółowe informacje na temat sposobu wdrażania widżetu.
