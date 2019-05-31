---
title: Usługa Azure Media Services v3 — informacje o wersji | Dokumentacja firmy Microsoft
description: Aby uzyskać najnowsze informacje o najnowszych zmianach, ten artykuł zawiera najnowsze aktualizacje usługi Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 05/29/2019
ms.author: juliako
ms.openlocfilehash: a5ce6467a547b9fded03b3b5a6f40f84d29d3618
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385143"
---
# <a name="azure-media-services-v3-release-notes"></a>Usługa Azure Media Services v3 — informacje o wersji

Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

* Zainstalowane najnowsze wersje
* Znane problemy
* Poprawki błędów
* Przestarzałe funkcje

## <a name="known-issues"></a>Znane problemy

> [!NOTE]
> Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-sdk), interfejsu wiersza polecenia lub w jednym z obsługiwanych zestawów SDK.

Aby uzyskać więcej informacji, zobacz [wskazówek dotyczących migracji do przenoszenia z usługi Media Services v2 do v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="may-2019"></a>Maja 2019 r

### <a name="hls-v4-and-above-support-in-dynamic-packaging"></a>HLS w wersji 4 lub nowszym pomocy technicznej w funkcji dynamicznego tworzenia pakietów 

Dodano HLS (w wersji 4 lub nowszej) obsługuje w funkcji dynamicznego tworzenia pakietów dla zasobów, które mają wiele ścieżki audio z wieloma koderów-dekoderów i języków.

Aby uzyskać więcej informacji, zobacz [funkcję dynamicznego tworzenia pakietów](dynamic-packaging-overview.md).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Korea, pary regionalne jest otwarty dla usługi Media Services 

Usługa Media Services jest teraz dostępna w regionach Korea Środkowa i Korea Południowa. 

Aby uzyskać więcej informacji, zobacz [chmur i regionów, w których usługi Media Services v3 istnieje](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Ulepszenia wydajności

Dodano aktualizacje, które obejmują udoskonalenia w zakresie wydajności usługi Media Services.

* Maksymalny rozmiar pliku do przetwarzania został zaktualizowany. Zobacz, [przydziały i ograniczenia](limits-quotas-constraints.md).
* [Kodowanie poprawy szybkości](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>2019 kwietnia

### <a name="new-presets"></a>Nowe ustawienia domyślne

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) została dodana do ustawień wstępnych wbudowany analizatora.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) została dodana do ustawienia wstępne kodera wbudowanych. Aby uzyskać więcej informacji, zobacz [kodowania zawartości](cae-experimental.md). 

## <a name="march-2019"></a>Marca 2019 r

Dynamiczne pakowanie teraz obsługuje Dolby Atmos. Aby uzyskać więcej informacji, zobacz [kodery-dekodery Audio obsługiwane przez funkcję dynamicznego tworzenia pakietów](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

Teraz można określić listę filtrów zasobów lub konta, które będzie dotyczyć Twojego lokalizatora przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [skojarzyć filtrów z lokalizatora przesyłania strumieniowego](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Lutego 2019 r

Usługa Media Services w wersji 3 jest teraz obsługiwana w chmurach narodowych platformy Azure. Nie wszystkie funkcje są jeszcze dostępne we wszystkich chmurach. Aby uzyskać więcej informacji, zobacz [chmur i regionów, w których usługa Azure Media Services v3 istnieje](azure-clouds-regions.md).

[Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) zdarzeń został dodany do schematów usługi Azure Event Grid w usłudze Media Services.

## <a name="january-2019"></a>Styczeń 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Pliki usługi Media Encoder Standard i MPI 

Podczas kodowania za pomocą usługi Media Encoder Standard do tworzenia plików w formacie MP4, nowy plik .mpi jest generowane i dodawane do wyjściowego elementu zawartości. Ten plik MPI ma na celu poprawę wydajności [funkcję dynamicznego tworzenia pakietów](dynamic-packaging-overview.md) i przesyłania strumieniowego scenariuszy.

Nie należy modyfikować lub usuń plik MPI lub wykonać żadnych zależności usługi o istnieniu (lub nie) z takiego pliku.

## <a name="december-2018"></a>Grudzień 2018 r.

Aktualizacji z wersji ogólnie dostępnej wersji 3 interfejsu API:
       
* **PresentationTimeRange** właściwości nie są już wymagane, aby **filtry zasobów** i **filtrów kont**. 
* $Top i $skip zapytań opcje **zadania** i **przekształca** został usunięty i dodano $orderby. Podczas dodawania nowych funkcji szeregowania wykryto, że opcje $top i $skip przypadkowo miał została udostępniona wcześniej nawet, jeśli nie są zaimplementowane.
* Ponownie włączono rozszerzalności wyliczenia. Ta funkcja została włączona w wersjach preview SDK i przypadkowo została wyłączona w wersji Ogólnodostępnej.
* Zmieniono dwa wstępnie zdefiniowane zasady przesyłania strumieniowego. **SecureStreaming** jest teraz **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** jest teraz **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Listopada 2018 r.

Moduł interfejsu wiersza polecenia 2.0 jest teraz dostępna dla [usług ogólnie dostępnych usług Azure Media Services v3](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Nowe polecenia

- [konta usługi ams az](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [zasób usługi ams az](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [Zadanie usługi ams az](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [AZ ams na żywo dane wyjściowe](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [usługi ams az account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) — pozwala na zarządzanie jednostek zarezerwowanych multimediów. Aby uzyskać więcej informacji, zobacz [jednostek zarezerwowanych multimediów skalowania](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nowe funkcje i zmiany powodujące niezgodność

#### <a name="asset-commands"></a>Polecenia trwałego

- ```--storage-account``` i ```--container``` dodane argumenty.
- Wartości domyślne dla uprawnienia (odczyt) i czas wygaśnięcia (teraz + 23 godz.) w ```az ams asset get-sas-url``` polecenia dodanego.

#### <a name="job-commands"></a>Polecenia zadania

- ```--correlation-data``` i ```--label``` dodane argumenty
- ```--output-asset-names``` zmieniona na ```--output-assets```. Teraz przyjmuje rozdzielonej spacjami listy zasobów w "assetName = label" format. Zasobu bez etykiety mogą być wysyłane następująco: "assetName =".

#### <a name="streaming-locator-commands"></a>Przesyłanie strumieniowe poleceń lokalizatora

- ```az ams streaming locator``` podstawowy polecenia zastąpione ```az ams streaming-locator```.
- ```--streaming-locator-id``` i ```--alternative-media-id support``` dodane argumenty.
- ```--content-keys argument``` argument zaktualizowany.
- ```--content-policy-name``` zmieniona na ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Przesyłanie strumieniowe poleceń zasad

- ```az ams streaming policy``` podstawowy polecenia zastąpione ```az ams streaming-policy```.
- Obsługa szyfrowania parametrów, w ```az ams streaming-policy create``` dodane.

#### <a name="transform-commands"></a>Przekształcanie poleceń

- ```--preset-names``` argumentu zamieniony ```--preset```. Teraz można ustawić tylko 1 wyjście/wstępnie ustawionych w danym momencie (Aby dodać więcej Uruchom ```az ams transform output add```). Ponadto można ustawić niestandardowe StandardEncoderPreset, przekazując ścieżkę do niestandardowego JSON.
- ```az ams transform output remove``` mogą być wykonywane przez przekazanie indeksu danych wyjściowych do usunięcia.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumenty, które dodano w ```az ams transform create``` i ```az ams transform output add``` poleceń.

## <a name="october-2018---ga"></a>Października 2018 — ogólna dostępność

W tej sekcji opisano aktualizacje usługi Azure Media Services (AMS) października.

### <a name="rest-v3-ga-release"></a>Wersja Ogólnodostępna v3 REST

[Wersja Ogólnodostępna v3 REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) zawiera więcej interfejsów API na żywo, filtry na poziomie manifestu konta/zasobów i obsługa technologii DRM.

#### <a name="azure-resource-management"></a>Azure Resource Management 

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

    Po utworzeniu zdarzenia na żywo, możesz teraz pobieranie 4 adresów URL pozyskiwania. Pozyskiwanie 4 adresy URL są niemal identyczne, mają ten sam token przesyłania strumieniowego (AppId), tylko część numer portu jest inny. Są dwa adresy URL podstawowego i zapasowego dla RTMPS. 
- Obsługa transkodowanie 24-godzinnym. 
- Ulepszona obsługa sygnalizowanie ad w RTMP za pośrednictwem SCTE35.

#### <a name="improved-event-grid-support"></a>Ulepszona obsługa usługi Event Grid

Możesz zobaczyć następujące usługi Event Grid obsługuje ulepszenia:

- Integracja usługi Azure Event Grid dla łatwiejsze tworzenie aplikacji przy użyciu aplikacji logiki i Azure Functions. 
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

### <a name="net-sdk"></a>Zestaw SDK .NET

Następujące funkcje są obecne w zestawie SDK platformy .NET:

* **Przekształca** i **zadań** do kodowania i analizowanie zawartości multimedialnej. Aby uzyskać przykłady, zobacz [Stream pliki](stream-files-tutorial-with-api.md) i [analizy](analyze-videos-tutorial-with-api.md).
* **Lokalizatory przesyłania strumieniowego** publikowania i przesyłanie strumieniowe zawartości na urządzeniach użytkowników końcowych
* **Przesyłanie strumieniowe zasady** i **zasady kluczy zawartości** skonfigurować dostarczania kluczy i ochrona zawartości (DRM), podczas dostarczania zawartości.
* **Wydarzeń na żywo** i **na żywo dane wyjściowe** skonfigurować pozyskiwania i zarchiwizowanie mających zawartości transmisji strumieniowej na żywo.
* **Zasoby** do przechowywania i publikowanie zawartości multimedialnej w usłudze Azure Storage. 
* **Punkty końcowe przesyłania strumieniowego** konfigurować i skalować funkcję dynamicznego tworzenia pakietów, szyfrowania i przesyłania strumieniowego dla zawartości multimedialnej na żywo i na żądanie.

### <a name="known-issues"></a>Znane problemy

* Podczas przesyłania zadania, można określić pozyskiwania źródłowy plik wideo przy użyciu adresów URL HTTPS, adresy URL sygnatury dostępu Współdzielonego lub ścieżki do plików znajdujących się w usłudze Azure Blob storage. Obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu przez adresy URL HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Prześlij opinię i pobieranie aktualizacji

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](media-services-overview.md)
