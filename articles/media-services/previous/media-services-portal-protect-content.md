---
title: Konfigurowanie zasad ochrony zawartości przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób konfigurowania zasad ochrony zawartości przy użyciu witryny Azure portal. Tego artykułu przedstawiono również sposób włączenia szyfrowania dynamicznego zasobów.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c0571e202448951de0994d34f68e1649eabd5519
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61128139"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Konfigurowanie zasad ochrony zawartości przy użyciu witryny Azure portal
 Usługa Azure Media Services można zabezpieczyć multimediów od momentu wysłania komputera za pośrednictwem przechowywania, przetwarzania i dostarczania. Usługa Media Services umożliwia dostarczanie zawartości dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES) przy użyciu kluczy szyfrowania 128-bitowego. Ponadto można korzystania z szyfrowania common encryption (CENC) przy użyciu technologii PlayReady i Widevine zarządzania prawami cyfrowymi (DRM) i technologii FairPlay firmy Apple. 

Usługa Media Services udostępnia usługę dostarczania licencji DRM i AES wyczyść klucze do autoryzowanych klientów. Aby utworzyć zasady autoryzacji kluczy/licencji dla wszystkich typów szyfrowania, można użyć witryny Azure portal.

W tym artykule pokazano, jak skonfigurować zasady ochrony zawartości przy użyciu portalu. Tego artykułu przedstawiono również sposób zastosowanie szyfrowania dynamicznego Twoich zasobów.

## <a name="start-to-configure-content-protection"></a>Rozpocznij Konfigurowanie ochrony zawartości
Aby skonfigurować globalne ochrony zawartości przy użyciu konta usługi Media Services za pomocą portalu, wykonaj następujące czynności:

1. W [portal](https://portal.azure.com/), wybierz konto usługi Media Services.

1. Wybierz **ustawienia** > **Content protection**.

    ![Ochrona zawartości](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Zasady autoryzacji kluczy/licencji
Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza lub licencji. Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient następnie musi spełnić te zasady przed kluczy/licencji mogą być dostarczane do niego. Zasady autoryzacji klucza zawartości mogą mieć jedno lub więcej ograniczeń: ograniczenia otwarte lub ograniczenia tokenu.

Aby utworzyć zasady autoryzacji kluczy/licencji dla wszystkich typów szyfrowania, można użyć portalu.

### <a name="open-authorization"></a>Otwórz autoryzacji
Otwórz ograniczeń oznacza, że system zapewnia klucza dla każdego, kto wykonuje żądanie klucza. To ograniczenie może być przydatna do celów testowych. 

### <a name="token-authorization"></a>Token autoryzacji
Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje tokenów w formatach tokenu Web JSON (JWT) i prosty token sieci web (SWT). Usługa Media Services nie dostarcza usługi tokenu Zabezpieczającego. Można utworzyć niestandardowej usługi STS, lub użyć usługi Azure Access Control Service do wydawania tokenów. Usługa STS musi być skonfigurowany do utworzenia tokenu, który został podpisany przy użyciu określonego klucza i problem oświadczenia określona w konfiguracji ograniczenia tokenu. Jeśli token jest prawidłowy i oświadczenia w tokenie są zgodne z oświadczeniami skonfigurowanymi dla klucza (lub licencji), usługa dostarczania kluczy usługi Media Services zwraca żądany kluczy (lub licencji) do klienta.

Po skonfigurowaniu zasad ograniczony token, należy określić podstawowy klucz weryfikacji wystawcy i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, który token został podpisany za pomocą. Wystawca jest usługa bezpiecznych tokenów, który wystawia token. Grupy odbiorców (nazywane również zakres) opisuje przeznaczenie tokenu lub zasób tokenu autoryzuje dostępu do. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasuje do wartości w szablonie.

![Zasady autoryzacji kluczy/licencji](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Szablon licencji PlayReady
Szablon licencji PlayReady Ustawia funkcje, które włączono licencji technologii PlayReady. Aby uzyskać więcej informacji na temat szablonu licencji technologii PlayReady zobacz [omówienie szablonu licencji PlayReady usługi Media Services](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Nietrwałych
Jeśli skonfigurujesz licencję jako nietrwałych tylko wtedy, gdy gracz korzysta z licencji jest przechowywany w pamięci.  

![Ochrona zawartości nietrwałych](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Trwała
Jeśli skonfigurujesz licencję jako trwałe jest zapisywane w trwałym magazynie na komputerze klienckim.

![Stałej ochrony zawartości](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Szablon licencji Widevine
Szablon licencji Widevine Ustawia funkcje, które jest włączona z licencji Widevine.

### <a name="basic"></a>Podstawowa
Po wybraniu **podstawowe**, szablon został utworzony przy użyciu wszystkich wartości domyślnych.

### <a name="advanced"></a>Zaawansowane
Aby uzyskać więcej informacji na temat szablon praw technologii Widevine, zobacz [omówienie szablonu licencji Widevine](media-services-widevine-license-template-overview.md).

![Zaawansowana ochrona zawartości](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Konfiguracja technologii FairPlay
Aby włączyć szyfrowanie FairPlay, wybierz **konfiguracji technologii FairPlay**. Następnie wybierz pozycję **certyfikatu usługi App** i wprowadź **klucz tajny aplikacji**. Aby uzyskać więcej informacji na temat konfiguracji technologii FairPlay i wymagań, zobacz [ochrony zawartości przy użyciu technologii FairPlay firmy Apple lub Microsoft PlayReady przez moduł HLS](media-services-protect-hls-with-FairPlay.md).

![Konfiguracja technologii FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Zastosowanie szyfrowania dynamicznego do elementu zawartości
Aby móc skorzystać z szyfrowania dynamicznego, kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Wybierz zasób, który chcesz zaszyfrować
Aby wyświetlić wszystkie elementy zawartości, wybierz **ustawienia** > **zasoby**.

![Opcja zasobów](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Szyfrowanie przy użyciu technologii AES lub technologii DRM
Po wybraniu **Szyfruj** dla zasobu, zobacz jedną z dwóch opcji: **AES** lub **DRM**. 

#### <a name="aes"></a>AES
AES jest jasne, że szyfrowanie klucza jest włączone na wszystkich protokołów przesyłania strumieniowego: Smooth Streaming, HLS i MPEG-DASH.

![Konfiguracja szyfrowania](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Po wybraniu **DRM**, zobacz zasady ochrony zawartości w innej, (które musi być skonfigurowany na tym etapie) oraz zestaw protokołów przesyłania strumieniowego:

    a. **Technologie PlayReady i Widevine przy użyciu standardu MPEG-DASH** dynamicznie szyfruje strumienia MPEG-DASH za pomocą usług PlayReady i Widevine protokołów DRM.

    b. **Technologie PlayReady i Widevine przy użyciu standardu MPEG-DASH oraz fairplay z HLS** dynamicznie szyfrując strumienia MPEG-DASH za pomocą usług PlayReady i Widevine protokołów DRM. Ta opcja również szyfruje Twoje strumieni HLS z użyciem technologii FairPlay.

    c. **PlayReady tylko przy użyciu funkcji Smooth Streaming, HLS i MPEG-DASH** dynamicznie szyfruje Smooth Streaming, HLS i MPEG-DASH strumieni przy użyciu technologii PlayReady DRM.

    d. **Widevine tylko przy użyciu standardu MPEG-DASH** dynamicznie szyfruje Twoje MPEG-DASH przy użyciu usługi Widevine DRM.
    
    e. **Technologia FairPlay tylko przy użyciu protokołu HLS** dynamicznie szyfruje strumienia HLS z użyciem technologii FairPlay.

1. Aby włączyć szyfrowanie FairPlay w **Content Protection globalne ustawienia** bloku wybierz **konfiguracji technologii FairPlay**. Następnie wybierz pozycję **certyfikatu usługi App**, a następnie wprowadź **klucz tajny aplikacji**.

    ![Typ szyfrowania](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Po dokonaniu wyboru, szyfrowanie, wybierz **Zastosuj**.

>[!NOTE] 
>Jeśli planujesz odtwarzania HLS z szyfrowaniem AES w przeglądarce Safari, zobacz wpis w blogu [HLS zaszyfrowanych w przeglądarce Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

