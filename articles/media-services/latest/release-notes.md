---
title: Usługa Azure Media Services v3 — informacje o wersji | Dokumentacja firmy Microsoft
description: Aby uzyskać najnowsze informacje o najnowszych zmianach, ten artykuł zawiera najnowsze aktualizacje usługi Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/25/2018
ms.author: juliako
ms.openlocfilehash: ed2550c1df4645933fb968c54ee536995c810136
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219333"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Azure Media Services v3 (wersja zapoznawcza) — informacje o wersji 

Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

* Zainstalowane najnowsze wersje
* Znane problemy
* Poprawki błędów
* Przestarzałe funkcje
* Plany dotyczące zmian

## <a name="may-07-2018"></a>07 maja 2018 r.

### <a name="net-sdk"></a>Zestaw SDK platformy .net

Następujące funkcje są obecne w zestawu .net SDK:

1. **Przekształca** i **zadań** do kodowania i analizowanie zawartości multimedialnej. Aby uzyskać przykłady, zobacz [Stream pliki](stream-files-tutorial-with-api.md) i [analizy](analyze-videos-tutorial-with-api.md).
2. **StreamingLocators** do publikowania i przesyłanie strumieniowe zawartości na urządzeniach użytkowników końcowych
3. **StreamingPolicies** i **ContentKeyPolicies** skonfigurować dostarczania kluczy i ochrona zawartości (DRM), podczas dostarczania zawartości.
4. **LiveEvents** i **LiveOutputs** skonfigurować pozyskiwania i zarchiwizowanie mających zawartości transmisji strumieniowej na żywo.
5. **Zasoby** do przechowywania i publikowanie zawartości multimedialnej w usłudze Azure Storage. 
6. **Punkty** konfigurować i skalować funkcję dynamicznego tworzenia pakietów, szyfrowania i przesyłania strumieniowego dla zawartości multimedialnej na żywo i na żądanie.

### <a name="known-issues"></a>Znane problemy

* Podczas przesyłania zadania, można określić pozyskiwania źródłowy plik wideo przy użyciu adresów URL HTTPS, adresy URL sygnatury dostępu Współdzielonego lub ścieżki do plików znajdujących się w usłudze Azure Blob storage. Obecnie AMS v3 nie obsługuje kodowanie przez adresy URL HTTPS fragmentaryczne transferu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Omówienie](media-services-overview.md)
