---
title: Video Indexer scen, zrzutów i klatek kluczowych — platforma Azure
titlesuffix: Azure Media Services
description: Ten temat zawiera omówienie Video Indexer scen, zrzutów i klatek kluczowych.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: cdabc1b6bfed519098f656710ef49a946e676cf2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815656"
---
# <a name="scenes-shots-and-keyframes"></a>Sceny, ujęcia i klatki kluczowe

Video Indexer obsługuje segmentacji wideo na jednostki czasowe na podstawie właściwości strukturalnych i semantycznych. Ta funkcja umożliwia klientom łatwe przeglądanie i Edytowanie zawartości wideo w oparciu o różne stopnia szczegółowości. Na przykład na podstawie scen, zrzutów i klatek kluczowych opisanych w tym temacie.   

![Sceny, ujęcia i klatki kluczowe](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Wykrywanie sceny  
 
Video Indexer określa, kiedy scena zmienia się w wideo na podstawie podpowiedzi wizualizacji. Scena przedstawia pojedyncze zdarzenie i składa się z szeregu kolejnych zrzutów, które są semantycznie powiązane. Miniatura sceny to pierwsza klatka kluczowa podstawowego zrzutu. Indeksator wideo segmentuje wideo w scenach na podstawie spójności kolorów dla kolejnych zrzutów i pobiera godzinę rozpoczęcia i zakończenia każdej sceny. Wykrywanie sceny jest uznawane za trudne zadanie, ponieważ obejmuje to obliczanie ilości semantycznych aspektów wideo.

> [!NOTE]
> Dotyczy wideo, które zawiera co najmniej 3 sceny.

## <a name="shot-detection"></a>Wykrywanie ujęć

Video Indexer określa, kiedy zrzut zmienia się w filmie wideo na podstawie podpowiedzi wizualizacji, śledząc jednocześnie przekroczenia i stopniowe przejścia w schemacie kolorów sąsiadujących ramek. Metadane zrzutu obejmują godzinę początkową i końcową oraz listę klatek kluczowych zawartych w tym zrzucie. Zrzuty to kolejne ramki pobierane z tego samego aparatu w tym samym czasie.

## <a name="keyframe-detection"></a>Wykrywanie klatek kluczowych

Wybiera ramki, które najlepiej reprezentują zrzut. Klatki kluczowe to reprezentatywne ramki wybrane z całego wideo w oparciu o właściwości estetyczne (na przykład kontrast i trwałość). Video Indexer pobiera listę identyfikatorów klatek kluczowych jako część metadanych zrzutu, na podstawie których klienci mogą wyodrębnić miniaturę klatki kluczowej. 

Ramki kluczowe są skojarzone ze zrzutami w wyjściowym kodzie JSON. 

## <a name="next-steps"></a>Następne kroki

[Sprawdzanie Video Indexer danych wyjściowych wytwarzanych przez interfejs API](video-indexer-output-json-v2.md#scenes)
