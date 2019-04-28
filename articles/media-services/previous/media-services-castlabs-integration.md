---
title: Korzystanie z castLabs w celu dostarczania licencji Widevine do usługi Azure Media Services | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak Azure Media Services (AMS) umożliwia dostarczanie dynamicznie szyfrowanych przez usługi AMS przy użyciu technologii PlayReady i Widevine protokołów DRM strumienia. Licencja PlayReady pochodzi z serwera licencji PlayReady usługi Media Services i licencji Widevine są dostarczane przez serwer licencji castLabs.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: dfb82e91b0f65b85d34b7e20d57ed9929469321f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232588"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Korzystanie z castLabs w celu dostarczania licencji Widevine do usługi Azure Media Services 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Omówienie

W tym artykule opisano, jak Azure Media Services (AMS) umożliwia dostarczanie dynamicznie szyfrowanych przez usługi AMS przy użyciu technologii PlayReady i Widevine protokołów DRM strumienia. Licencja PlayReady pochodzi z serwera licencji PlayReady usługi Media Services i licencji Widevine są dostarczane przez **castLabs** serwera licencji.

Aby odtworzyć ponownie przesyłania strumieniowego zawartości chronionej przez CENC (PlayReady i Widevine), można użyć [usługi Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html). Zobacz [dokumentu AMP](https://amp.azure.net/libs/amp/latest/docs/) Aby uzyskać szczegółowe informacje.

Poniższy diagram przedstawia wysokiego poziomu usługi Azure Media Services i architektura integracyjna castLabs.

![Integracja](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Typowym systemie Konfigurowanie

* Zawartości multimedialnej są przechowywane w usługi AMS.
* Identyfikatory klucz zawartości kluczy są przechowywane w castLabs i usługi AMS.
* castLabs i AMS mają wbudowane uwierzytelnianie przy użyciu tokenów. W poniższych sekcjach omówiono tokeny uwierzytelniania. 
* Gdy klient zażąda do przesyłania strumieniowego wideo, zawartość dynamicznie jest szyfrowana za pomocą **szyfrowania Common Encryption** (CENC) i dynamicznie opakowane przez usługi AMS Smooth Streaming i KRESKI. Możemy również zapewniać PlayReady M2TS szyfrowanie podstawowe strumienia HLS protokołu przesyłania strumieniowego.
* Licencja PlayReady są pobierane z serwera licencji usługi AMS i licencji Widevine są pobierane z serwera licencji castLabs. 
* Usługa Media Player automatycznie decyduje, licencji, które można pobrać oparciu o możliwości platformy klienta. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Generowanie tokenu uwierzytelniania w celu uzyskania licencji

Zarówno castLabs, jak i usługi AMS obsługuje format tokenu JWT (JSON Web Token), służące do autoryzowania licencji. 

### <a name="jwt-token-in-ams"></a>Token JWT w usługi AMS

W poniższej tabeli opisano tokenu JWT w usługi AMS. 

| Wystawca | Ciąg wystawcy z wybranego Secure Token Service (STS) |
| --- | --- |
| Grupy odbiorców |Ciąg odbiorców z używane usługi STS |
| Oświadczenia |Zestaw oświadczeń |
| nie wcześniej niż |Rozpocznij ważności tokenu |
| Wygasa |Zakończenia ważności tokenu |
| SigningCredentials |Klucz, który jest współużytkowana przez serwer licencji PlayReady, castLabs serwer licencji i usługi STS, możliwe, konfiguracji symetrycznej lub asymetrycznej klucza. |

### <a name="jwt-token-in-castlabs"></a>Token JWT w castLabs

W poniższej tabeli opisano tokenu JWT w castLabs. 

| Name (Nazwa) | Opis |
| --- | --- |
| optData |Ciąg JSON zawierający informacje o Tobie. |
| CRT |Ciąg JSON zawierający informacje o zawartości, jej informacji i odtwarzanie praw licencyjnych. |
| IAT |Bieżąca data i godzina w epoki. |
| jti |Unikatowy identyfikator o ten token (każdy token może być użyte tylko raz w systemie castLabs). |

## <a name="sample-solution-setup"></a>Przykładowe rozwiązanie Instalatora

[Przykładowe rozwiązanie](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) obejmuje dwa projekty:

* Aplikacja konsoli, która może służyć do Ustawianie ograniczeń DRM na już pozyskiwane elementu zawartości, zarówno dla technologii PlayReady i Widevine.
* Aplikacja sieci Web, która przekazuje się tokenów, które może być traktowany jak bardzo uproszczony wersji usługi tokenu Zabezpieczającego.

Korzystanie z aplikacji konsoli:

1. Zmienianie pliku app.config, aby skonfigurować poświadczenia usługi AMS, castLabs poświadczeń, konfiguracji usługi STS i klucz współużytkowany.
2. Przekaż element zawartości do usługi AMS.
3. Uzyskaj identyfikator UUID z przekazanego zasobów, a następnie zmień 32 wiersza w pliku Program.cs:
   
      var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();
4. Na użytek AssetId nazwy zasobu w systemie castLabs (44 wiersza w pliku Program.cs).
   
   Należy ustawić AssetId dla **castLabs**; musi być unikatowy ciąg alfanumeryczny.
5. Uruchom program.

Aby użyć aplikacji sieci Web (STS):

1. Zmień plik web.config w instalacji castlabs handlowca identyfikator, konfiguracją usługi STS i klucz współużytkowany.
2. Wdrażanie w usłudze Azure Websites.
3. Przejdź do witryny sieci Web.

## <a name="playing-back-a-video"></a>Odtwarzanie filmu wideo

Aby odtworzyć wideo zaszyfrowany przy użyciu szyfrowania common encryption (PlayReady i Widevine), można użyć [usługi Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html). Podczas uruchamiania aplikacji konsolowej, powtarzane Identyfikatora klucza zawartości i adres URL manifestu.

1. Otwórz nową kartę, a następnie uruchom usługi STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Przejdź do [usługi Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Wklej adres URL przesyłania strumieniowego.
4. Kliknij przycisk **zaawansowane opcje** pola wyboru.
5. W **ochrony** listy rozwijanej wybierz PlayReady i Widevine.
6. Wklej token uzyskany z usługi STS w polu tekstowym tokenu. 
   
   Serwer licencji castLab nie jest konieczne "Bearer =" prefiks przed tokenu. Dlatego należy usunąć przed przesłaniem tokenu.
7. Zaktualizuj odtwarzacza.
8. Należy odtwarzanie filmu wideo.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

