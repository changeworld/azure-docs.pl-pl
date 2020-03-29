---
title: Obsługa licencji Usługi Media Services i Apple FairPlay — Platforma Azure | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono omówienie wymagań i konfiguracji licencji Apple FairPlay.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6d4b7ba842d08723b90a4f2491d9e79e68dd932e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733576"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Wymagania licencyjne i konfiguracja technologii FairPlay firmy Apple 

Usługa Azure Media Services umożliwia szyfrowanie zawartości HLS za pomocą **usługi Apple FairPlay** (AES-128 CBC). Usługa Media Services zapewnia również usługę dostarczania licencji FairPlay. Gdy gracz próbuje odtworzyć zawartość chronioną przez FairPlay, żądanie jest wysyłane do usługi dostarczania licencji w celu uzyskania licencji. Jeśli usługa licencji zatwierdzi żądanie, wystawia licencję, która jest wysyłana do klienta i jest używana do odszyfrowywania i odtwarzania określonej zawartości.

Usługi Media Services udostępnia również interfejsy API, których można używać do konfigurowania licencji FairPlay. W tym temacie omówiono wymagania dotyczące licencji FairPlay i pokazano, jak można skonfigurować licencję **FairPlay** przy użyciu interfejsów API usługi Media Services. 

## <a name="requirements"></a>Wymagania

Podczas szyfrowania zawartości HLS za pomocą usługi **Apple FairPlay** i dostarczania licencji FairPlay za pomocą usługi Media Services wymagane są następujące elementy:

* Zarejestruj się w [Programie Rozwoju Firmy Apple](https://developer.apple.com/).
* Firma Apple wymaga od właściciela zawartości uzyskania [pakietu wdrożeniowego](https://developer.apple.com/contact/fps/). Oświadcza, że moduł zabezpieczeń kluczy (KSM) został już zaimplementowany w u usług media services i że żądasz ostatecznego pakietu FPS. Istnieją instrukcje w ostatecznym pakiecie FPS do generowania certyfikacji i uzyskania klucza tajnego aplikacji (ASK). Ask służy do konfigurowania FairPlay.
* Po stronie dostarczania klucza/licencji usługi Media Services należy ustawić następujące czynności:

    * **Certyfikat aplikacji (AC)**: Jest to plik .pfx, który zawiera klucz prywatny. Tworzysz ten plik i szyfrujesz go hasłem. Plik .pfx powinien być w formacie Base64.

        W poniższych krokach opisano sposób generowania pliku certyfikatu pfx dla gry FairPlay:

        1. Zainstaluj OpenSSL https://slproweb.com/products/Win32OpenSSL.htmlz .

            Przejdź do folderu, w którym znajduje się certyfikat FairPlay i inne pliki dostarczane przez firmę Apple.
        2. W wierszu polecenia uruchom następujące polecenie. Spowoduje to przekonwertowanie pliku cer na plik pem.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. W wierszu polecenia uruchom następujące polecenie. Spowoduje to przekonwertowanie pliku pem na plik .pfx z kluczem prywatnym. Hasło do pliku .pfx jest następnie zadawane przez OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **Hasło certyfikatu aplikacji**: Hasło do tworzenia pliku .pfx.
    * **ASK:** Ten klucz jest odbierany podczas generowania certyfikacji przy użyciu portalu Apple Developer. Każdy zespół programistów otrzymuje unikatowy ASK. Zapisz kopię ask i przechowuj ją w bezpiecznym miejscu. Należy skonfigurować ASK jako FairPlayAsk z usługą Media Services.
    
* Po stronie klienta FPS muszą być ustawione następujące czynności:

  * **Certyfikat aplikacji (AC)**: Jest to plik .cer/.der, który zawiera klucz publiczny, którego system operacyjny używa do szyfrowania ładunku. Usługi media muszą o tym wiedzieć, ponieważ jest to wymagane przez odtwarzacza. Usługa dostarczania kluczy odszyfrowuje go przy użyciu odpowiedniego klucza prywatnego.

* Aby odtworzyć zaszyfrowany strumień FairPlay, najpierw uzyskaj prawdziwy ask, a następnie wygeneruj prawdziwy certyfikat. Proces ten tworzy wszystkie trzy części:

  * plik der
  * .pfx plik
  * hasło do .pfx

## <a name="fairplay-and-player-apps"></a>FairPlay i aplikacje dla graczy

Gdy zawartość jest szyfrowana za pomocą **apple FairPlay,** poszczególne próbki wideo i audio są szyfrowane przy użyciu trybu **CBC AES-128.** **Usługa FairPlay Streaming** (FPS) jest zintegrowana z systemami operacyjnymi urządzeń, z natywną obsługą na iOS i Apple TV. Safari w os X włącza FPS przy użyciu obsługi interfejsu szyfrowanych rozszerzeń multimediów (EME).

Program Azure Media Player obsługuje również odtwarzanie fairplay. Aby uzyskać więcej informacji, zobacz [dokumentację programu Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html).

Możesz tworzyć własne aplikacje dla graczy za pomocą sdk iOS. Aby móc odtwarzać zawartość FairPlay, musisz zaimplementować protokół wymiany licencji. Ten protokół nie jest określony przez apple. To do każdej aplikacji, jak wysyłać żądania dostarczania kluczy. Usługa dostarczania kluczy Programu Media Services FairPlay oczekuje, że SPC pojawi się jako wiadomość post zakodowana w formularzu www-form-url w następującej formie:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Przykład konfiguracji FairPlay .NET

Za pomocą interfejsu API usługi Media Services można skonfigurować licencje FairPlay. Gdy gracz próbuje odtworzyć zawartość chronioną przez FairPlay, żądanie jest wysyłane do usługi dostarczania licencji w celu uzyskania licencji. Jeśli usługa licencji zatwierdzi żądanie, usługa wystawia licencję. Jest wysyłany do klienta i służy do odszyfrowywania i odtwarzania określonej zawartości.

> [!NOTE]
> Zazwyczaj chcesz skonfigurować opcje zasad FairPlay tylko raz, ponieważ będziesz mieć tylko jeden zestaw certyfikatów i ASK.

W poniższym przykładzie użyto [narzędzia Media Services .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) do skonfigurowania licencji.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Następne kroki

Sprawdź, jak [chronić za pomocą DRM](protect-with-drm.md)
