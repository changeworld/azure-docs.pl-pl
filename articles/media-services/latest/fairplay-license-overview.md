---
title: Usługa Media Services i technologii FairPlay firmy Apple licencji pomoc techniczna — Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie licencji technologii FairPlay firmy Apple, wymagań i konfiguracji.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733576"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Wymagania licencyjne technologii FairPlay firmy Apple i Konfiguracja 

Usługa Azure Media Services umożliwia szyfrowanie zawartości przy użyciu protokołu HLS **technologii FairPlay firmy Apple** (CBC AES-128). Media Services udostępnia również usługę dostarczania licencje usługi fairplay:. Gdy gracz próbuje odtwarzanie zawartości chronionej na technologii FairPlay, żądanie jest wysyłane do usługi dostarczania licencji uzyskanie licencji. Jeśli usługa licencji zatwierdza żądanie, problemy z licencji, która jest wysyłana do klienta i jest używany do odszyfrowania i odtwarzania określonej zawartości.

Media Services dostarcza również interfejsy API, które można użyć, aby skonfigurować licencje FairPlay. W tym temacie omówiono wymagania licencyjne FairPlay i pokazuje, jak konfigurować **FairPlay** Uzyskaj licencję za pomocą interfejsów API usług Media Services. 

## <a name="requirements"></a>Wymagania

Szyfrowanie zawartości przy użyciu protokołu HLS przy użyciu usługi Media Services są wymagane następujące **technologii FairPlay firmy Apple** a dostarczać licencje usługi fairplay: za pomocą usługi Media Services:

* Zarejestruj się przy użyciu [programem tworzącym Apple](https://developer.apple.com/).
* Firma Apple wymaga właścicielem zawartości w celu uzyskania [pakietu wdrożeniowego](https://developer.apple.com/contact/fps/). Stan już zaimplementowany klucz zabezpieczeń modułu (KSM) za pomocą usługi Media Services i żądają pakiecie końcowym kl. / s. Istnieją instrukcje w pakiecie końcowym kl. / s, aby wygenerować certyfikacji i Uzyskaj klucz klucz tajny aplikacji (AKS). ZADAJ umożliwiają konfigurowanie technologii FairPlay.
* Stronie dostarczania kluczy/licencji Media Services, musi być ustawione następujące elementy:

    * **Certyfikat aplikacji (AC)**: Jest to plik PFX, który zawiera klucz prywatny. Utwórz ten plik i szyfrować je przy użyciu hasła. Plik PFX powinien być w formacie Base64.

        W poniższych krokach opisano sposób generowania plik certyfikatu PFX dla technologii FairPlay:

        1. Zainstalować protokół OpenSSL z https://slproweb.com/products/Win32OpenSSL.html.

            Przejdź do folderu, w których certyfikat FairPlay i inne pliki dostarczane przez firmę Apple.
        2. W wierszu polecenia uruchom następujące polecenie. Spowoduje to konwersję plik cer do pliku PEM.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 — informuje der — w FairPlay.cer-out out.pem technologii FairPlay
        3. W wierszu polecenia uruchom następujące polecenie. Spowoduje to konwersję plik PEM do pliku .pfx, przy użyciu klucza prywatnego. Hasło dla pliku PFX jest następnie proszeni przez OpenSSL.

            Pkcs12 "C:\OpenSSL-Win32\bin\openssl.exe" — Eksportowanie — limit FairPlay out.pfx-inkey privatekey.pem — w file:privatekey-pem-pass.txt - passin out.pem technologii FairPlay
            
    * **Hasło certyfikatu aplikacji**: Hasło do tworzenia pliku .pfx.
    * **POPROŚ**: Ten klucz jest zgłaszany, jeśli Generowanie certyfikacji przy użyciu portalu dla deweloperów firmy Apple. Każdy zespół programistyczny otrzymuje unikatowy pytanie. Zapisz kopię ZADAJ i zapisz go w bezpiecznym miejscu. Należy skonfigurować poproś jako FairPlayAsk za pomocą usługi Media Services.
    
* Po stronie klienta kl. / s, należy ustawić następujące elementy:

  * **Certyfikat aplikacji (AC)**: Jest to plik.cer/.der, który zawiera klucz publiczny, który system operacyjny używa szyfrowania niektóre ładunku. Usługa Media Services musi znać na jego temat, ponieważ jest to wymagane przez gracza. Usługa dostarczania kluczy odszyfrowuje ją przy użyciu odpowiedniego klucza prywatnego.

* Aby odtworzyć strumień zaszyfrowanej technologia FairPlay, Pobierz rzeczywistych ZADAJĄ pierwszy, a następnie wygeneruj prawdziwym certyfikatem. Ten proces tworzy wszystkie trzy elementy:

  * pliku der
  * plik PFX
  * hasło PFX

## <a name="fairplay-and-player-apps"></a>Technologia FairPlay, jak i odtwarzacz aplikacji

Gdy zawartość jest szyfrowana za pomocą **technologii FairPlay firmy Apple**, poszczególne próbki audio i wideo są szyfrowane przy użyciu **CBC AES-128** trybu. **FairPlay Streaming** (kl. / s) jest zintegrowana z systemów operacyjnych urządzeń z natywną obsługą w systemach iOS i Apple TV. Safari w systemie OS X umożliwia kl. / s za pomocą obsługi interfejsu Encrypted Media Extensions (EME).

Usługa Azure Media Player obsługuje również FairPlay odtwarzania. Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html).

Możesz tworzyć własne aplikacje player przy użyciu zestawu SDK dla systemu iOS. Aby można było do odtwarzania zawartości z użyciem technologii FairPlay, musisz zaimplementować protokół wymiany licencji. Ten protokół nie jest określony przez firmę Apple. Jest każda aplikacja sposób wysyłania żądań dostarczania kluczy. Usługa dostarczania kluczy Media Services FairPlay oczekuje SPC na uszkodzonym www-form-url wpis zakodowany komunikat w następującej postaci:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Przykład konfiguracji technologii FairPlay dla środowiska .NET

Aby skonfigurować licencje usługi fairplay:, można użyć interfejsu API usługi Media Services. Próbą odtwarzanie zawartości chronionej na technologii FairPlay, żądania są wysyłane do usługi dostarczania licencji, aby uzyskać licencję. Jeśli usługa licencji zatwierdza żądanie, problemy z usługą licencji. Ona są wysyłane do klienta i jest używany do odszyfrowania i odtwarzania określonej zawartości.

> [!NOTE]
> Zazwyczaj należy skonfigurować opcje zasad FairPlay tylko raz, ponieważ masz tylko jeden zestaw certyfikacji i poproś.

W poniższym przykładzie użyto [Media Services .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) skonfigurować licencji.

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

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z jak [chronić za pomocą technologii DRM](protect-with-drm.md)
