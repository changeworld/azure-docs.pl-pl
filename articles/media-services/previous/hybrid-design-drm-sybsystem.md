---
title: Projekt hybrydowego subsystem(s) DRM przy użyciu usługi Azure Media Services | Dokumentacja firmy Microsoft
description: W tym temacie omówiono hybrydowego projekt subsystem(s) DRM przy użyciu usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 1584dab96d1cd772bf04620c68dbe1f133304a1c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="hybrid-design-of-drm-subsystems"></a>Projekt hybrydowego DRM subsystem(s)

W tym temacie omówiono hybrydowego projekt subsystem(s) DRM przy użyciu usługi Azure Media Services.

## <a name="overview"></a>Przegląd

Usługa Azure Media Services obsługuje następujące trzy systemu DRM:

* PlayReady
* Widevine (moduły)
* FairPlay

Obsługa DRM obejmuje szyfrowania DRM (dynamicznego szyfrowania) i dostarczania licencji przy użyciu usługi Azure Media Player obsługi wszystkich DRMs 3 jako odtwarzacza przeglądarkę zestawu SDK.

Szczegółowe traktowania DRM/CENC podsystemu projekt i implementację, zobacz dokument zatytułowany [CENC z wieloma DRM i kontrolą dostępu](media-services-cenc-with-multidrm-access-control.md).

Mimo że firma Microsoft oferuje pełnej obsługi dla trzech systemach DRM, czasami klienci muszą różnych części własnej infrastruktury i podsystemy oprócz usługi Azure Media Services umożliwia tworzenie hybrydowych Podsystem DRM.

Poniżej są często zadawane pytania poprosił o ponowne klientów:

* "Czy za pomocą własnych serwerów licencji DRM?" (W tym przypadku klienci ma zainwestowały w farmie serwerów licencji DRM z logiki biznesowej osadzonych).
* "Czy za pomocą tylko z dostarczania licencji DRM w usłudze Azure Media Services bez hostowanie zawartości w AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modułowość platformy AMS DRM

W ramach kompleksowe wideo platformy w chmurze usługi Azure Media Services DRM ma projektu z elastyczność i Modułowość pamiętać. Za pomocą dowolnego z następujących kombinacji różnych opisane w poniższej tabeli (następuje wyjaśnienie notacji używany w tabeli), można użyć usługi Azure Media Services. 

|**Hosting zawartości i źródła**|**Szyfrowanie zawartości**|**Dostarczanie licencji DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Innych firm|
|AMS|Innych firm|AMS|
|AMS|Innych firm|Innych firm|
|Innych firm|Innych firm|AMS|

### <a name="content-hosting--origin"></a>Hosting zawartości i źródła

* AMS: zawartości wideo jest obsługiwana w AMS i przesyłania strumieniowego odbywa się za pośrednictwem usługi AMS punktów końcowych przesyłania strumieniowego (ale nie zawsze dynamicznego tworzenia pakietów).
* Innych firm: wideo jest obsługiwana i dostarczane na platformie przesyłania strumieniowego innych firm poza AMS.

### <a name="content-encryption"></a>Szyfrowanie zawartości

* AMS: szyfrowanie zawartości jest wykonywane dynamicznie/na żądanie za pomocą szyfrowania dynamicznego AMS.
* Innych firm: szyfrowanie zawartości jest wykonywane poza AMS przy użyciu przetwarzania wstępnego przepływ pracy.

### <a name="drm-license-delivery"></a>Dostarczanie licencji DRM

* AMS: Licencji DRM są dostarczane przez usługę dostarczania licencji AMS.
* Innych firm: Licencji DRM są dostarczane przez serwer licencji DRM innych firm, poza AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Skonfiguruj oparte na danego scenariusza hybrydowego

### <a name="content-key"></a>Klucz zawartości

Poprzez konfigurację klucz zawartości można kontrolować następujące atrybuty AMS szyfrowania dynamicznego i usługi dostarczania licencji AMS:

