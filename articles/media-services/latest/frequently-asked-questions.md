---
title: Azure Media Services v3 — często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/05/2019
ms.author: juliako
ms.openlocfilehash: a447c359c38c2173ea42b6d717067fc8b3a88f9a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875495"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Azure Media Services v3 — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Media Services (AMS) w wersji 3.

## <a name="v3-apis"></a>interfejsy API w wersji 3

### <a name="how-do-i-configure-media-reserved-units"></a>Jak skonfigurować jednostki zarezerwowane multimediów?

Analiza Audio i wideo zadania analizy, które są wyzwalane przez usługi Media Services v3 lub Video Indexer, aby uzyskać zaleca aprowizację swojego konta za pomocą 10 S3 lokalizacje MRU. Jeśli potrzebujesz więcej niż 10 S3 lokalizacje MRU, otwórz bilet pomocy technicznej przy użyciu [witryny Azure portal](https://portal.azure.com/).

Aby uzyskać więcej informacji, zobacz [skalowanie przetwarzania multimediów z użyciem interfejsu wiersza polecenia](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Co to jest to zalecana metoda wideo proces?

Zalecane jest, przesyłania zadań przy użyciu adresu URL HTTP (s) wskazuje filmu wideo. Aby uzyskać więcej informacji [pozyskiwania HTTP (s)](job-input-from-http-how-to.md). Nie należy utworzyć element zawartości za pomocą wejściowy plik wideo, zanim mogą być przetwarzane.

### <a name="how-does-pagination-work"></a>Jak działa dzielenia na strony

Korzystając z podziałem na strony, zawsze należy używać następny link do wyliczania kolekcji i nie są zależne od wielkości określonej strony. Aby uzyskać szczegółowe informacje i przykłady, zobacz [filtrowanie, porządkowanie, stronicowanie](entities-overview.md).

## <a name="live-streaming"></a>Transmisja strumieniowa na żywo 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Jak wstawianie podziałów/wideo i obraz plansz podczas transmisji strumieniowej na żywo?

Usługa Media Services v3 funkcja kodowania na żywo nie obsługuje jeszcze Wstawianie plansz wideo lub obrazy podczas transmisji strumieniowej na żywo. 

Możesz użyć [lokalny koder na żywo](recommended-on-premises-live-encoders.md) przełączyć źródłowy plik wideo. Wiele aplikacji, zapewnia możliwość przełączania źródeł, takich jak Telestream Wirecast, przełącznik Studio (w systemie iOS), Studio systemu bankowości Internetowej (aplikacja jest bezpłatna) i wiele innych.

## <a name="media-services-v2-vs-v3"></a>Usługa Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Czy można użyć witryny Azure portal w celu zarządzania zasobami v3?

Jeszcze nie. Można użyć jednego z obsługiwanych zestawów SDK. Zobacz, samouczki i przykłady w tym zestawie dokumentów.

### <a name="is-there-an-assetfile-concept-in-v3"></a>W wersji 3 jest pojęciem AssetFile?

AssetFiles zostały usunięte z interfejsem API usługi AMS w celu oddzielenia usługi Media Services z zależności zestawu SDK usługi Storage. Teraz Magazyn, a nie usługi Media Services przechowuje informacje, które należy w magazynie. 

Aby uzyskać więcej informacji, zobacz [migracja do usługi Media Services v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Dokąd zostały przeniesione szyfrowanie po stronie klienta magazynu?

Teraz zalecane jest korzystanie z szyfrowania po stronie serwera, magazynowania, (która jest domyślnie włączone). Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przegląd usługi Media Services v3](media-services-overview.md)
