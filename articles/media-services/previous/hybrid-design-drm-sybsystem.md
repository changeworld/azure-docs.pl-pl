---
title: Projekt hybrydowego subsystem(s) DRM przy użyciu usługi Azure Media Services | Dokumentacja firmy Microsoft
description: W tym temacie omówiono hybrydowy projekt subsystem(s) DRM przy użyciu usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan;juliako
ms.openlocfilehash: 5c86a49cd9dc26f724de12ed2e5e77e645e4ab53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466599"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hybrydowy projekt podsystemu DRM 

W tym temacie omówiono hybrydowy projekt subsystem(s) DRM przy użyciu usługi Azure Media Services.

## <a name="overview"></a>Omówienie

Usługa Azure Media Services obsługuje następujące trzy systemu DRM:

* PlayReady
* Widevine (modułowy)
* FairPlay

Obsługa technologii DRM obejmuje szyfrowania DRM (szyfrowanie dynamiczne) i dostarczania licencji za pomocą usługi Azure Media Player obsługuje wszystkie 3 protokołów DRM gracza przeglądarki zestawu SDK.

Szczegółowe traktowania projekt podsystemu DRM/CENC i implementację, można znaleźć w dokumentacji pod tytułem [CENC przy użyciu technologii Multi-DRM i kontroli dostępu](media-services-cenc-with-multidrm-access-control.md).

Mimo że firma Microsoft oferuje pełną obsługę trzech systemach DRM, czasami klienci muszą używać różnych części własnych infrastrukturę i podsystemy oprócz usługi Azure Media Services do tworzenia hybrydowych podsystemu DRM.

Poniższe często zadawane pytania zadawane przez klientów:

* "Czy za pomocą własnych serwerów licencji DRM?" (W tym przypadku klienci mają poświęcił w farmie serwerów licencji DRM z logiką biznesową embedded).
* "Czy za pomocą tylko usługi dostarczania licencji DRM w usłudze Azure Media Services bez hosting zawartości usługi AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modułowość platformy AMS DRM

Jako część w chmurze kompleksową platformę wideo usługi Azure Media Services DRM korzysta elastyczność i Modułowość pamiętać. Za pomocą usługi Azure Media Services z dowolnymi z następujących kombinacji różnych opisane w poniższej tabeli (wyjaśnienie notację używaną w tabeli poniżej). 

|**Hosting zawartości źródł & o**|**Szyfrowanie zawartości**|**Dostarczanie licencji DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Innych firm|
|AMS|Innych firm|AMS|
|AMS|Innych firm|Innych firm|
|Innych firm|Innych firm|AMS|

### <a name="content-hosting--origin"></a>Hosting zawartości źródł & o

* Usługi AMS: zawartości wideo jest hostowane na platformie usługi AMS i przesyłania strumieniowego jest za pośrednictwem punktów końcowych przesyłania strumieniowego usługi AMS (ale nie zawsze dynamicznego tworzenia pakietów).
* Innych firm: wideo jest hostowane i dostarczane na platformie przesyłania strumieniowego innych firm poza usługi AMS.

### <a name="content-encryption"></a>Szyfrowanie zawartości

* Usługi AMS: szyfrowanie zawartości jest wykonywane dynamicznie/na żądanie usługi AMS szyfrowania dynamicznego.
* Innych firm: szyfrowanie zawartości odbywa się poza usługi AMS przy użyciu przetwarzania wstępnego przepływu pracy.

### <a name="drm-license-delivery"></a>Dostarczanie licencji DRM

* AMS: Licencji DRM są dostarczane przez usługi dostarczania licencji usługi AMS.
* Innych firm: Licencji DRM są dostarczane przez serwer licencji DRM innych firm, poza usługi AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurowanie oparte na scenariusza hybrydowego

### <a name="content-key"></a>Klucz zawartości

Za pomocą konfiguracji klucza zawartości można kontrolować następujące atrybuty zarówno usługi AMS dynamicznego szyfrowania, jak i usługi dostarczania licencji usługi AMS:

* Jeśli klucz zawartości jest używany do szyfrowania dynamicznego DRM.
* DRM licencji zawartości dostarczanej przez usług dostarczania licencji: prawa, klucz zawartości i ograniczenia.
* Typ **ograniczenia zasad autoryzacji klucza zawartości**: otworzyć, adres IP lub ograniczenia tokenu.
* Jeśli **tokenu** typu **ograniczenia zasad autoryzacji klucza zawartości jest używany**, **ograniczenia zasad autoryzacji klucza zawartości** muszą zostać spełnione przed wystawieniem licencji.

### <a name="asset-delivery-policy"></a>Zasady dostarczania elementu zawartości

Za pomocą konfiguracji zasad dostarczania elementu zawartości można kontrolować następujące atrybuty, które korzystają z funkcji pakowania dynamicznego usługi AMS i dynamiczne szyfrowanie punkt końcowy przesyłania strumieniowego usługi AMS:

* Przesyłania strumieniowego, protokół i kombinacji szyfrowania DRM, takich jak KRESKI w obszarze CENC (PlayReady i Widevine), smooth streaming w ramach technologii PlayReady, HLS, w ramach Widevine lub technologii PlayReady.
* Domyślne/osadzony licencji dostarczania adresów URL dla wszystkich zaangażowanych protokołów DRM.
* Czy licencji adresy URL pozyskiwania (LA_URLs) w DASH MPD lub HLS lista odtwarzania zawiera ciąg zapytania klucza identyfikator (KĄCIK) Widevine i FairPlay, odpowiednio.

## <a name="scenarios-and-samples"></a>Scenariusze i przykłady

Oparte na wyjaśnienia w poprzedniej sekcji, w następujących scenariuszach hybrydowych pięć użyć odpowiednich **klucz zawartości**-**zasad dostarczania elementów zawartości** kombinacje konfiguracji (przykłady podane w ostatniej kolumnie postępuj zgodnie z tabeli):

|**Hosting zawartości źródł & o**|**Szyfrowanie DRM**|**Dostarczanie licencji DRM**|**Konfigurowanie klucza zawartości**|**Konfigurowanie zasad dostarczania elementów zawartości**|**Przykład**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Yes|Yes|Przykład 1|
|AMS|AMS|Innych firm|Yes|Yes|Przykład 2|
|AMS|Innych firm|AMS|Yes|Nie|Przykład 3|
|AMS|Innych firm|Poza|Nie|Nie|Przykład 4|
|Innych firm|Innych firm|AMS|Yes|Nie|    

W przykładach objętych ochroną PlayReady i działa w przypadku DASH i smooth streaming. Poniższe adresy URL wideo są smooth streaming adresów URL. Aby uzyskać odpowiednie adresy URL DASH, Dołącz tylko "(format = mpd-time-csf)". Można użyć [usługi azure media test player](https://aka.ms/amtest) testowanie w przeglądarce. Umożliwia konfigurowanie protokołu przesyłania strumieniowego do użycia, w ramach której pomocy. IE11 i Microsoft Edge w systemie Windows 10 obsługują technologii PlayReady za pomocą rozszerzeń EME. Aby uzyskać więcej informacji, zobacz [szczegółowe informacje na temat narzędzia test](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Przykład 1

* Adres URL źródła (podstawowa): https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH i smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* LA_URL Widevine (kreska): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* Technologia FairPlay LA_URL (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Przykład 2

* Adres URL źródła (podstawowa): https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH i smooth): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Przykład 3

* Źródłowy adres URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH i smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Przykład 4

* Źródłowy adres URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH i smooth): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Podsumowanie

Podsumowując składników usługi Azure Media Services DRM są elastyczne, możesz użyć ich w scenariuszu hybrydowym odpowiednio konfigurując klucz zawartości i zasad dostarczania elementów zawartości, zgodnie z opisem w tym temacie.

## <a name="next-steps"></a>Kolejne kroki
Wyświetl usługi Media Services ścieżki szkoleniowe.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