* Klucz zawartości, używany do szyfrowania dynamicznego DRM.
* Zawartość licencji DRM dostarczanej przez usługi dostarczania licencji: klucz zawartości, ograniczenia i uprawnienia.
* Typ **ograniczenia zasad autoryzacji klucza zawartości**: Otwórz adres IP lub ograniczenie tokenu.
* Jeśli **tokenu** typu **ograniczenia zasad autoryzacji klucza zawartości jest używany**, **ograniczenia zasad autoryzacji klucza zawartości** muszą zostać spełnione przed wystawieniem licencji.

### <a name="asset-delivery-policy"></a>Zasady dostarczania elementu zawartości

Za pomocą konfiguracji zasad dostarczania elementów zawartości można kontrolować następujące atrybuty, które są używane przez AMS Pakowarka dynamicznego i szyfrowania dynamicznego punktu końcowego przesyłania strumieniowego AMS:

* Protokół i kombinacja szyfrowania DRM przesyłania strumieniowego, takich jak ŁĄCZNIKI w obszarze CENC (PlayReady i Widevine), funkcje smooth streaming w obszarze PlayReady, HLS w ramach Widevine lub PlayReady.
* Domyślne/osadzony licencji dostarczania adresów URL dla każdego DRMs związane.
* Określa, czy licencję adresy URL pozyskiwania (LA_URLs) w DASH MPD lub HLS odtwarzania zawierają ciąg zapytania klucza identyfikator (KĄCIK) Widevine i FairPlay, odpowiednio.

## <a name="scenarios-and-samples"></a>Scenariusze i przykłady

W oparciu o wyjaśnienia w poprzedniej sekcji, w następujących scenariuszach hybrydowych pięć użyć odpowiednich **klucz zawartości**-**zasad dostarczania elementów zawartości** kombinacje konfiguracji (przykłady wymienionych w ostatniej kolumnie wykonaj tabeli):

|**Hosting zawartości i źródła**|**Szyfrowanie DRM**|**Dostarczanie licencji DRM**|**Skonfiguruj klucz zawartości**|**Konfigurowanie zasad dostarczania elementów zawartości**|**próbki**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Yes|Yes|Przykład 1|
|AMS|AMS|Innych firm|Yes|Yes|Przykład 2|
|AMS|Innych firm|AMS|Yes|Nie|Przykład 3|
|AMS|Innych firm|Poza|Nie|Nie|Przykład 4|
|Innych firm|Innych firm|AMS|Yes|Nie|    

W przykładach działa ochrona PlayReady kreska i smooth streaming. Wideo poniżej adresy URL są smooth streaming adresów URL. Aby uzyskać odpowiednie adresy URL DASH, Dołącz tylko "(format = mpd-time-csf)". Można użyć [azure media test player](http://aka.ms/amtest) do testowania w przeglądarce. Umożliwia konfigurowanie który przesyłania strumieniowego protokół do użycia, w ramach których techniczna. Obsługuje PlayReady za pośrednictwem EME, IE11 i Microsoft Edge w systemie Windows 10. Aby uzyskać więcej informacji, zobacz [szczegółowe informacje na temat narzędzia test](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Przykład 1

* Adres URL źródła (podstawowy): https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH & smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (kreska): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* LA_URL FairPlay (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Przykład 2

* Adres URL źródła (podstawowy): http://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH & smooth): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Przykład 3

* Źródłowy adres URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Przykład 4

* Źródłowy adres URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & smooth): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Podsumowanie

Podsumowując składniki usługi Azure Media Services DRM są elastyczne, możesz użyć ich w scenariuszu hybrydowym odpowiednio konfigurując klucz zawartości i zasady dostarczania elementu zawartości, zgodnie z opisem w tym temacie.

## <a name="next-steps"></a>Kolejne kroki
Ścieżki szkoleniowe dotyczące usługi Media widoku.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

