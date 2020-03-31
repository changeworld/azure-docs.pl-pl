---
title: Używanie castLabs do dostarczania licencji Widevine do usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak można użyć usługi Azure Media Services (AMS) do dostarczania strumienia, który jest dynamicznie szyfrowany przez usługę AMS za pomocą technologii DrM PlayReady i Widevine.
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
ms.author: Juliako
ms.reviewer: willzhan
ms.openlocfilehash: 29a344c739d8d99da2e5c81d41a11c601e48022e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969144"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Korzystanie z castLabs w celu dostarczania licencji Widevine do usługi Azure Media Services 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Omówienie

W tym artykule opisano, jak można użyć usługi Azure Media Services (AMS) do dostarczania strumienia, który jest dynamicznie szyfrowany przez usługę AMS za pomocą technologii DrM PlayReady i Widevine. Licencja PlayReady pochodzi z serwera licencji Programu Media Services PlayReady, a licencja Widevine jest dostarczana przez serwer licencji **castLabs.**

Aby odtwarzać zawartość strumieniową chroniona przez CENC (PlayReady i/lub Widevine), można użyć [programu Azure Media Player](https://aka.ms/azuremediaplayer). Szczegółowe informacje można znaleźć w [dokumencie AMP.](https://amp.azure.net/libs/amp/latest/docs/)

Na poniższym diagramie przedstawiono architekturę integracji usługi Azure Media Services i castLabs wysokiego poziomu.

![Integracji](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Typowa konfiguracja systemu

* Zawartość multimedialna jest przechowywana w programie AMS.
* Identyfikatory kluczy zawartości są przechowywane zarówno w castLabs, jak i AMS.
* castLabs i AMS mają wbudowane uwierzytelnianie tokenu. W poniższych sekcjach omówiono tokeny uwierzytelniania. 
* Gdy klient żąda przesyłania strumieniowego wideo, zawartość jest dynamicznie szyfrowana za pomocą **szyfrowania wspólnego** (CENC) i dynamicznie pakowana przez usługę AMS do płynnego przesyłania strumieniowego i DASH. Dostarczamy również podstawowe szyfrowanie strumienia PlayReady M2TS dla protokołu przesyłania strumieniowego HLS.
* Licencja PlayReady jest pobierana z serwera licencji AMS, a licencja Widevine jest pobierana z serwera licencji castLabs. 
* Program Media Player automatycznie decyduje o tym, którą licencję pobrać na podstawie możliwości platformy klienta. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Generowanie tokenu uwierzytelniania w celu uzyskania licencji

Zarówno castLabs, jak i AMS obsługują format tokenu JWT (JSON Web Token) używany do autoryzowania licencji. 

### <a name="jwt-token-in-ams"></a>Token JWT w ucho.

W poniższej tabeli opisano token JWT w udziale ams. 

| Wystawca | Ciąg wystawcy z wybranej usługi bezpiecznego tokenu (STS) |
| --- | --- |
| Grupy odbiorców |Ciąg odbiorców z używanego STS |
| Oświadczenia |Zestaw roszczeń |
| Nie wcześniej niż |Rozpocznij ważność tokenu |
| Wygasa |Końcowa ważność tokenu |
| Signingcredentials |Klucz współużytkowany przez playready license server, castLabs License Server i STS może być kluczem symetrycznym lub asymetrycznym. |

### <a name="jwt-token-in-castlabs"></a>Token JWT w castLabs

W poniższej tabeli opisano token JWT w castLabs. 

| Nazwa | Opis |
| --- | --- |
| optData ( optData ) |Ciąg JSON zawierający informacje o tobie. |
| Crt |Ciąg JSON zawierający informacje o zasobie, jego informacje o licencji i prawa do odtwarzania. |
| Iat |Bieżący datetime w epoce. |
| jti |Unikatowy identyfikator dotyczący tego tokenu (każdy token może być użyty tylko raz w systemie castLabs). |

## <a name="sample-solution-setup"></a>Przykładowa konfiguracja rozwiązania

Przykładowe [rozwiązanie](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) składa się z dwóch projektów:

* Aplikacja konsoli, która może służyć do ustawiania ograniczeń DRM dla już spożyconego zasobu, zarówno playready, jak i widevine.
* Aplikacja sieci Web, która rozdaje tokeny, które mogą być postrzegane jako bardzo uproszczona wersja STS.

Aby korzystać z aplikacji konsoli:

1. Zmień app.config na ustawienia poświadczeń AMS, poświadczenia castLabs, konfigurację STS i klucz udostępniony.
2. Prześlij zasób do usługi AMS.
3. Pobierz UUID z przesłanego zasobu i zmień wiersz 32 w pliku Program.cs:
   
      var objIAsset = _context. Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault();
4. Użyj assetId do nadawania nazwy zasobu w systemie castLabs (wiersz 44 w pliku Program.cs).
   
   Należy ustawić AssetId dla **castLabs**; musi to być unikatowy ciąg alfanumeryczny.
5. Uruchom program.

Aby korzystać z aplikacji sieci Web (STS):

1. Zmień web.config na identyfikator sprzedawcy castlabs, konfigurację STS i klucz udostępniony.
2. Wdrażanie w witrynach sieci Web platformy Azure.
3. Przejdź do witryny.

## <a name="playing-back-a-video"></a>Odtwarzanie filmu

Aby odtworzyć wideo zaszyfrowane za pomocą wspólnego szyfrowania (PlayReady i/lub Widevine), można użyć [programu Azure Media Player](https://aka.ms/azuremediaplayer). Podczas uruchamiania aplikacji konsoli identyfikator klucza zawartości i adres URL manifestu są powtarzane.

1. Otwórz nową kartę i uruchom sts: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Przejdź do [programu Azure Media Player](https://aka.ms/azuremediaplayer).
3. Wklej adres URL przesyłania strumieniowego.
4. Kliknij pole wyboru **Opcje zaawansowane.**
5. W menu rozwijanym **Ochrona** wybierz PlayReady i/lub Widevine.
6. Wklej token, który otrzymałeś z sts w pola tekstowym tokenu. 
   
   Serwer licencji castLab nie potrzebuje prefiksu "Bearer=" przed tokenem. Więc proszę usunąć, że przed przesłaniem tokenu.
7. Zaktualizuj odtwarzacz.
8. Film powinien być odtwarzany.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

