---
title: Wideo scen, zrzuty i klatki kluczowe — Azure indeksatora
titlesuffix: Azure Media Services
description: Ten temat zawiera omówienie sceny indeksatora wideo, zrzutów i klatki kluczowe.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: dfa41dc695cf6ab357a9cd4cdbd32454b6dd107d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896555"
---
# <a name="scenes-shots-and-keyframes"></a>Scen, zrzuty i klatki kluczowe

Usługa Video Indexer obsługuje podzielenie filmów wideo do jednostek danych czasowych na podstawie właściwości strukturalną i semantyczną. Ta funkcja umożliwia klientom łatwe przeglądanie, zarządzania i Edytuj ich zawartość wideo, w oparciu o różnej szczegółowości. Na przykład na podstawie scen, zrzuty i klatki kluczowe, które są opisane w tym temacie. **Wykrywania sceny** funkcja jest obecnie dostępna w wersji zapoznawczej.   

![Scen, zrzuty i klatki kluczowe](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>Wykrywanie sceny (wersja zapoznawcza)

Usługa Video Indexer określa zmiany scenę w oparciu o podpowiedzi wizualne wideo. Scena przedstawia pojedyncze zdarzenie i składa się z szeregu kolejnych zrzuty, które są semantycznie ze sobą powiązane. Miniatura sceny jest pierwszy kluczową jego zrzut bazowego. Usługa Video indexer segmenty wideo w tle oparte na kolor spójności między kolejnymi zrzuty i pobiera rozpoczęcia i czas zakończenia każdego sceny. Wykrywanie sceny jest traktowane jako trudniejszym zadaniem, ponieważ spowodowałoby to kwantyfikacja semantycznego aspektów filmów wideo.

> [!NOTE]
> Ma zastosowanie do filmów wideo, które zawierają co najmniej 3 sceny.

## <a name="shot-detection"></a>Wykrywanie ujęć

Usługa Video Indexer Określa, kiedy zrzut zmiany w trakcie filmu wideo, na podstawie podpowiedzi wizualne śledzenie nagłego i stopniowo przejścia w schemacie sąsiadujących ramek. Zrzut metadanych zawiera rozpoczęcia i czas zakończenia, a także listę ramek kluczowych zawarte w tym zrzut. Zrzuty są kolejnych klatek wykonane z tego samego aparatu w tym samym czasie.

## <a name="keyframe-detection"></a>Wykrywanie ramki kluczowej

Wybiera ramki, które najlepiej reprezentują zrzut. Klatki kluczowe są reprezentatywne ramek, wybrana w zaufanym całe wideo na podstawie właściwości estetycznych (na przykład kontrast i stableness). Usługa Video Indexer umożliwia pobranie listy ramki kluczowej identyfikatorów jako część zrzut metadanych, których klientów można wyodrębnić miniaturę klatki kluczowej. 

Klatki kluczowe są skojarzone z zrzuty w danych wyjściowych JSON. 

## <a name="next-steps"></a>Kolejne kroki

[Zbadanie danych wyjściowych indeksatora wideo utworzone przez interfejs API](video-indexer-output-json-v2.md#scenes)