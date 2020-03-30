---
title: Rozwiązywanie problemów z kompresją plików w usłudze Azure CDN | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z kompresją plików usługi Azure CDN.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: aff2dadee365fcdc7e14070714aa1d2cbba901ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476427"
---
# <a name="troubleshooting-cdn-file-compression"></a>Rozwiązywanie problemów związanych z kompresją pliku CDN
Ten artykuł ułatwia rozwiązywanie problemów z [kompresją plików CDN](cdn-improve-performance.md).

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [platformie MSDN Azure i forach przepełnienia stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i kliknij pozycję Uzyskaj pomoc **techniczną**.

## <a name="symptom"></a>Objaw
Kompresja dla punktu końcowego jest włączona, ale pliki są zwracane bez kompresji.

> [!TIP]
> Aby sprawdzić, czy pliki są zwracane skompresowane, należy użyć narzędzia, takiego jak [Fiddler](https://www.telerik.com/fiddler) lub [narzędzi programistycznych](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)przeglądarki.  Sprawdź nagłówki odpowiedzi HTTP zwrócone z buforowaną zawartością sieci CDN.  Jeśli istnieje nagłówek `Content-Encoding` o nazwie o wartości **gzip**, **bzip2**lub **deflate**, zawartość jest skompresowana.
> 
> ![Nagłówek kodowania zawartości](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Przyczyna
Istnieje kilka możliwych przyczyn, w tym:

* Żądana zawartość nie kwalifikuje się do kompresji.
* Kompresja nie jest włączona dla żądanego typu pliku.
* Żądanie HTTP nie zawierało nagłówka żądającego prawidłowego typu kompresji.
* Origin wysyła zawartość w posyłkioną.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
> [!TIP]
> Podobnie jak w przypadku wdrażania nowych punktów końcowych, zmiany konfiguracji usługi CDN zajmują trochę czasu, aby propagować za pośrednictwem sieci.  Zazwyczaj zmiany są stosowane w ciągu 90 minut.  If this is the first time you've set up compression for your CDN endpoint, you should consider waiting 1-2 hours to be sure the compression settings have propagated to the POPs. 
> 
> 

### <a name="verify-the-request"></a>Sprawdź żądanie
Po pierwsze, powinniśmy zrobić szybkie sprawdzanie poczytalności na żądanie.  Możesz użyć [narzędzi programistycznych](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) przeglądarki, aby wyświetlić składane żądania.

* Sprawdź, czy żądanie jest wysyłane do `<endpointname>.azureedge.net`adresu URL punktu końcowego, a nie do źródła.
* Sprawdź, czy żądanie zawiera nagłówek **Akceptowania kodowania,** a wartość tego nagłówka zawiera **gzip**, **deflate**lub **bzip2**.

> [!NOTE]
> **Usługa Azure CDN z profili Akamai** obsługuje tylko kodowanie **gzip.**
> 
> 

![Nagłówki żądań sieci CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Weryfikowanie ustawień kompresji (standardowe profile cdn)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy twój profil usługi CDN jest **standardem usługi Azure CDN firmy Microsoft,** **standardem usługi Azure CDN firmy Verizon**lub **standardem usługi Azure CDN standard z profilu Akamai.** 
> 
> 

Przejdź do punktu końcowego w [witrynie Azure portal](https://portal.azure.com) i kliknij przycisk **Konfiguruj.**

* Sprawdź, czy kompresja jest włączona.
* Sprawdź, czy typ MIME dla zawartości, która ma być skompresowana, znajduje się na liście skompresowanych formatów.

![Ustawienia kompresji CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Weryfikowanie ustawień kompresji (profile premium CDN)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy twój profil usługi CDN jest profilem **usługi Azure CDN Premium firmy Verizon.**
> 
> 

Przejdź do punktu końcowego w [witrynie Azure portal](https://portal.azure.com) i kliknij przycisk **Zarządzaj.**  Otworzy się dodatkowy portal.  Umieść wskaźnik myszy na karcie **Duży http,** a następnie umieść wskaźnik myszy na **wysu wysu wysu wysu wysu wysu wysu podczas gdy ustawienia pamięci podręcznej.**  Kliknij **przycisk Kompresja**. 

* Sprawdź, czy kompresja jest włączona.
* Sprawdź, czy lista **Typy plików** zawiera listę oddzieloną przecinkami (bez spacji) typów MIME.
* Sprawdź, czy typ MIME dla zawartości, która ma być skompresowana, znajduje się na liście skompresowanych formatów.

![Ustawienia kompresji premium CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Sprawdź, czy zawartość jest buforowana (profile Verizon CDN)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy twój profil usługi CDN jest **standardem usługi Azure CDN firmy Verizon** lub **Azure CDN Premium z** profilu Verizon.
> 
> 

Korzystając z narzędzi programistycznych przeglądarki, sprawdź nagłówki odpowiedzi, aby upewnić się, że plik jest buforowany w regionie, w którym jest wymagany.

* Sprawdź nagłówek odpowiedzi **serwera.**  Nagłówek powinien mieć format **Platformy (POP/Server ID),** jak pokazano w poniższym przykładzie.
* Sprawdź nagłówek odpowiedzi **X-Cache.**  Nagłówek powinien być odczytywany **z treścią HIT**.  

![Nagłówki odpowiedzi cdn](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Sprawdź, czy plik spełnia wymagania dotyczące rozmiaru (profile Verizon CDN)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy twój profil usługi CDN jest **standardem usługi Azure CDN firmy Verizon** lub **Azure CDN Premium z** profilu Verizon.
> 
> 

Aby kwalifikować się do kompresji, plik musi spełniać następujące wymagania dotyczące rozmiaru:

* Większe niż 128 bajtów.
* Mniejszy niż 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Sprawdź żądanie na serwerze pochodzenia dla nagłówka **Via**
Nagłówek **Via** HTTP wskazuje serwerowi sieci web, że żądanie jest przekazywane przez serwer proxy.  Serwery sieci Web usług IIS firmy Microsoft domyślnie nie kompresują odpowiedzi, gdy żądanie zawiera nagłówek **Via.**  Aby zastąpić to zachowanie, wykonaj następujące czynności:

* **IIS 6**: [Zestaw HcNoCompressionForProxies="FALSE" we właściwościach metabazy usług IIS](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **Usługi IIS 7 i nowe**: [Ustaw zarówno **noCompressionForHttp10,** jak i **noCompressionForProxies** na False w konfiguracji serwera](https://www.iis.net/configreference/system.webserver/httpcompression)

