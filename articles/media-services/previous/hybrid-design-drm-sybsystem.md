---
title: Hybrydowy projekt podsystemów DRM przy użyciu usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym temacie omówiono projekt hybrydowy podsystemów DRM przy użyciu usługi Azure Media Services.
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
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: d2f4ddfbff791fbfeb2eb006a628c0fdeb4fdce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975197"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hybrydowy projekt podsystemów DRM 

W tym temacie omówiono projekt hybrydowy podsystemów DRM przy użyciu usługi Azure Media Services.

## <a name="overview"></a>Omówienie

Usługa Azure Media Services zapewnia obsługę następujących trzech systemów DRM:

* PlayReady
* Widevine (modułowy)
* FairPlay

Obsługa drm obejmuje szyfrowanie DRM (szyfrowanie dynamiczne) i dostarczanie licencji, a usługa Azure Media Player obsługuje wszystkie 3 drm jako zestaw SDK odtwarzacza przeglądarki.

Szczegółowe informacje na temat projektowania i wdrażania podsystemu DRM/CENC można znaleźć w dokumencie zatytułowanym [CENC with Multi-DRM i Access Control](media-services-cenc-with-multidrm-access-control.md).

Chociaż oferujemy pełną obsługę trzech systemów DRM, czasami klienci muszą używać różnych części własnej infrastruktury/podsystemów oprócz usługi Azure Media Services do tworzenia hybrydowego podsystemu DRM.

Poniżej znajduje się kilka typowych pytań zadawanych przez klientów:

* "Czy mogę korzystać z własnych serwerów licencji DRM?" (W tym przypadku klienci zainwestowali w farmę serwerów licencji DRM z osadzoną logiką biznesową).
* "Czy mogę używać tylko dostarczania licencji DRM w usłudze Azure Media Services bez hostingu zawartości w usłudze AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modułowość platformy AMS DRM

W ramach kompleksowej platformy wideo w chmurze usługa Azure Media Services DRM ma projekt z myślą o elastyczności i modułowości. Usługi Azure Media Services można używać z dowolną z następujących kombinacji różnych opisanych w poniższej tabeli (wyjaśnienie notacji używanej w poniższej tabeli). 

|**Hosting zawartości & pochodzenia**|**Szyfrowanie zawartości**|**Dostarczanie licencji DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Strona trzecia|
|AMS|Strona trzecia|AMS|
|AMS|Strona trzecia|Strona trzecia|
|Strona trzecia|Strona trzecia|AMS|

### <a name="content-hosting--origin"></a>Hosting zawartości & pochodzenia

* AMS: zasób wideo jest hostowany w systemie AMS, a przesyłanie strumieniowe odbywa się za pośrednictwem punktów końcowych przesyłania strumieniowego AMS (ale niekoniecznie dynamicznego pakowania).
* Strona trzecia: wideo jest hostowane i dostarczane na platformie streamingowej innej firmy poza usługą AMS.

### <a name="content-encryption"></a>Szyfrowanie zawartości

* AMS: szyfrowanie zawartości jest wykonywane dynamicznie/na żądanie przez szyfrowanie dynamiczne ams.
* Innej firmy: szyfrowanie zawartości jest wykonywane poza programem AMS przy użyciu przepływu pracy przetwarzania wstępnego.

### <a name="drm-license-delivery"></a>Dostarczanie licencji DRM

* AMS: Licencja DRM jest dostarczana przez usługę dostarczania licencji AMS.
* Strona trzecia: Licencja DRM jest dostarczana przez serwer licencji DRM innej firmy poza usługą AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurowanie na podstawie scenariusza hybrydowego

### <a name="content-key"></a>Klucz zawartości

Dzięki konfiguracji klucza zawartości można kontrolować następujące atrybuty zarówno szyfrowania dynamicznego usługi AMS, jak i usługi dostarczania licencji USŁUGI AMS:

* Klucz zawartości używany do dynamicznego szyfrowania DRM.
* Zawartość licencji DRM, która ma być dostarczana przez usługi dostarczania licencji: prawa, klucz zawartości i ograniczenia.
* Ograniczenie **zasad autoryzacji klucza zawartości: ograniczenie**otwarcia, adresu IP lub tokenu.
* Jeśli używane **jest ograniczenie zasad autoryzacji klucza zawartości**typu **tokenu,** ograniczenie **zasad autoryzacji klucza zawartości** musi zostać spełnione przed wystawieniem licencji.

### <a name="asset-delivery-policy"></a>Zasady dostarczania zasobów

Za pomocą konfiguracji zasad dostarczania zasobów można kontrolować następujące atrybuty używane przez pakiet dynamiczny usługi AMS i szyfrowanie dynamiczne punktu końcowego przesyłania strumieniowego usługi AMS:

* Protokół przesyłania strumieniowego i kombinacja szyfrowania DRM, takie jak DASH w cenc (PlayReady i Widevine), płynne przesyłanie strumieniowe w obszarze PlayReady, HLS w obszarze Widevine lub PlayReady.
* Domyślne/osadzone adresy URL dostarczania licencji dla każdego z zaangażowanych drmów.
* Niezależnie od tego, czy adresy URL pozyskiwania licencji (LA_URLs) na liście odtwarzania DASH MPD lub HLS zawierają ciąg zapytań o identyfikatorze klucza (KID) odpowiednio dla Widevine i FairPlay.

## <a name="scenarios-and-samples"></a>Scenariusze i przykłady

Na podstawie wyjaśnień w poprzedniej sekcji następujące pięć scenariuszy hybrydowych używa odpowiednich kombinacji konfiguracji**zasad dostarczania** **klucza**-zawartości (przykłady wymienione w ostatniej kolumnie są zgodne z tabelą):

|**Hosting zawartości & pochodzenia**|**Szyfrowanie DRM**|**Dostarczanie licencji DRM**|**Konfigurowanie klucza zawartości**|**Konfigurowanie zasad dostarczania zasobów**|**Przykładowe**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Tak|Tak|Przykład 1|
|AMS|AMS|Strona trzecia|Tak|Tak|Przykład 2|
|AMS|Strona trzecia|AMS|Tak|Nie|Przykład 3|
|AMS|Strona trzecia|Poza|Nie|Nie|Przykład 4|
|Strona trzecia|Strona trzecia|AMS|Tak|Nie|    

W przykładach ochrona PlayReady działa zarówno dla DASH, jak i płynnego przesyłania strumieniowego. Poniższe adresy URL wideo są płynnymi adresami URL przesyłania strumieniowego. Aby uzyskać odpowiednie adresy URL DASH, wystarczy dołączyć "(format=mpd-time-csf)". Można użyć [azure media test player](https://aka.ms/amtest) do testowania w przeglądarce. To pozwala skonfigurować, który protokół przesyłania strumieniowego do wykorzystania, w ramach których tech. IE11 i Microsoft Edge w systemie Windows 10 obsługują playready za pośrednictwem EME. Aby uzyskać więcej informacji, zobacz [szczegółowe informacje na temat narzędzia testowego](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Przykład 1

* Źródło (podstawowy) adres URL:https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* LA_URL PlayReady (DASH & gładka):https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* LA_URL Widevine (DASH):https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* LA_URL FairPlay (HLS):https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Przykład 2

* Źródło (podstawowy) adres URL:https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* LA_URL PlayReady (DASH & gładka):http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Przykład 3

* Źródłowy adres URL:https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* LA_URL PlayReady (DASH & gładka):https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Przykład 4

* Źródłowy adres URL:https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* LA_URL PlayReady (DASH & gładka):https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="summary"></a>Podsumowanie

Podsumowując składniki usługi Azure Media Services DRM są elastyczne, można ich używać w scenariuszu hybrydowym, poprawnie konfigurując zasady dostarczania klucza zawartości i zasobów, zgodnie z opisem w tym temacie.

## <a name="next-steps"></a>Następne kroki
Wyświetlanie ścieżek szkoleniowych usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

