---
title: Twórz klipy z usługi Azure Media Clipper | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Media Clipper, narzędzia do tworzenia nośnika klipy z zasobów
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 35f1f359b44af00000ccd9047673b80ca541d376
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61243884"
---
# <a name="create-clips-with-azure-media-clipper"></a>Twórz klipy z usługi Azure Media Clipper 

Usługa Azure Media Clipper jest bezpłatne biblioteki JavaScript, która umożliwia projektantom sieci web użytkownikom ich z interfejsem do tworzenia klipów multimedialnych. To narzędzie może zostać włączone do dowolnej strony sieci web i udostępnia interfejsy API do ładowania zasobów i przesyłanie zadań przycinania.

Usługa Azure Media Clipper umożliwia:
- TRIM plansz przed i po planszy z na żywo archiwa 
- Redagowanie wideo najważniejsze informacje z wydarzeń na żywo usługi AMS, archiwów na żywo lub pliki wideo na żądanie fMP4 
- Łączenie filmy wideo z wielu źródeł 
- Tworzy podsumowanie klipy z zasobów multimedialnych usługi AMS 
- Klip wideo z dokładności ramce 
- Generowanie filtry manifestów dynamicznych istniejące na żywo oraz wideo na żądanie zasobów z dokładnością grupy z obrazów (GOP) 
- Tworzyć zadania kodowania względem zasobów w konto usługi media services

Aby poprosić o nowe funkcje, pomysły lub opinie, Prześlij, aby [UserVoice dla usługi Azure Media Services](https://aka.ms/amsvoice/). Jeśli masz i określonych problemów, pytania lub znajdowania usterek, docelowej usługi Media Services zespołu linii w amcinfo@microsoft.com.

Na poniższym obrazie przedstawiono interfejs Clipper: ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Informacje o wersji
Zobacz Clipper wpis w blogu, różne znane problemy i dziennika zmian, aby uzyskać najnowszą wersję Clipper następującą listę:
- [Wpis w blogu](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Lista znanych problemów](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Changelog](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Obsługa przeglądarek
Usługa Azure Media Clipper został skompilowany przy użyciu nowoczesnych technologii HTML5 i obsługuje następujące przeglądarki:

- Microsoft Edge 13+
- Internet Explorer 11+
- Chrome 54+
- Safari 10+
- Firefox 50+

> [!NOTE]
> Tylko HTML5 odtwarzania strumieni z usługi Azure Media Services jest obecnie obsługiwane.

## <a name="language-support"></a>Obsługa języków
Element widget Clipper jest dostępna w następujących językach w 18:
- Chiński (uproszczony)
- Chiński (tradycyjny)
- Czeski
- Holenderski, flamandzki
- Polski
- Francuski
- Niemiecki
- Węgierski
- Włoski
- Japoński
- koreański
- Polski
- Portugalski (Brazylia)
- Portugalski (Portugalia)
- Rosyjski
- Hiszpański
- Szwedzki
- Turecki

## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć korzystanie z usługi Azure Media Clipper przeczytaj [wprowadzenie](media-services-azure-media-clipper-getting-started.md) artykuł, aby uzyskać szczegółowe informacje na temat wdrażania elementu widget.
