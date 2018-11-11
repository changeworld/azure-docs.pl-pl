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
ms.date: 11/05/2018
ms.author: juliako
ms.openlocfilehash: 26661a213b5df5424bf9ab9ad799345ae35620ea
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036771"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Azure Media Services v3 — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Media Services (AMS) w wersji 3.

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Czy można użyć witryny Azure portal w celu zarządzania zasobami v3?

Jeszcze nie. Można użyć jednego z obsługiwanych zestawów SDK. Zobacz, samouczki i przykłady w tym zestawie dokumentów.

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>Do konfigurowania jednostek zarezerwowanych multimediów to interfejs API?

Obecnie masz konfigurowania jednostek zarezerwowanych multimediów przy użyciu interfejsów API usług AMS w wersji 2 (zgodnie z opisem w [skalowanie przetwarzania multimediów](../previous/media-services-scale-media-processing-overview.md). 

Korzystając z **VideoAnalyzerPreset** i/lub **AudioAnalyzerPreset**, ustawić konto usługi Media Services do 10 jednostek zarezerwowanych multimediów S3.

## <a name="does-v3-asset-have-no-assetfile-concept"></a>Zasób V3 ma koncepcja AssetFile?

AssetFiles zostały usunięte z interfejsem API usługi AMS w celu oddzielenia usługi Media Services z zależności zestawu SDK usługi Storage. Teraz Magazyn, a nie usługi Media Services przechowuje informacje, które należy w magazynie. 

## <a name="where-did-client-side-storage-encryption-go"></a>Dokąd zostały przeniesione szyfrowanie po stronie klienta magazynu?

Zaleca się teraz szyfrowanie po stronie serwera, magazynowania, (która jest domyślnie włączone). Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="what-is-the-recommended-upload-method"></a>Co to jest metoda zalecana przekazywania?

Zaleca się, że pozyskuje użytkowania HTTP (s). Aby uzyskać więcej informacji, zobacz [pozyskiwania HTTP (s)](job-input-from-http-how-to.md).

## <a name="how-does-pagination-work"></a>Jak działa dzielenia na strony

Usługa Media Services obsługuje $top dla zasobów, które obsługują OData, ale wartość przekazana do $top musi być mniejsza niż 1000 (na przykład rozmiar strony dla podziału na strony).

Dzięki temu można uzyskać małą próbkę elementów za pomocą $top (na przykład 100 najnowsze elementy) lub stronie mimo że wszystkie elementy przy użyciu dzielenia na strony. 

Usługa Media Services nie obsługuje stronicować je z użytkownikiem, który został określony rozmiar strony.

Aby uzyskać więcej informacji, zobacz [filtrowanie, porządkowanie, stronicowania](assets-concept.md#filtering-ordering-paging)

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Jak pobrać jednostki w usłudze Media Services v3?

v3 opiera się na ujednoliconego powierzchni interfejsu API, który udostępnia funkcje zarządzania i operacje oparte na **usługi Azure Resource Manager**. Zgodnie z **usługi Azure Resource Manager**, nazw zasobów są unikatowe. W związku z tym jako nazw zasobów można używać dowolnych ciągów będących unikatowymi identyfikatorami (na przykład identyfikatorów GUID). 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przegląd usługi Media Services v3](media-services-overview.md)
