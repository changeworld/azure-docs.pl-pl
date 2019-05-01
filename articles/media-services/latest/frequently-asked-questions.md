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
ms.date: 04/24/2019
ms.author: juliako
ms.openlocfilehash: d99e3661fd51b481a20404cfeb55157ac803c220
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706147"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Azure Media Services v3 — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Media Services (AMS) w wersji 3.

## <a name="v3-apis"></a>interfejsy API w wersji 3

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Jakie ról platformy Azure mogą wykonywać akcje na zasoby usługi Azure Media Services? 

Zobacz [kontrola dostępu oparta na rolach (RBAC) dla konta usługi Media Services](rbac-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Jak skonfigurować jednostki zarezerwowane multimediów?

Zdecydowanie zaleca się, aby w przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez usługi Media Services wersji 3 lub Video Indexer, aby przeprowadzać aprowizację konta przy użyciu 10 jednostek MRU typu S3. Jeśli potrzebujesz więcej niż 10 S3 lokalizacje MRU, otwórz bilet pomocy technicznej przy użyciu [witryny Azure portal](https://portal.azure.com/).

Aby uzyskać więcej informacji, zobacz [skalowanie przetwarzania multimediów z użyciem interfejsu wiersza polecenia](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Co to jest to zalecana metoda wideo proces?

Użyj [przekształca](https://docs.microsoft.com/rest/api/media/transforms) skonfigurować typowych zadań związanych z kodowaniem lub analizowanie filmów wideo. Każdy **Przekształcanie** opisuje młyna lub przepływu pracy zadań przetwarzania plików wideo lub audio. A [zadania](https://docs.microsoft.com/rest/api/media/jobs) jest rzeczywistego żądania do usługi Media Services, aby zastosować **Przekształcanie** do danego wejściowego zawartości wideo lub audio. Po utworzeniu przekształcenia można przesłać zadania przy użyciu interfejsów API usług Media Services lub dowolny z opublikowanych zestawów SDK. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transforms-jobs-concept.md).

### <a name="how-does-pagination-work"></a>Jak działa dzielenia na strony

Korzystając z podziałem na strony, zawsze należy używać następny link do wyliczania kolekcji i nie są zależne od wielkości określonej strony. Aby uzyskać szczegółowe informacje i przykłady, zobacz [filtrowanie, porządkowanie, stronicowanie](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Jakie funkcje nie są jeszcze dostępne w usłudze Azure Media Services v3?

Aby uzyskać więcej informacji, zobacz [luki w odniesieniu do interfejsów API w wersji 2 są wyposażone w](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis).

## <a name="live-streaming"></a>Transmisja strumieniowa na żywo 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Jak wstawianie podziałów/wideo i obraz plansz podczas transmisji strumieniowej na żywo?

Usługa Media Services v3 funkcja kodowania na żywo nie obsługuje jeszcze Wstawianie plansz wideo lub obrazy podczas transmisji strumieniowej na żywo. 

Możesz użyć [lokalny koder na żywo](recommended-on-premises-live-encoders.md) przełączyć źródłowy plik wideo. Wiele aplikacji, zapewnia możliwość przełączania źródeł, takich jak Telestream Wirecast, przełącznik Studio (w systemie iOS), Studio systemu bankowości Internetowej (aplikacja jest bezpłatna) i wiele innych.

## <a name="content-protection"></a>Ochrona zawartości

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Jak i gdzie można uzyskać tokenu JWT token przed użyciem w celu żądania licencji lub klucza?

1. W środowisku produkcyjnym musisz zabezpieczyć tokenu usługi (STS) (usługa sieci web), który wystawia token JWT na żądanie protokołu HTTPS. Dla testu, można użyć kod przedstawiony w **GetTokenAsync** metody zdefiniowanej w [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Odtwarzacz należy wysłać żądanie, po uwierzytelnieniu użytkownika do usługi STS do takich token i przypisz ją jako wartość tokenu. Możesz użyć [interfejsu API usługi Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

* Na przykład uruchomienia usługi STS, przy użyciu klucza symetrycznego i asymetrycznym można znaleźć [ https://aka.ms/jwt ](https://aka.ms/jwt). 
* Na przykład odtwarzacza, w oparciu o usługi Azure Media Player przy użyciu takich tokenu JWT zobacz [ https://aka.ms/amtest ](https://aka.ms/amtest) (expand link "player_settings", aby wyświetlić dane wejściowe tokenu).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Jak autoryzować żądania strumieniowo pliki wideo przy użyciu szyfrowania AES?

Właściwe podejście jest korzystanie z usługi STS (Secure Token Service):

W usługi STS w zależności od profilu użytkownika, należy dodać różne oświadczenia (na przykład "Premium User", "Podstawowe użytkownika", "Bezpłatna wersja próbna użytkownika"). Za pomocą różnych oświadczeń w token JWT użytkownik może wyświetlić różną zawartość. Oczywiście dla innej zawartości/zasobu ContentKeyPolicyRestriction mają odpowiednie RequiredClaims.

Użyj Media Services interfejsów API usługi Azure do konfigurowania/klucz licencji dostarczania i szyfrowanie zawartości (jak pokazano na [w tym przykładzie](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Aby uzyskać więcej informacji, zobacz:

- [Omówienie ochrony zawartości](content-protection-overview.md)
- [Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md)

## <a name="media-services-v2-vs-v3"></a>Usługa Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Czy można użyć witryny Azure portal w celu zarządzania zasobami v3?

Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](developers-guide.md).

### <a name="is-there-an-assetfile-concept-in-v3"></a>W wersji 3 jest pojęciem AssetFile?

AssetFiles zostały usunięte z interfejsem API usługi AMS w celu oddzielenia usługi Media Services z zależności zestawu SDK usługi Storage. Teraz Magazyn, a nie usługi Media Services przechowuje informacje, które należy w magazynie. 

Aby uzyskać więcej informacji, zobacz [migracja do usługi Media Services v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Dokąd zostały przeniesione szyfrowanie po stronie klienta magazynu?

Teraz zalecane jest korzystanie z szyfrowania po stronie serwera, magazynowania, (która jest domyślnie włączone). Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Kolejne kroki

[Przegląd usługi Media Services v3](media-services-overview.md)
