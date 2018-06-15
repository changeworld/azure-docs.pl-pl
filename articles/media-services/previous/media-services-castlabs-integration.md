---
title: Na potrzeby dostarczania licencji Widevine do usługi Azure Media Services przy użyciu castLabs | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak Azure Media Services (AMS) umożliwia dostarczanie dynamicznie szyfrowanych przez AMS z PlayReady i Widevine DRMs strumienia. Licencja PlayReady pochodzi z serwera licencji Media Services PlayReady i licencji Widevine jest dostarczany przez serwer licencji castLabs.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: cfowler
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: aff5b94840e63176358d64a535c9cc0dd9ec617a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788501"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Korzystanie z castLabs w celu dostarczania licencji Widevine do usługi Azure Media Services
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Przegląd
W tym artykule opisano, jak Azure Media Services (AMS) umożliwia dostarczanie dynamicznie szyfrowanych przez AMS z PlayReady i Widevine DRMs strumienia. Licencja PlayReady pochodzi z serwera licencji Media Services PlayReady i licencji Widevine jest dostarczany przez **castLabs** serwera licencji.

Do odtwarzania przesyłania strumieniowego zawartości chronionej przez CENC (PlayReady i Widevine), można użyć [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Zobacz [dokumentu AMP](http://amp.azure.net/libs/amp/latest/docs/) szczegółowe informacje.

Poniższy diagram ilustruje wysokiego poziomu usługi Azure Media Services i Architektura integracji castLabs.

![Integracja](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Typowy systemem
* Nośnik zawartość jest przechowywana w AMS.
* Identyfikatory klucz zawartości kluczy są przechowywane w castLabs i AMS.
* castLabs i AMS ma token uwierzytelniania wbudowane. W poniższych sekcjach omówiono tokeny uwierzytelniania. 
* Gdy klient zażąda strumienia wideo, zawartości dynamicznej jest szyfrowana za **Common Encryption** (CENC) i dynamicznie opakowane przez AMS Smooth Streaming i KRESKI. Możemy również zapewniać PlayReady M2TS podstawowe strumienia szyfrowania dla protokołu HLS protokołu przesyłania strumieniowego.
* Licencja PlayReady są pobierane z serwera licencji usług AMS i licencji Widevine są pobierane z serwera licencji castLabs. 
* Odtwarzacz automatycznie decyduje, licencji można pobrać oparciu o możliwości platformy klienta. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Generowanie tokenu uwierzytelniania dla pobierania licencji
Zarówno castLabs, jak i AMS obsługuje format tokenu JWT (JSON Web Token) użyty do autoryzacji licencji. 

### <a name="jwt-token-in-ams"></a>Token JWT w AMS
W poniższej tabeli opisano tokenu JWT w AMS. 

| Wystawca | Ciąg wystawcy z wybranego Secure Token Service (STS) |
| --- | --- |
| Grupy odbiorców |Ciąg odbiorców z STS używane |
| Oświadczenia |Zestaw oświadczeń |
| Nie wcześniej niż |Uruchom ważności tokenu |
| Wygasa |Zakończenia ważności tokenu |
| Elementu SigningCredentials |Klucz współużytkowany serwera licencji PlayReady, castLabs serwera licencji i STS, może ona konfiguracji symetrycznej lub asymetrycznej klucza. |

### <a name="jwt-token-in-castlabs"></a>Token JWT w castLabs
W poniższej tabeli opisano tokenu JWT w castLabs. 

| Name (Nazwa) | Opis |
| --- | --- |
| optData |Ciąg JSON zawierający informacje o Tobie. |
| CRT |Ciąg JSON zawierający informacje o zawartości, jego informacje o i odtwarzania praw licencyjnych. |
| IAT |Bieżący element datetime w epoki. |
| jti |Unikatowy identyfikator o token (token, co może być użyte tylko raz w systemie castLabs). |

## <a name="sample-solution-set-up"></a>Przykładowe rozwiązanie — Konfiguracja
[Przykładowe rozwiązanie](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) składa się z dwóch projektów:

* Aplikacja konsoli, który może służyć do ustawienia ograniczeń DRM zasób już pozyskiwane PlayReady i Widevine.
* Aplikacja sieci Web, która przekazuje limit tokeny, których może być traktowany jako bardzo uproszczonej wersji usługi tokenu Zabezpieczającego.

Korzystanie z aplikacji konsoli:

1. Zmień app.config można skonfigurować poświadczenia AMS, poświadczenia castLabs, konfiguracji usługi STS i klucza wspólnego.
2. Przekaż zasób do AMS.
3. Pobierz identyfikator UUID z przekazanego zasobów i zmienić 32 wiersz w pliku Program.cs:
   
      var objIAsset = _kontekstu. Assets.Where (x = > x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault();
4. Użyj IDśrodkaTrwałego dla nazw zasobów castLabs systemu (44 wiersz w pliku Program.cs).
   
   Należy ustawić IDśrodkaTrwałego dla **castLabs**; musi on być unikatowy ciąg alfanumeryczny.
5. Uruchom program.

Aby użyć aplikacji sieci Web (STS):

1. Zmień sprzedawcy castlabs ustawienia pliku web.config Identyfikatora, konfiguracji usługi STS i klucza wspólnego.
2. Wdróż witryn sieci Web Azure.
3. Przejdź do witryny sieci Web.

## <a name="playing-back-a-video"></a>Odtwarzanie wideo
Do odtwarzania wideo zaszyfrowane za pomocą wspólnego szyfrowania (PlayReady i Widevine), można użyć [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Podczas uruchamiania aplikacji konsoli, powtarzana Identyfikatora klucza zawartości i adresu URL manifestu.

1. Otwórz nową kartę, a następnie uruchom z STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Przejdź do [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Wklej adres URL przesyłania strumieniowego.
4. Kliknij przycisk **zaawansowane opcje** wyboru.
5. W **ochrony** listy rozwijanej wybierz PlayReady i Widevine.
6. Wklej token pochodzący z sieci usługi STS w polu tekstowym tokenu. 
   
   CastLab serwer licencji nie jest konieczne "Bearer =" prefiks przed tokenu. Dlatego należy usunąć przed przesłaniem tokenu.
7. Zaktualizuj odtwarzacza.
8. Powinien być odtwarzanie wideo.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

