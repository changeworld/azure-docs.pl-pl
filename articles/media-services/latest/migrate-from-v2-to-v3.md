---
title: Migrowanie z usługi Azure Media Services w wersji 2 do wersji 3 | Dokumenty firmy Microsoft
description: W tym artykule opisano zmiany, które zostały wprowadzone w usłudze Azure Media Services w wersji 3 i pokazuje różnice między dwiema wersjami. Ten artykuł zawiera również wskazówki dotyczące migracji do przenoszenia z usługi Media Services w wersji 2 do wersji 3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 72d413c5d8bc982d885d889da35b29a3607410cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472071"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Wskazówki dotyczące migracji dotyczące przechodzenia z usługi Media Services w wersji 2 do wersji 3

>Otrzymuj powiadomienia o tym, kiedy można ponownie odwiedzić `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` tę stronę, aby uzyskać aktualizacje, kopiując i wklejając ten adres URL: do czytnika kanałów RSS.

Ten artykuł zawiera wskazówki dotyczące migracji z usługi Media Services w wersji 2 do wersji 3.

Jeśli masz usługę wideo opracowaną dzisiaj na [starszych interfejsach API usługi Media Services w wersji 2,](../previous/media-services-overview.md)przed migracją do interfejsów API systemu microsoft services należy zapoznać się z następującymi wytycznymi i zagadnieniami. Istnieje wiele korzyści i nowych funkcji w interfejsie API w wersji 3, które poprawiają środowisko deweloperskie i możliwości usługi Media Services. Jednak jak to wywołano w sekcji [Znane problemy](#known-issues) w tym artykule, istnieją również pewne ograniczenia ze względu na zmiany między wersjami interfejsu API. Ta strona zostanie zachowana, ponieważ zespół usługi Media Services wprowadza ciągłe ulepszenia interfejsów API w wersji 3 i rozwiązuje luki między wersjami. 

## <a name="prerequisites"></a>Wymagania wstępne

* Przegląd [usługi Media Services v2 vs. v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Korzyści z usługi Media Services w wersji 3
  
### <a name="api-is-more-approachable"></a>API jest bardziej przystępny

*  Wersja 3 opiera się na ujednoliconej powierzchni interfejsu API, która udostępnia zarówno funkcje zarządzania, jak i operacji oparte na usłudze Azure Resource Manager. Szablony usługi Azure Resource Manager mogą służyć do tworzenia i wdrażania przekształceń, punktów końcowych przesyłania strumieniowego, wydarzeń na żywo i innych.
* [Dokument specyfikacji OpenAPI (dawniej nazywany Swagger).](https://aka.ms/ams-v3-rest-sdk)
    Udostępnia schemat dla wszystkich składników usługi, w tym kodowania opartego na plikach.
* SDK dostępne dla [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref)i Ruby.
* Integracja [interfejsu wiersza polecenia platformy Azure](https://aka.ms/ams-v3-cli-ref) w celu obsługi prostych skryptów.

### <a name="new-features"></a>Nowe funkcje

* W przypadku przetwarzania zadań opartych na plikach można użyć adresu URL HTTP(S) jako danych wejściowych.<br/>Nie trzeba mieć zawartość już przechowywane na platformie Azure, ani nie trzeba tworzyć zasoby.
* Wprowadza koncepcję [przekształceń](transforms-jobs-concept.md) do przetwarzania zadań opartych na plikach. Transformacja może służyć do tworzenia konfiguracji wielokrotnego użycia, do tworzenia szablonów usługi Azure Resource Manager i izolowania ustawień przetwarzania między wieloma klientami lub dzierżawami.
* Zasób może mieć wiele [lokalizatorów przesyłania strumieniowego,](streaming-locators-concept.md) z których każdy ma różne ustawienia [dynamicznego pakowania](dynamic-packaging-overview.md) i szyfrowania dynamicznego.
* [Ochrona zawartości](content-key-policy-concept.md) obsługuje funkcje wielonakołowe.
* Można przesyłać strumieniowo zdarzenia na żywo, które są do 24 godzin podczas korzystania z usługi Media Services do transkodowania pojedynczego kanału danych wejściowych bitrate do strumienia wyjściowego, który ma wiele szybkości transmisji bitów.
* Nowa obsługa transmisji strumieniowej na żywo o niskim opóźnieniu w wydarzeniach na żywo. Aby uzyskać więcej informacji, zobacz [opóźnienie](live-event-latency.md).
* Podgląd zdarzeń na żywo obsługuje [dynamiczne pakowanie](dynamic-packaging-overview.md) i szyfrowanie dynamiczne. Umożliwia to ochronę zawartości w wersji zapoznawczej oraz na opakowaniach DASH i HLS.
* Dane wyjściowe na żywo jest prostsze w użyciu niż program jednostki w interfejsach API w wersji 2. 
* Ulepszona obsługa RTMP (zwiększona stabilność i większa obsługa kodera źródłowego).
* RTMPS bezpieczne połknienia.<br/>Podczas tworzenia wydarzenia na żywo otrzymasz 4 adresy URL pozyskiwania. 4 adresy URL pozyskiwania są prawie identyczne, mają ten sam token przesyłania strumieniowego (AppId), tylko część numeru portu jest inna. Dwa adresy URL są podstawowymi i kopiami zapasowymi dla RTMPS.   
* Masz kontroli dostępu opartej na rolach (RBAC) nad encjami. 

## <a name="known-issues"></a>Znane problemy

*  Obecnie można użyć [witryny Azure portal](https://portal.azure.com/) do:

    * zarządzanie wydarzeniami [na żywo](live-events-outputs-concept.md)w udziale usługi Media Services w wersji 3 , 
    * widok (nie zarządzanie) [v3 Aktywa](assets-concept.md), 
    * [uzyskać informacje o dostępie do interfejsów API](access-api-portal.md). 

    W przypadku wszystkich innych zadań zarządzania (na przykład [przekształceń i zadań](transforms-jobs-concept.md) oraz [ochrony zawartości)](content-protection-overview.md)użyj [interfejsu API REST,](https://docs.microsoft.com/rest/api/media/) [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref)lub jednego z [obsługiwanych pakietów SDK.](media-services-apis-overview.md#sdks)
* Musisz aprowizować jednostki zarezerwowane multimediów (MRU) na swoim koncie, aby kontrolować współbieżność i wydajność zadań, w szczególności tych dotyczących analizy wideo lub audio. Aby uzyskać więcej informacji, zobacz [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Skalowanie przetwarzania multimediów). Mru można zarządzać za pomocą [interfejsu CLI 2.0 for Media Services w wersji 3,](media-reserved-units-cli-how-to.md)za pomocą [portalu Azure](../previous/media-services-portal-scale-media-processing.md)lub przy użyciu [interfejsów API w wersji 2](../previous/media-services-dotnet-encoding-units.md). Należy aprowizować procesory MRU, niezależnie od tego, czy są używane interfejsy API usługi Media Services w wersji 2 czy 3.
* Encje usługi Media Services utworzone za pomocą interfejsu API w wersji 3 nie mogą być zarządzane przez interfejs API w wersji 2.  
* Nie wszystkie jednostki w interfejsie API v2 automatycznie pojawiają się w interfejsie API v3.  Poniżej przedstawiono przykłady jednostek w dwóch wersjach, które są niezgodne:  
    * Zadania i zadania utworzone w wersji 2 nie są wyświetlane w wersji 3, ponieważ nie są skojarzone z transformacją. Zalecenie jest, aby przełączyć się na v3 Przekształcenia i zadania. Będzie stosunkowo krótki okres czasu konieczności monitorowania zadań pokładowych v2 podczas przełączania.
    * Kanały i programy utworzone w wersji 2 (które są mapowane na wydarzenia na żywo i wyjścia na żywo w wersji 3) nie mogą być nadal zarządzane w wersji 3. Zaleca się, aby przełączyć się na v3 Live Events i live outputs na wygodnym przystanku kanału.<br/>Obecnie nie można migrować stale uruchomionych kanałów.  

> [!NOTE]
> Ta strona zostanie zachowana, ponieważ zespół usługi Media Services wprowadza ciągłe ulepszenia interfejsów API w wersji 3 i rozwiązuje luki między wersjami.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

[Samouczek: Kodowanie zdalnego pliku na podstawie adresu URL i przesyłanie strumieniowe wideo - .NET](stream-files-dotnet-quickstart.md)
