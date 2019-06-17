---
title: Rozwiązywanie problemów z kompresją pliku w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z kompresją pliku Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 4df8e5d4560a813c47319833a8cd91726abcb8e6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60323813"
---
# <a name="troubleshooting-cdn-file-compression"></a>Rozwiązywanie problemów związanych z kompresją pliku CDN
Ten artykuł pomoże Ci rozwiązać problemy z [kompresją pliku CDN](cdn-improve-performance.md).

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i kliknij przycisk **Uzyskaj pomoc techniczną**.

## <a name="symptom"></a>Objaw
Kompresja dla punktu końcowego usługi jest włączona, ale pliki są zwracane bez kompresji.

> [!TIP]
> Aby sprawdzić, czy pliki zwracane są skompresowane, należy użyć narzędzia, takiego jak [Fiddler](https://www.telerik.com/fiddler) lub w przeglądarce [narzędzi deweloperskich](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Nagłówki odpowiedzi HTTP wyboru zwracane z sieci CDN pamięci podręcznej zawartości.  Jeśli istnieje nagłówek o nazwie `Content-Encoding` o wartości **gzip**, **bzip2**, lub **deflate**, zawartość jest skompresowany.
> 
> ![Nagłówek Content-Encoding](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Przyczyna
Istnieje kilka możliwych przyczyn, takich jak:

* Żądana zawartość nie kwalifikuje się do kompresji.
* Kompresja nie jest włączona dla typu żądanego pliku.
* Żądanie HTTP nie zawiera nagłówka żądania typu kompresji prawidłowe.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
> [!TIP]
> Podobnie jak w przypadku wdrażania nowych punktów końcowych, zmiany w konfiguracji sieci CDN zająć trochę czasu, do propagowania za pośrednictwem sieci.  Zwykle zmiany są stosowane w ciągu 90 minut.  Jeśli po raz pierwszy po skonfigurowaniu kompresji dla punktu końcowego usługi CDN, należy rozważyć, 1 – 2 godzin oczekiwania mieć pewność, że kompresji, którego ustawienia mają propagowane do lokalizacji POP. 
> 
> 

### <a name="verify-the-request"></a>Sprawdź żądania
Najpierw należy wykonać sprawdzenie szybkiego poprawnością na żądanie.  Możesz użyć swojej przeglądarki [narzędzi deweloperskich](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) do wyświetlania żądań.

* Sprawdź, żądanie jest wysyłane do adresu URL punktu końcowego, `<endpointname>.azureedge.net`, a nie źródło.
* Sprawdź żądanie zawiera **Accept-Encoding** zawiera nagłówek i wartość tego nagłówka **gzip**, **deflate**, lub **bzip2**.

> [!NOTE]
> **Usługa Azure CDN from Akamai** profile obsługują tylko **gzip** kodowania.
> 
> 

![Nagłówki żądania usługi CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Sprawdź ustawienia kompresji (standardowa profilów usługi CDN)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy Twój profil CDN jest **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Verizon**, lub **Azure CDN Standard from Akamai** profilu. 
> 
> 

Przejdź do punktu końcowego w [witryny Azure portal](https://portal.azure.com) i kliknij przycisk **Konfiguruj** przycisku.

* Sprawdź, czy kompresja jest włączona.
* Sprawdź, czy typ MIME zawartości do skompresowania znajduje się na liście skompresowanych formatów.

![Ustawienia kompresji usługi CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Sprawdź ustawienia kompresji (profilów usługi CDN w warstwie Premium)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy Twój profil CDN jest **Azure CDN Premium from Verizon** profilu.
> 
> 

Przejdź do punktu końcowego w [witryny Azure portal](https://portal.azure.com) i kliknij przycisk **Zarządzaj** przycisku.  Spowoduje to otwarcie portalu dodatkowego.  Umieść kursor nad **HTTP dużych** kartę, a następnie umieść kursor nad **ustawienia pamięci podręcznej** okno wysuwane.  Kliknij przycisk **kompresji**. 

* Sprawdź, czy kompresja jest włączona.
* Sprawdź **typów plików** lista zawiera listę rozdzielanych przecinkami (bez spacji) typów MIME.
* Sprawdź, czy typ MIME zawartości do skompresowania znajduje się na liście skompresowanych formatów.

![Ustawienia kompresji — wersja premium usługi CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Sprawdź, czy zawartość jest pamięci podręcznej (profilów usługi CDN firmy Verizon)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy Twój profil CDN jest **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profilu.
> 
> 

Za pomocą narzędzi dla deweloperów w przeglądarce, sprawdź nagłówki odpowiedzi, aby upewnić się, że plik jest buforowany w regionie, gdzie jest on wymagany.

* Sprawdź **serwera** nagłówka odpowiedzi.  Nagłówek powinien mieć format **platformy (serwer protokołu POP/ID)** , jak pokazano w poniższym przykładzie.
* Sprawdź **X-Cache** nagłówka odpowiedzi.  Nagłówek powinni przeczytać **TRAFIEŃ**.  

![Nagłówki odpowiedzi sieci CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Upewnij się, że plik spełnia wymagania dotyczące rozmiaru (profilów usługi CDN firmy Verizon)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy Twój profil CDN jest **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profilu.
> 
> 

Aby zakwalifikować się do kompresji, plik musi spełniać następujące wymagania rozmiar:

* Większe niż 128 bajtów.
* Mniejszy niż 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Sprawdzanie żądania na serwer pochodzenia **za pośrednictwem** nagłówka
**Za pośrednictwem** nagłówka HTTP wskazuje na serwerze sieci web, czy żądanie jest przekazywana przez serwer proxy.  Serwery sieci web Microsoft IIS, domyślnie nie Kompresuj odpowiedzi, gdy żądanie zawiera **za pośrednictwem** nagłówka.  Aby zmienić to zachowanie, wykonaj następujące czynności:

* **IIS 6**: [Ustaw HcNoCompressionForProxies = "FALSE" we właściwościach metabazy usług IIS](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **Usługi IIS 7 i nowsze**: [Ustaw obie **noCompressionForHttp10** i **noCompressionForProxies** na wartość False w konfiguracji serwera](https://www.iis.net/configreference/system.webserver/httpcompression)

