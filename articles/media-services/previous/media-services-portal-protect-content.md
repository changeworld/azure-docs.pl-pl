---
title: Konfigurowanie zasad ochrony zawartości przy użyciu witryny Azure Portal | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak korzystać z witryny Azure portal do konfigurowania zasad ochrony zawartości. W tym artykule pokazano również, jak włączyć szyfrowanie dynamiczne dla zasobów.
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
ms.openlocfilehash: 0c2a9612fab6c685cbc690aa9bbc12d1c7b7b746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978206"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Konfigurowanie zasad ochrony zawartości przy użyciu witryny Azure portal

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).   > Do usługi Media Services w wersji 2 nie są dodawane żadne nowe funkcje ani funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)
>

 Dzięki usłudze Azure Media Services możesz zabezpieczyć multimedia od momentu opuszczenia komputera przez magazyn, przetwarzanie i dostarczanie. Za pomocą usługi Media Services można dynamicznie dostarczać zawartość za pomocą zaawansowanego standardu szyfrowania (AES) przy użyciu 128-bitowych kluczy szyfrowania. Można go również używać z powszechnym szyfrowaniem (CENC) przy użyciu PlayReady i/lub Widevine digital rights management (DRM) i Apple FairPlay. 

Usługa Media Services zapewnia usługę dostarczania licencji DRM i jasnych kluczy AES autoryzowanym klientom. Za pomocą witryny Azure Portal można utworzyć jedną zasadę autoryzacji kluczy/licencji dla wszystkich typów szyfrowania.

W tym artykule pokazano, jak skonfigurować zasady ochrony zawartości przy użyciu portalu. W tym artykule pokazano również, jak zastosować szyfrowanie dynamiczne do zasobów.

## <a name="start-to-configure-content-protection"></a>Zacznij konfigurować ochronę zawartości
Aby skonfigurować globalną ochronę zawartości za pomocą konta usługi Media Services, należy wykonać następujące czynności:

1. W [portalu](https://portal.azure.com/)wybierz swoje konto usługi Media Services.

1. Wybierz **pozycję Ustawienia** > **ochrony zawartości**.

    ![Ochrona zawartości](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Zasady autoryzacji kluczy/licencji
Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy żądają klucza lub licencji. Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient musi następnie spełnić zasady, zanim klucz/licencja może zostać do niego dostarczony. Zasady autoryzacji klucza zawartości mogą mieć jedno lub więcej ograniczeń: ograniczenia otwarte lub ograniczenia tokenu.

Za pomocą portalu można utworzyć jedną zasadę autoryzacji kluczy/licencji dla wszystkich typów szyfrowania.

### <a name="open-authorization"></a>Otwarta autoryzacja
Otwarte ograniczenie oznacza, że system dostarcza klucz każdemu, kto zgłosi żądanie klucza. To ograniczenie może być przydatne do celów testowych. 

### <a name="token-authorization"></a>Autoryzacja tokenu
Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje następujące formaty tokenów: prosty token sieci Web (SWT) i token sieci Web JSON (JWT). Usługi Media Services nie zapewniają sts. Można utworzyć niestandardowy sts lub użyć usługi Azure Access Control Service do wystawiania tokenów. Sts musi być skonfigurowany do tworzenia tokenu podpisanego przy określonym kluczu i oświadczeń problem, które zostały określone w konfiguracji ograniczenia tokenu. Jeśli token jest prawidłowy, a oświadczenia w tokenie są zgodne z tymi skonfigurowanym dla klucza (lub licencji), usługa dostarczania kluczy usługi Media Services zwraca żądany klucz (lub licencję) do klienta.

Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacji, wystawcę i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, za pomocą którym token został podpisany. Wystawca jest usługą bezpiecznego tokenu, która wystawia token. Grupa odbiorców (czasami nazywana zakresem) opisuje intencję tokenu lub zasobu, do który token autoryzuje dostęp. Usługa dostarczania kluczy usługi Media Services sprawdza, czy te wartości w tokenie są zgodne z wartościami w szablonie.

![Zasady autoryzacji kluczy/licencji](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Szablon licencji PlayReady
Szablon licencji PlayReady ustawia funkcje, które są włączone na licencji PlayReady. Aby uzyskać więcej informacji na temat szablonu licencji PlayReady, zobacz [omówienie szablonu licencji Programu Media Services PlayReady](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Nietrętne
Jeśli licencja zostanie skonfigurowana jako nietrwencjonowa, jest utrzymywana w pamięci tylko wtedy, gdy gracz używa licencji.  

![Nietrwące zabezpieczenie zawartości](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Stale
Jeśli licencja zostanie skonfigurowana jako trwała, zostanie zapisana w magazynie trwałym na kliencie.

![Trwała ochrona zawartości](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Szablon licencji Widevine
Szablon licencji Widevine ustawia funkcje włączone na licencjach Widevine.

### <a name="basic"></a>Podstawowa (Basic)
Po wybraniu opcji **Podstawowe**szablon jest tworzony ze wszystkimi wartościami domyślnymi.

### <a name="advanced"></a>Zaawansowane
Aby uzyskać więcej informacji na temat szablonu praw Widevine, zobacz [omówienie szablonu licencji Widevine](media-services-widevine-license-template-overview.md).

![Zaawansowana ochrona zawartości](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Konfiguracja FairPlay
Aby włączyć szyfrowanie FairPlay, wybierz **opcję Konfiguracja FairPlay**. Następnie wybierz **certyfikat aplikacji** i wprowadź klucz tajny **aplikacji**. Aby uzyskać więcej informacji na temat konfiguracji i wymagań FairPlay, zobacz [Ochrona zawartości HLS za pomocą usługi Apple FairPlay lub Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![Konfiguracja FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Stosowanie szyfrowania dynamicznego do zasobu
Aby skorzystać z szyfrowania dynamicznego, zakoduj plik źródłowy w zestawie plików MP4 o szybkości adaptacyjnej szybkości transmisji bitów.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Wybierz zasób, który chcesz zaszyfrować
Aby wyświetlić wszystkie zasoby, wybierz **pozycję Zasoby ustawień** > **.**

![Opcja Zasoby](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Szyfruj za pomocą AES lub DRM
Po **wybraniu opcji Szyfruj** dla zasobu zobaczysz dwie opcje: **AES** lub **DRM**. 

#### <a name="aes"></a>AES
Szyfrowanie klucza Wyczyszczonego AES jest włączone we wszystkich protokołach przesyłania strumieniowego: Smooth Streaming, HLS i MPEG-DASH.

![Konfiguracja szyfrowania](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>Drm
1. Po wybraniu **drm**, zobaczysz różne zasady ochrony zawartości (które muszą być skonfigurowane przez ten punkt) i zestaw protokołów przesyłania strumieniowego:

    a. **PlayReady i Widevine z MPEG-DASH** dynamicznie szyfrują strumień MPEG-DASH za pomocą technologii DrM PlayReady i Widevine.

    b. **PlayReady i Widevine z MPEG-DASH + FairPlay z HLS** dynamicznie szyfrują strumień MPEG-DASH za pomocą technologii DrM PlayReady i Widevine. Ta opcja szyfruje również strumienie HLS za pomocą FairPlay.

    d. **PlayReady tylko z Smooth Streaming, HLS i MPEG-DASH** dynamicznie szyfruje smooth streaming, HLS i MPEG-DASH strumieni z PlayReady DRM.

    d. **Widevine tylko z MPEG-DASH** dynamicznie szyfruje MPEG-DASH z Widevine DRM.
    
    e. **FairPlay tylko z HLS** dynamicznie szyfruje strumień HLS z FairPlay.

1. Aby włączyć szyfrowanie FairPlay, w bloku **Ustawienia globalne ochrony zawartości** wybierz pozycję **Konfiguracja FairPlay**. Następnie wybierz **certyfikat aplikacji**i wprowadź klucz tajny **aplikacji**.

    ![Typ szyfrowania](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Po dokonaniu wyboru szyfrowania wybierz pozycję **Zastosuj**.

>[!NOTE] 
>Jeśli planujesz grać w hls szyfrowany AES w Safari, zobacz wpis na blogu [Zaszyfrowane HLS w Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

