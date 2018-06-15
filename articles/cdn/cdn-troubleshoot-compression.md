---
title: Rozwiązywanie problemów z kompresji plików w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z kompresji plików Azure CDN.
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
ms.openlocfilehash: 14d50cb7cac77af75dd4b7293812154d1f24e47c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33765528"
---
# <a name="troubleshooting-cdn-file-compression"></a>Rozwiązywanie problemów związanych z kompresją pliku CDN
Ten artykuł ułatwia rozwiązywanie problemów z [kompresji plików CDN](cdn-improve-performance.md).

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [MSDN Azure i fora przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i kliknij przycisk **Get Support**.

## <a name="symptom"></a>Objaw
Kompresja dla punktu końcowego jest włączona, ale pliki są zwracane nieskompresowane.

> [!TIP]
> Aby sprawdzić, czy pliki zostały zwrócone skompresowany, należy użyć narzędzia, takiego jak [Fiddler](http://www.telerik.com/fiddler) lub przeglądarki [narzędzia dla deweloperów](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Nagłówki odpowiedzi HTTP wyboru zwrócone z sieci CDN pamięci podręcznej zawartości.  Jeśli istnieje nagłówek o nazwie `Content-Encoding` o wartości **gzip**, **bzip2**, lub **deflate**, jest skompresowana zawartość.
> 
> ![Kodowanie zawartości nagłówka](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Przyczyna
Istnieje kilka możliwych przyczyn, w tym:

* Żądana zawartość nie jest uprawniony do kompresji.
* Kompresja nie jest włączona na żądany typ pliku.
* Żądanie HTTP nie zawiera nagłówka zażądał typu prawidłowy kompresji.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
> [!TIP]
> Podobnie jak w przypadku wdrażania nowych punktów końcowych, zmiany w konfiguracji sieci CDN zająć trochę czasu propagację za pośrednictwem sieci.  Zwykle zmiany zostaną zastosowane w ciągu 90 minut.  Jeśli jest to po raz pierwszy po skonfigurowaniu kompresji dla punktu końcowego CDN, należy rozważyć oczekiwania 1 – 2 godz. Aby upewnić się, że ustawienia zostaną rozpropagowane do lokalizacji POP kompresji. 
> 
> 

### <a name="verify-the-request"></a>Sprawdź żądania
Najpierw należy wykonać związane z poprawnością szybkie sprawdzenie żądanie.  Można użyć przeglądarki [narzędzia dla deweloperów](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) Przeglądanie żądań wysyłanych.

* Sprawdź, żądanie jest wysyłane na adres URL punktu końcowego `<endpointname>.azureedge.net`, a nie źródła.
* Sprawdź żądanie zawiera **Accept-Encoding** zawiera nagłówek i wartość tego nagłówka **gzip**, **deflate**, lub **bzip2**.

> [!NOTE]
> **Azure CDN from Akamai** profile obsługują tylko **gzip** kodowania.
> 
> 

![Nagłówki żądania CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Sprawdź ustawienia kompresji (standardowych profilów CDN)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy profilu CDN **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Verizon**, lub **Azure CDN Standard from Akamai** profilu. 
> 
> 

Przejdź do punktu końcowego w [portalu Azure](https://portal.azure.com) i kliknij przycisk **Konfiguruj** przycisku.

* Sprawdź, czy kompresja jest włączona.
* Sprawdź, czy typ MIME dla zawartości do skompresowania jest uwzględniony na liście formatów skompresowane.

![Ustawienia kompresji CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Sprawdź ustawienia kompresji (profile sieci CDN w warstwie Premium)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy profilu CDN **Azure CDN Premium from Verizon** profilu.
> 
> 

Przejdź do punktu końcowego w [portalu Azure](https://portal.azure.com) i kliknij przycisk **Zarządzaj** przycisku.  Zostanie otwarty w portalu dodatkowym.  Umieść kursor nad **HTTP dużych** , a następnie umieść kursor nad **ustawienia pamięci podręcznej** wysuwane okno.  Kliknij przycisk **kompresji**. 

* Sprawdź, czy kompresja jest włączona.
* Sprawdź **typów plików** lista zawiera listę rozdzielanych przecinkami (bez spacji) typów MIME.
* Sprawdź, czy typ MIME dla zawartości do skompresowania jest uwzględniony na liście formatów skompresowane.

![Ustawienia kompresji sieci CDN w warstwie premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Sprawdź, czy zawartość jest buforowany (Verizon CDN profilów)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy profilu CDN **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profilu.
> 
> 

Przy użyciu narzędzia dla deweloperów w przeglądarce, sprawdź nagłówków odpowiedzi, aby upewnić się, że plik jest buforowany w regionie, w którym jest on wymagany.

* Sprawdź **serwera** nagłówka odpowiedzi.  Nagłówek powinna mieć format **platformy (serwer protokołu POP/ID)**, jak pokazano w poniższym przykładzie.
* Sprawdź **pamięci podręcznej X** nagłówka odpowiedzi.  Nagłówek należy przeczytać **TRAFIEŃ**.  

![Nagłówki odpowiedzi CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Sprawdź, czy plik spełnia wymagania rozmiaru (profilów Verizon CDN)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy profilu CDN **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profilu.
> 
> 

Aby kwalifikować się do kompresji, plik musi spełniać następujące wymagania rozmiar:

* Większe niż 128 bajtów.
* Mniejszy niż 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Sprawdzanie żądania na serwerze źródłowym dla **za pośrednictwem** nagłówka
**Za pośrednictwem** nagłówka HTTP wskazuje na serwerze sieci web, czy żądanie jest przekazywany przez serwer proxy.  Serwery sieci web Microsoft IIS domyślnie nie Kompresuj odpowiedzi, jeśli żądanie zawiera **za pośrednictwem** nagłówka.  Aby zmienić to zachowanie, należy wykonać następujące czynności:

* **IIS 6**: [ustawić HcNoCompressionForProxies = "FALSE" we właściwościach metabazy usług IIS](https://msdn.microsoft.com/library/ms525390.aspx)
* **Usługi IIS 7 i nowsze**: [wartość **noCompressionForHttp10** i **noCompressionForProxies** na wartość False w konfiguracji serwera](http://www.iis.net/configreference/system.webserver/httpcompression)

