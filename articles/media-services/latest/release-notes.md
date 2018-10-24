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
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: db68f979239a5783338d99360209ae231a75c936
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945039"
---
# <a name="azure-media-services-v3-release-notes"></a>Usługa Azure Media Services v3 — informacje o wersji 

Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

* Zainstalowane najnowsze wersje
* Znane problemy
* Poprawki błędów
* Przestarzałe funkcje
* Plany dotyczące zmian

## <a name="october-2018---ga"></a>Października 2018 — ogólna dostępność

W tej sekcji opisano aktualizacje usługi Azure Media Services (AMS) października.

### <a name="rest-v3-ga-release"></a>Wersja Ogólnodostępna v3 REST

[Wersja Ogólnodostępna v3 REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) zawiera więcej interfejsów API na żywo, filtry na poziomie manifestu konta/zasobów i obsługa technologii DRM.

#### <a name="azure-resource-management"></a>Zarządzanie zasobami platformy Azure 

Obsługa usługi Azure Resource Management umożliwia ujednolicone zarządzanie i operacje interfejsu API (teraz wszystko w jednym miejscu).

Począwszy od tej wersji, można użyć szablonów usługi Resource Manager można tworzyć zdarzenia na żywo.

#### <a name="improvement-of-asset-operations"></a>Ulepszanie operacje zasobów 

Wprowadzono następujące ulepszenia:

- Pozyskuj z adresów URL HTTP (s) lub adresów URL sygnatury dostępu Współdzielonego magazynu obiektów Blob platformy Azure.
- Określ nazwy kontenera własnych zasobów. 
- Łatwiejsza Obsługa danych wyjściowych do tworzenia niestandardowych przepływów pracy z usługą Azure Functions.

#### <a name="new-transform-object"></a>Nowe przekształcenie obiektu

Nowy **Przekształcanie** obiektu upraszcza modelu z kodowaniem. Nowy obiekt ułatwia tworzenie i udostępnianie szablonów usługi Resource Manager kodowania i ustawienia wstępne. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Uwierzytelnianie usługi Active Directory i Azure RBAC

Uwierzytelnianie usługi Azure AD i kontroli dostępu opartej na rolach (RBAC) umożliwiają bezpieczne przekształceń, LiveEvents, zasady kluczy zawartości lub zasobów według roli lub użytkowników w usłudze Azure AD.

#### <a name="client-sdks"></a>Zestawy SDK klienta  

Języki obsługiwane w wersji 3 usługa Media Services: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Aktualizacje kodowania na żywo

Wprowadzono następujące aktualizacje kodowania na żywo:

- Nowy tryb małe opóźnienia na żywo (10 sekund end-to-end).
- Ulepszona obsługa protokołu RTMP (Zwiększona stabilność i więcej obsługę kodera źródłowego).
- Pozyskuj RTMPS bezpieczne.

    Gdy utworzysz element LiveEvent, możesz teraz pobieranie 4 adresów URL pozyskiwania. Pozyskiwanie 4 adresy URL są niemal identyczne, mają ten sam token przesyłania strumieniowego (AppId), tylko część numer portu jest inny. Są dwa adresy URL podstawowego i zapasowego dla RTMPS. 
- Obsługa transkodowanie 24-godzinnym. 
- Ulepszona obsługa sygnalizowanie ad w RTMP za pośrednictwem SCTE35.

#### <a name="improved-event-grid-support"></a>Ulepszona obsługa usługi Event Grid

Możesz zobaczyć następujące usługi Event Grid obsługuje ulepszenia:

- Integracja usługi Azure EventGrid ułatwia tworzenie aplikacji przy użyciu aplikacji logiki i Azure Functions. 
- Subskrybowanie zdarzeń na kodowanie, kanały na żywo i nie tylko.

### <a name="cmaf-support"></a>Obsługa CMAF

CMAF i "cbcs" Obsługa szyfrowania HLS firmy Apple (system iOS 11 +) i MPEG-DASH graczy, które obsługują CMAF.

### <a name="video-indexer"></a>Indeksator wideo

Wideo wersja Ogólnodostępna indeksatora zostało zapowiedziane w sierpniu. Aby uzyskać nowe informacje o aktualnie obsługiwanych funkcji, zobacz [co to jest Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Plany dotyczące zmian

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
 
Moduł Azure CLI 2.0, który zawiera operacje na wszystkich funkcji (w tym na żywo, zasad dotyczących zawartości klucza, filtry konta/zasobów, przesyłania strumieniowego zasad) będzie dostępna wkrótce. 

### <a name="known-issues"></a>Znane problemy

Następujący problem wpływa tylko klienci, którzy używać interfejsu API w wersji zapoznawczej dla aktywami lub AccountFilters.

Jeśli podczas tworzenia zasobów lub filtrów kont od 09/28 i 10/12 przy użyciu interfejsu wiersza polecenia usługi Media Services v3 lub interfejsów API, należy usunąć wszystkie zasoby i AccountFilters i ponownie utwórz je ze względu na konflikt wersji. 

## <a name="may-2018---preview"></a>Maja 2018 r. — wersja zapoznawcza

### <a name="net-sdk"></a>Zestaw SDK platformy .net

Następujące funkcje są obecne w zestawu .net SDK:

* **Przekształca** i **zadań** do kodowania i analizowanie zawartości multimedialnej. Aby uzyskać przykłady, zobacz [Stream pliki](stream-files-tutorial-with-api.md) i [analizy](analyze-videos-tutorial-with-api.md).
* **StreamingLocators** do publikowania i przesyłanie strumieniowe zawartości na urządzeniach użytkowników końcowych
* **StreamingPolicies** i **ContentKeyPolicies** skonfigurować dostarczania kluczy i ochrona zawartości (DRM), podczas dostarczania zawartości.
* **LiveEvents** i **LiveOutputs** skonfigurować pozyskiwania i zarchiwizowanie mających zawartości transmisji strumieniowej na żywo.
* **Zasoby** do przechowywania i publikowanie zawartości multimedialnej w usłudze Azure Storage. 
* **Punkty** konfigurować i skalować funkcję dynamicznego tworzenia pakietów, szyfrowania i przesyłania strumieniowego dla zawartości multimedialnej na żywo i na żądanie.

### <a name="known-issues"></a>Znane problemy

* Podczas przesyłania zadania, można określić pozyskiwania źródłowy plik wideo przy użyciu adresów URL HTTPS, adresy URL sygnatury dostępu Współdzielonego lub ścieżki do plików znajdujących się w usłudze Azure Blob storage. Obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu przez adresy URL HTTPS.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Omówienie](media-services-overview.md)
