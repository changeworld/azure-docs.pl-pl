---
title: Migrowanie z Azure Media Services V2 do wersji v3 | Microsoft Docs
description: W tym artykule opisano zmiany wprowadzone w Azure Media Services v3 i przedstawiono różnice między dwiema wersjami. Artykuł zawiera również wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3.
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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 003cc54a07455118969a2dd497e9b963c03f68f2
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73099486"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3

W tym artykule opisano zmiany wprowadzone w Azure Media Services v3, przedstawiono różnice między dwiema wersjami i przedstawiono wskazówki dotyczące migracji.

Jeśli masz już zainstalowaną usługę wideo na [starszej wersji interfejsów api Media Services V2](../previous/media-services-overview.md), przed migracją do interfejsów API v3 należy zapoznać się z poniższymi wskazówkami i kwestiami. Istnieje wiele zalet i nowych funkcji w interfejsie API v3, które zwiększają możliwości środowiska deweloperskiego i funkcje Media Services. Jednak zgodnie z opisem w sekcji [znane problemy](#known-issues) w tym artykule istnieją także pewne ograniczenia związane z zmianami między wersjami interfejsu API. Ta strona będzie utrzymywana, gdy zespół Media Services wprowadza dalsze ulepszenia interfejsów API v3 i rozwiązuje luki między wersjami. 

> [!NOTE]
> Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

## <a name="benefits-of-media-services-v3"></a>Zalety Media Services v3
  
### <a name="api-is-more-approachable"></a>Interfejs API jest bardziej wydajny

*  Wersja 3 opiera się na ujednoliconej powierzchni interfejsu API, która udostępnia zarówno funkcje zarządzania, jak i operacji oparte na usłudze Azure Resource Manager. Przy użyciu szablonów Azure Resource Manager można tworzyć i wdrażać transformacje, punkty końcowe przesyłania strumieniowego, zdarzenia na żywo i wiele innych.
* Dokument [specyfikacji openapi (dawniej: Swagger)](https://aka.ms/ams-v3-rest-sdk) .
    Uwidacznia schemat dla wszystkich składników usługi, w tym kodowania opartego na plikach.
* Zestawy SDK dostępne dla [platform .NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node. js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [go](https://aka.ms/ams-v3-go-ref)i Ruby.
* Integracja [interfejsu wiersza polecenia platformy Azure](https://aka.ms/ams-v3-cli-ref) w celu obsługi prostych skryptów.

### <a name="new-features"></a>Nowe funkcje

* W przypadku przetwarzania zadań opartych na plikach można użyć adresu URL HTTP (S) jako danych wejściowych.<br/>Nie musisz mieć już przechowywanej zawartości na platformie Azure ani nie musisz tworzyć zasobów.
* Wprowadza koncepcję [transformacji](transforms-jobs-concept.md) w przypadku przetwarzania zadań opartych na plikach. Przekształcenie może służyć do kompilowania konfiguracji do wielokrotnego użytku, tworzenia Azure Resource Manager szablonów i izolowania ustawień przetwarzania między wieloma klientami lub dzierżawcami.
* Element zawartości może zawierać wiele [lokalizatorów przesyłania strumieniowego](streaming-locators-concept.md) z różnymi [pakietami dynamicznymi](dynamic-packaging-overview.md) i dynamicznymi ustawieniami szyfrowania.
* Funkcja [Content Protection](content-key-policy-concept.md) obsługuje funkcje wielokluczowe.
* Można przesyłać strumieniowo zdarzenia na żywo, które są maksymalnie 24 godziny, przy użyciu Media Services do transkodowania pojedynczego źródła szybkości transmisji bitów do strumienia wyjściowego, który ma wiele szybkości transmisji bitów.
* Obsługa przesyłania strumieniowego na żywo w przypadku nowych małych opóźnień na żywo. Aby uzyskać więcej informacji, zobacz [opóźnienie](live-event-latency.md).
* Usługa Live Event Preview obsługuje [dynamiczne pakowanie](dynamic-packaging-overview.md) i szyfrowanie dynamiczne. Umożliwia to ochronę zawartości w wersji zapoznawczej, a także używanie ŁĄCZNIKa i HLS.
* Użycie danych wyjściowych na żywo jest łatwiejsze niż w przypadku jednostki programu w interfejsach API v2. 
* Ulepszona obsługa protokołu RTMP (Zwiększona stabilność i więcej obsługa kodera-źródła).
* Bezpieczne pozyskiwanie RTMP.<br/>Gdy tworzysz wydarzenie na żywo, otrzymujesz 4 adresy URL pozyskiwania. 4 adresy URL pozyskiwania są prawie identyczne, mają ten sam token przesyłania strumieniowego (AppId), tylko część numeru portu. Dwa z adresów URL to podstawowy i zapasowy dla RTMP.   
* Kontrola dostępu oparta na rolach (RBAC) jest dostępna dla jednostek. 

## <a name="changes-from-v2"></a>Zmiany z wersji 2

* W przypadku zasobów utworzonych w wersji 3 Media Services obsługuje tylko [szyfrowanie magazynu po stronie serwera usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Można używać interfejsów API v3 z zasobami utworzonymi przy użyciu interfejsów API v2, które mają [szyfrowanie magazynu](../previous/media-services-rest-storage-encryption.md) (AES 256) zapewniane przez Media Services.
    * Nie można tworzyć nowych zasobów przy użyciu starszej wersji [szyfrowania pamięci](../previous/media-services-rest-storage-encryption.md) AES 256 z użyciem interfejsów API v3.
* Właściwości [zasobu](assets-concept.md)w wersji 3 różnią się od v2, zobacz [jak mapa właściwości](assets-concept.md#map-v3-asset-properties-to-v2).
* Zestawy SDK V3 są teraz rozłączone z ZESTAWem danych magazynu, co zapewnia większą kontrolę nad wersją zestawu SDK magazynu, którego chcesz używać, oraz pozwala uniknąć problemów z przechowywaniem wersji. 
* W przypadku interfejsów API V3 wszystkie stawki bitów kodowania są w bitach na sekundę. Różni się to od ustawień wstępnych w wersji 2 Media Encoder Standard. Na przykład szybkość transmisji bitów w v2 zostanie określona jako 128 (KB/s), ale w wersji 3 będzie 128000 (bity/s). 
* Jednostki AssetFiles, AccessPolicies i IngestManifests nie istnieją w wersji 3.
* Właściwość IAsset. ParentAssets nie istnieje w wersji 3.
* ContentKeys nie jest już jednostką, jest teraz właściwością lokalizatora przesyłania strumieniowego.
* Obsługa Event Grid zastępuje NotificationEndpoints.
* Zmieniono nazwy następujących jednostek:
    * Dane wyjściowe zadania zastępują zadanie i są teraz częścią zadania.
    * Lokalizator przesyłania strumieniowego zastępuje lokalizator.
    * Wydarzenie na żywo zastępuje kanał.<br/>Rozliczenia wydarzeń na żywo opierają się na licznikach kanału na żywo. Aby uzyskać więcej informacji, zobacz temat [rozliczenia](live-event-states-billing.md) i [Cennik](https://azure.microsoft.com/pricing/details/media-services/).
    * Wyjście na żywo zastępuje program.
* Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania. Programy pracowały inaczej w interfejsach API v2, musiały zostać uruchomione po utworzeniu.
* Aby uzyskać informacje o zadaniu, należy znać nazwę przekształcenia, w której utworzono zadanie. 
* W wersji 2 pliki metadanych [danych wejściowych](../previous/media-services-input-metadata-schema.md) i [wyjściowych](../previous/media-services-output-metadata-schema.md) XML są generowane w wyniku zadania kodowania. W wersji 3 format metadanych został zmieniony z XML na JSON. 

> [!NOTE]
> Zapoznaj się z konwencjami nazewnictwa, które są stosowane do [zasobów Media Services v3](media-services-apis-overview.md#naming-conventions). Przejrzyj również [nazwy obiektów BLOB](assets-concept.md#naming-blobs).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Luki w funkcjach w odniesieniu do interfejsów API v2

Interfejs API v3 ma następujące luki w odniesieniu do interfejsu API w wersji 2. Zamknięcie przerw jest w toku.

* [Koder w warstwie Premium](../previous/media-services-premium-workflow-encoder-formats.md) i starsze [procesory analizy multimediów](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 — wersja zapoznawcza, Front redactor itp.) nie są dostępne za pośrednictwem wersji 3.<br/>Klienci, którzy chcą przeprowadzić migrację z Media Indexer 1 lub 2 wersji zapoznawczej, mogą natychmiast użyć ustawień wstępnych AudioAnalyzer w interfejsie API v3.  To nowe ustawienie wstępne zawiera więcej funkcji niż starszy Media Indexer 1 lub 2. 
* Wiele [zaawansowanych funkcji Media Encoder standard w](../previous/media-services-advanced-encoding-with-mes.md) interfejsie API v2 nie są obecnie dostępne w wersji 3, na przykład:
  
    * Łączenie elementów zawartości
    * Nakładki
    * Przycinanie
    * Miniaturowe sprites
    * Wstawianie dyskretnej ścieżki audio, gdy dane wejściowe nie zawierają dźwięku
    * Wstawianie ścieżki wideo, gdy dane wejściowe nie zawierają wideo
* Zdarzenia na żywo z transkodowaniem obecnie nie obsługują wstawiania poza strumień i znacznik usługi AD za pośrednictwem wywołania interfejsu API. 

> [!NOTE]
> Oznacz ten artykuł zakładką i Kontynuuj sprawdzanie aktualizacji.
 
## <a name="code-differences"></a>Różnice w kodzie

W poniższej tabeli przedstawiono różnice w kodzie między wersjami 2 i V3 dla typowych scenariuszy.

|Scenariusz|INTERFEJS API V2|INTERFEJS API V3|
|---|---|---|
|Tworzenie elementu zawartości i przekazywanie pliku |[przykład v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[przykład .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Przesyłanie zadania|[przykład v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[przykład .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Pokazuje, jak najpierw utworzyć transformację, a następnie przesłać zadanie.|
|Publikowanie elementu zawartości przy użyciu szyfrowania AES |1. Utwórz ContentKeyAuthorizationPolicyOption<br/>2. Utwórz ContentKeyAuthorizationPolicy<br/>3. Utwórz AssetDeliveryPolicy<br/>4. Utwórz element zawartości i przekaż zawartość lub Prześlij zadanie, a następnie użyj elementu zawartości wyjściowej<br/>5. Skojarz AssetDeliveryPolicy z elementem zawartości<br/>6. Utwórz ContentKey<br/>7. Dołącz ContentKey do zasobu<br/>8. Utwórz AccessPolicy<br/>9. Tworzenie lokalizatora<br/><br/>[przykład v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Utwórz zasady klucza zawartości<br/>2. Utwórz element zawartości<br/>3. Przekaż zawartość lub użyj elementu zawartości jako JobOutput<br/>4. Tworzenie lokalizatora przesyłania strumieniowego<br/><br/>[przykład .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Pobierz szczegóły zadania i Zarządzaj zadaniami |[Zarządzanie zadaniami za pomocą wersji 2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Zarządzanie zadaniami za pomocą wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Znane problemy

* Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-sdk), interfejsu wiersza polecenia lub jednego z obsługiwanych zestawów SDK.
* Musisz zainicjować obsługę jednostek zarezerwowanych multimediów (MRUs) na koncie, aby kontrolować współbieżność i wydajność zadań, w szczególności na potrzeby analizy wideo lub audio. Aby uzyskać więcej informacji, zobacz [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Skalowanie przetwarzania multimediów). MRUs można zarządzać za pomocą [interfejsu wiersza polecenia 2,0 dla Media Services v3](media-reserved-units-cli-how-to.md)przy użyciu [Azure Portal](../previous/media-services-portal-scale-media-processing.md)lub przy użyciu [interfejsów API v2](../previous/media-services-dotnet-encoding-units.md). Należy udostępnić MRUs, niezależnie od tego, czy są Media Services używane interfejsy API w wersji 2 i v3.
* Media Services jednostek utworzonych za pomocą interfejsu API V3 nie można zarządzać za pomocą interfejsu API v2.  
* Nie zaleca się zarządzania jednostkami utworzonymi przy użyciu interfejsów API v2 za pośrednictwem interfejsów API v3. Poniżej przedstawiono przykłady różnic, które sprawiają, że jednostki w dwóch wersjach są niezgodne:   
    * Zadania i zadania utworzone w wersji 2 nie są wyświetlane w programie v3, ponieważ nie są skojarzone z przekształceniem. Zaleca się przełączenie na transformacje v3 i zadania. Podczas przełączenia będzie konieczne monitorowanie zadań numerów porządkowych określających w wersji 2.
    * Kanały i programy utworzone przy użyciu wersji 2 (które są mapowane na zdarzenia na żywo i wyjście na żywo w programie v3) nie mogą być nadal zarządzane przy użyciu wersji 3. Zalecenie polega na przełączeniu się do wydarzeń na żywo i danych wyjściowych na żywo w wygodnym stopniu kanału.<br/>Obecnie nie można migrować ciągle uruchomionych kanałów.  

> [!NOTE]
> Ta strona będzie utrzymywana, gdy zespół Media Services wprowadza dalsze ulepszenia interfejsów API v3 i rozwiązuje luki między wersjami.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

Aby sprawdzić, jak łatwo rozpocząć kodowanie i strumieniowe przesyłanie plików wideo, zobacz [Strumieniowe przesyłanie plików](stream-files-dotnet-quickstart.md). 

