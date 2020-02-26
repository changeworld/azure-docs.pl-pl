---
title: Azure Media Services kodów błędów zdarzeń na żywo | Microsoft Docs
description: W tym artykule wymieniono kody błędów zdarzeń na żywo.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599472"
---
# <a name="media-services-live-event-error-codes"></a>Kody błędów zdarzeń na żywo Media Services

W poniższej tabeli wymieniono kody błędów [zdarzeń na żywo](live-events-outputs-concept.md) :

|Błąd|Opis|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|Ten błąd występuje, gdy koder przychodzący wysyła strumienie przekraczające 30fps do kodowania na żywo zdarzeń/kanałów.|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|Ten błąd występuje, gdy koder przychodzący wysyła strumienie z przekroczeniem następujących rozwiązań: 1920x1088 do kodowania na żywo zdarzeń/kanałów i 4096 x 2160 do przesyłania zdarzeń/kanałów na żywo.|

## <a name="see-also"></a>Zobacz też

[Kody błędów punktu końcowego przesyłania strumieniowego (Źródło)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Następne kroki

[Samouczek: przesyłanie strumieniowe na żywo za pomocą Media Services](stream-live-tutorial-with-api.md)
