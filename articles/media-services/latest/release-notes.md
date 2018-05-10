---
title: Azure Media Services w wersji 3 informacje o wersji | Dokumentacja firmy Microsoft
description: Na bieżąco o najnowszych zmianach, ten artykuł zawiera najnowsze aktualizacje na usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: fc6c5ba6cd97c261dd44eade33bf21e8d1b74bf0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Azure Media Services v3 informacje o wersji (wersja zapoznawcza) 

Na bieżąco o najnowszych zmianach, w tym artykule przedstawiono informacje o:

* Najnowsze wersje
* Znane problemy
* Poprawki błędów
* Funkcje uznane za przestarzałe
* Plany zmiany

## <a name="may-07-2018"></a>07 maja 2018

### <a name="net-sdk"></a>Zestaw SDK platformy .net

Następujące funkcje są obecne w .net SDK:

1. **Przekształca** i **zadania** do kodowania i analizowania zawartości nośnika. Przykłady można znaleźć [strumienia pliki](stream-files-tutorial-with-api.md) i [analizy](analyze-videos-tutorial-with-api.md).
2. **StreamingLocators** publikowania i przesyłanie strumieniowe zawartości do urządzeń użytkowników końcowych
3. **StreamingPolicies** i **ContentKeyPolicies** konfigurowania klucza dostawy i ochrony zawartości (DRM), gdy dostarczania zawartości.
4. **LiveEvents** i **LiveOutputs** skonfigurować pozyskiwanie i archiwizację zawartości transmisji strumieniowej na żywo.
5. **Zasoby** do przechowywania i publikowania zawartości multimedialnej w usłudze Azure Storage. 
6. **Punkty końcowe** do konfigurowania i skalować dynamicznego tworzenia pakietów, szyfrowania i przesyłania strumieniowego dla zawartości multimedialnej na żywo i na żądanie.

### <a name="known-issues"></a>Znane problemy

Znany problem:

Podczas przesyłania zadania o adresie URL HTTPS (JobInputHttp) wskazuje zawartość źródłową, upewnij się, że serwer HTTP obsługuje żądania "HEAD". W przeciwnym razie zadanie zostanie odrzucone.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Omówienie](media-services-overview.md)
