---
title: Azure Media Services v3 — często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/29/2018
ms.author: juliako
ms.openlocfilehash: 098a34aba8e5ce23f64d4bb07e3b9622aa2adb8e
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110424"
---
# <a name="azure-media-services-v3-preview-frequently-asked-questions"></a>Azure Media Services w wersji 3 (wersja zapoznawcza) — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Media Services (AMS) w wersji 3.

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Do zarządzania zasobami v3 można używać portalu Azure?

Jeszcze nie. Można użyć jednego z obsługiwanych zestawów SDK. Zobacz samouczki i przykłady w tym zestawie dokumentów.

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>Do konfigurowania jednostki zarezerwowane multimediów to interfejs API?

Zespół usługi Media Services jest wyeliminowanie RUs w wersji 3. Jednak pracy wymagane usługi nie została ukończona. Do tego czasu klienci mają do portalu Azure lub interfejsów API usług AMS v2 skonfiguruj RUs (zgodnie z opisem w [skalowanie przetwarzania multimediów](../previous/media-services-scale-media-processing-overview.md). 

Korzystając z **VideoAnalyzerPreset** i/lub **AudioAnalyzerPreset**, ustawić konta usługi Media Services do 10 jednostki zarezerwowane multimediów S3.

## <a name="does-v3-asset-have-no-assetfile-concept"></a>V3 zasobów ma pojęcie AssetFile?

AssetFiles zostały usunięte z interfejsu API usług AMS aby oddzielić Media Services z zestawu SDK usługi Magazyn zależności. Teraz magazynu, a nie usługi Media Services, przechowuje informacje, które należy w magazynie. 

## <a name="where-did-client-side-storage-encryption-go"></a>Gdzie szyfrowanie po stronie klienta magazynu?

Zaleca się teraz szyfrowanie po stronie serwera, magazynu, (która jest domyślnie włączona). Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Magazyn Azure dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="what-is-the-recommended-upload-method"></a>Co to jest metoda przekazywania zalecane?

Zaleca się użycie HTTP (s) wysyła strumień. Aby uzyskać więcej informacji, zobacz [pozyskiwania HTTP (s)](job-input-from-http-how-to.md).

## <a name="how-does-pagination-work"></a>Jak działa podział na strony

Usługa Media Services obsługuje $top dla zasobów, które obsługują OData, ale wartość przekazana do $top musi być mniejsza niż 1000 (na przykład rozmiar strony dla podział na strony).

Umożliwia uzyskanie albo małej przykładowej elementów przy użyciu $top (na przykład 100 najnowsze elementy) lub strona mimo że wszystkie elementy przy użyciu podział na strony. 

Usługa Media Services nie obsługuje stronicowania za pomocą danych z określonego przez użytkownika rozmiar strony.

Aby uzyskać więcej informacji, zobacz [filtrowanie, porządkowanie, stronicowania](assets-concept.md#filtering-ordering-paging)

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Jak pobrać jednostki w wersji 3 usługi Media Services?

v3 opiera się na ujednoliconego powierzchni interfejsu API, który udostępnia funkcje zarówno zarządzanie i operacje, w oparciu **usługi Azure Resource Manager**. Zgodnie z **usługi Azure Resource Manager**, nazw zasobów są unikatowe. W związku z tym jako nazw zasobów można używać dowolnych ciągów będących unikatowymi identyfikatorami (na przykład identyfikatorów GUID). 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Omówienie v3 Media Services](media-services-overview.md)
