---
title: Skonfiguruj zasady ochrony zawartości przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak skonfigurować zasady ochrony zawartości przy użyciu portalu Azure. Artykuł opisuje również sposób włączania szyfrowania dynamicznego trwałych.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 8603716d30e1061ca9d600f2c053e90ff50c2433
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790384"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Skonfiguruj zasady ochrony zawartości przy użyciu portalu Azure
 Z usługi Azure Media Services można zabezpieczyć nośnik od momentu, gdy opuszczą komputera za pośrednictwem przechowywania, przetwarzania i dostarczania. Usługi Media Services umożliwia dostarczanie zawartości dynamicznie zaszyfrowany z Standard AES (Advanced Encryption) przy użyciu kluczy szyfrowania 128-bitowego. Możesz również służy z szyfrowania common encryption (CENC) za pomocą PlayReady i Widevine zarządzania prawami cyfrowymi (DRM) i FairPlay firmy Apple. 

Usługa Media Services udostępnia usługę dostarczania licencji DRM i AES wyczyść klucze do autoryzowanych klientów. Azure portal umożliwia utworzenie jedne zasady autoryzacji klucza/licencji dla wszystkich typów szyfrowania.

W tym artykule pokazano, jak skonfigurować zasady ochrony zawartości przy użyciu portalu. Artykuł opisuje również sposób stosowania szyfrowania dynamicznego do zasobów.

## <a name="start-to-configure-content-protection"></a>Uruchom, aby skonfigurować ochronę zawartości
Aby skonfigurować globalne ochrony zawartości przy użyciu konta usługi Media Services za pomocą portalu, należy wykonać następujące czynności:

1. W [portal](https://portal.azure.com/), wybierz konto usługi Media Services.

2. Wybierz **ustawienia** > **zawartości ochrony**.

    ![Ochrona zawartości](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Zasady autoryzacji kluczy/licencji
Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania licencji lub klucza. Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient następnie musi spełniać zasady przed klucz/licencji mogą być dostarczane do niego. Zasady autoryzacji klucza zawartości mogą mieć jedno lub więcej ograniczeń: ograniczenia otwarte lub ograniczenia tokenu.

Portal umożliwia utworzenie jedne zasady autoryzacji klucza/licencji dla wszystkich typów szyfrowania.

### <a name="open-authorization"></a>Otwórz autoryzacji
Otwórz ograniczeń oznacza, że system będzie klucza dla każdego, kto wysyła żądanie klucza. To ograniczenie mogą być przydatne do celów testowych. 

### <a name="token-authorization"></a>Token autoryzacji
Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje tokenów w formatach tokenu Web JSON (JWT) i simple web token (SWT). Usługa Media Services nie dostarcza usługi tokenu Zabezpieczającego. Można utworzyć niestandardowe usługi STS lub użyj usługi kontroli dostępu platformy Azure do wydawania tokenów. Usługa tokenu Zabezpieczającego musi być skonfigurowana do utworzenia tokenu podpisany z określonego klucza i problem oświadczenia określony w konfiguracji ograniczenia tokenu. Jeśli token jest prawidłowy i oświadczenia w tokenie zgodne z tymi skonfigurowana dla klucza (lub licencji), usługa Media Services klucza dostawy zwraca żądanego (licencji lub klucza) do klienta.

Po skonfigurowaniu zasad ograniczonej token, należy określić klucz podstawowy weryfikacji, wystawcy i parametry odbiorców. Klucz podstawowy weryfikacji zawiera klucz, który został podpisany token, z. Wystawca jest bezpieczne usługi tokenu, który wystawia token. Odbiorców (nazywane również zakres) opisano celem tokenu lub zasobu tokenu zezwala na dostęp do. Usługa Media Services klucza dostawy weryfikuje, czy te wartości w tokenie pasują do wartości w szablonie.

![Zasady autoryzacji kluczy/licencji](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Szablon licencji PlayReady
Licencja PlayReady szablon licencji PlayReady Ustawia funkcje, które jest włączona. Aby uzyskać więcej informacji o szablonie licencji PlayReady, zobacz [omówienie szablon licencji PlayReady usług Media](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Nietrwałych
Jeśli skonfigurujesz licencji jako nietrwałych tylko wtedy, gdy odtwarzacz korzysta z nim licencja jest przechowywane w pamięci.  

![Nietrwałych ochrony zawartości](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Trwała
Jeśli skonfigurujesz licencji jako trwałe jest zapisywany w magazynu trwałego na komputerze klienckim.

![Stałej ochrony zawartości](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Szablon licencji Widevine
Licencje Widevine szablonu licencji Widevine Ustawia funkcje, które jest włączona.

### <a name="basic"></a>Podstawowa
Po wybraniu **podstawowe**, szablon jest utworzony wartościami domyślnymi.

### <a name="advanced"></a>Advanced
Aby uzyskać więcej informacji o szablonie prawa Widevine, zobacz [omówienie szablonu licencji Widevine](media-services-widevine-license-template-overview.md).

![Zaawansowane ochrony zawartości](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Konfiguracja technologii FairPlay
Aby włączyć szyfrowanie FairPlay, wybierz **FairPlay konfiguracji**. Następnie wybierz **certyfikatu aplikacji** , a następnie wprowadź **klucz tajny aplikacji**. Aby uzyskać więcej informacji o konfiguracji FairPlay i wymagania, zobacz [chronić Twoje HLS zawartości z FairPlay firmy Apple lub Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![Konfiguracja technologii FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Zastosowanie szyfrowania dynamicznego do zawartości
Aby móc korzystać z szyfrowania dynamicznego, kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Wybierz zasób, który chcesz zaszyfrować
Aby wyświetlić wszystkich zasobów, wybierz **ustawienia** > **zasoby**.

![Opcja zasoby](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Szyfrowanie AES lub DRM
Po wybraniu **Szyfruj** dla zasobu, zobacz dwie opcje: **AES** lub **DRM**. 

#### <a name="aes"></a>AES
Wyczyść klucza szyfrowania jest włączona na wszystkich protokołów transmisji strumieniowej AES: Smooth Streaming, HLS i MPEG-DASH.

![Konfiguracja szyfrowania](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Po wybraniu **DRM**, zobacz różnych zawartości zasad ochrony (które musi być skonfigurowany w tym punkcie) oraz zestaw funkcji przesyłania strumieniowego protokołów:

    a. **PlayReady i Widevine z MPEG-DASH** dynamicznie szyfruje strumienia MPEG-DASH PlayReady i Widevine DRMs.

    b. **PlayReady i Widevine z MPEG-DASH + FairPlay z HLS** dynamicznego szyfrowania strumienia MPEG-DASH PlayReady i Widevine DRMs. Ta opcja szyfruje również strumienie HLS z FairPlay.

    c. **PlayReady tylko w przypadku funkcji Smooth Streaming, HLS i MPEG-DASH** dynamicznie szyfruje Smooth Streaming, HLS i MPEG-DASH strumieni z PlayReady DRM.

    d. **Widevine tylko z MPEG-DASH** dynamicznie szyfruje Twojej MPEG-DASH z Widevine DRM.
    
    e. **FairPlay tylko z HLS** dynamicznie szyfruje strumienia HLS z FairPlay.

2. Włącz szyfrowanie FairPlay **ustawienia globalne ochrony zawartości** bloku, wybierz opcję **FairPlay konfiguracji**. Następnie wybierz **certyfikatu aplikacji**, a następnie wprowadź **klucz tajny aplikacji**.

    ![Typ szyfrowania](./media/media-services-portal-content-protection/media-services-content-protection009.png)

3. Po dokonaniu wyboru szyfrowania, wybierz **Zastosuj**.

>[!NOTE] 
>Jeśli planujesz odtwarzania HLS zaszyfrowanych AES w programie Safari, zobacz wpis w blogu [HLS zaszyfrowanych w programie Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

