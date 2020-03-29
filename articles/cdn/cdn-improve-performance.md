---
title: Zwiększ wydajność, kompresując pliki w usłudze Azure CDN | Dokumenty firmy Microsoft
description: Dowiedz się, jak zwiększyć szybkość transferu plików i zwiększyć wydajność ładowania strony, kompresując pliki w usłudze Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: magattus
ms.openlocfilehash: 9f185f58e1d33a3985777cb22bc7578f9f2c4541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593799"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Poprawianie wydajności poprzez kompresowanie plików w usłudze Azure CDN
Kompresja plików jest prostą i skuteczną metodą poprawy szybkości transferu plików i zwiększenia wydajności ładowania strony poprzez zmniejszenie rozmiaru pliku przed wysłaniem go z serwera. Kompresja plików może zmniejszyć koszty przepustowości i zapewnić użytkownikom bardziej responsywne środowisko.

Kompresja plików można włączyć na dwa sposoby:

- Włącz kompresję na serwerze pochodzenia. W takim przypadku usługa Azure CDN przekazuje wzdłuż skompresowanych plików i dostarcza je do klientów, którzy ich żądają.
- Włącz kompresję bezpośrednio na serwerach CDN POP *(kompresja w locie).* W takim przypadku sieć CDN kompresuje pliki i obsługuje je użytkownikom końcowym, nawet jeśli nie zostały skompresowane przez serwer pochodzenia.

> [!IMPORTANT]
> Zmiany konfiguracji usługi Azure CDN może zająć trochę czasu, aby propagować za pośrednictwem sieci: 
> - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut. 
> 
> Jeśli konfigurujesz kompresję po raz pierwszy dla punktu końcowego sieci CDN, należy rozważyć odczekanie 1-2 godzin przed rozwiązywaniem problemów, aby upewnić się, że ustawienia kompresji zostały propagowane do punktu punktu punktu docelowego.

## <a name="enabling-compression"></a>Włączanie kompresji
Warstwy sieci CDN w warstwie standard i premium zapewniają te same funkcje kompresji, ale interfejs użytkownika różni się. Aby uzyskać więcej informacji na temat różnic między standardowymi i premium warstwami sieci CDN, zobacz [Omówienie usługi Azure CDN](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Standardowe profile CDN 
> [!NOTE]
> Ta sekcja dotyczy **usługi Azure CDN Standard firmy Microsoft**, Azure **CDN Standard firmy Verizon**i azure **CDN Standard z profilów Akamai.**
> 
> 

1. Na stronie profilu sieci CDN wybierz punkt końcowy sieci CDN, który chcesz zarządzać.

    ![Punkty końcowe profilu SIECI CDN](./media/cdn-file-compression/cdn-endpoints.png)

    Zostanie otwarta strona punktu końcowego sieci CDN.
2. Wybierz **opcję Kompresja**.

    ![Wybór kompresji CDN](./media/cdn-file-compression/cdn-compress-select-std.png)

    Zostanie otwarta strona kompresji.
3. Wybierz **włącz,** aby włączyć kompresję.

    ![Opcje kompresji plików CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Użyj domyślnych typów MIME lub zmodyfikuj listę, dodając lub usuwając typy MIME.

   > [!TIP]
   > Chociaż jest to możliwe, nie zaleca się stosowania kompresji do formatów skompresowanych. Na przykład ZIP, MP3, MP4 lub JPG.
   > 

   > [!NOTE]
   > Modyfikowanie domyślnej listy typów MIME nie jest obecnie obsługiwane w usłudze Azure CDN Standard firmy Microsoft.
   > 

5. Po wszuszeniu wybierz pozycję **Zapisz**.

### <a name="premium-cdn-profiles"></a>Profile premium CDN
> [!NOTE]
> Ta sekcja dotyczy tylko **usługi Azure CDN Premium z** profili Verizon.
> 

1. Na stronie profilu sieci CDN wybierz pozycję **Zarządzaj**.

    ![CDN Zarządzaj wybierz](./media/cdn-file-compression/cdn-manage-btn.png)

    Zostanie otwarty portal zarządzania siecią CDN.
2. Umieść wskaźnik myszy na karcie **Duży http,** a następnie umieść wskaźnik myszy na **wysu wysu wysu wysu wysu wysu wysu podczas gdy ustawienia pamięci podręcznej.** Wybierz **opcję Kompresja**.

    ![Wybór kompresji CDN](./media/cdn-file-compression/cdn-compress-select.png)

    Zostaną wyświetlone opcje kompresji.

    ![Opcje kompresji plików CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Włącz kompresję, wybierając opcję **Włączone kompresja**. Wprowadź typy MIME, które chcesz skompresować jako listę rozdzielanych przecinkami (bez spacji) w polu **Typy plików.**

   > [!TIP]
   > Chociaż jest to możliwe, nie zaleca się stosowania kompresji do formatów skompresowanych. Na przykład ZIP, MP3, MP4 lub JPG.
   > 

4. Po wszuszeniu zmiany wybierz pozycję **Aktualizuj**.

## <a name="compression-rules"></a>Reguły kompresji

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Standard usługi Azure CDN z profili firmy Microsoft

W przypadku **usługi Azure CDN Standard z** profilów firmy Microsoft tylko kwalifikujące się pliki są kompresowane. Aby kwalifikować się do kompresji, plik musi:
- Bądź typu MIME, który został [skonfigurowany do kompresji](#enabling-compression).
- Być większy niż 1 KB
- Być mniejsza niż 8 MB

Profile te obsługują następujące kodowania kompresji:
- gzip (GNU zip)
- brotli 

Jeśli żądanie obsługuje więcej niż jeden typ kompresji, kompresja brotli ma pierwszeństwo.

Gdy żądanie zasobu określa kompresję gzip, a żądanie powoduje pominięcie pamięci podręcznej, usługa Azure CDN wykonuje kompresję gzip zasobu bezpośrednio na serwerze POP. Następnie skompresowany plik jest obsługiwany z pamięci podręcznej.

### <a name="azure-cdn-from-verizon-profiles"></a>Usługa Azure CDN z profili Verizon

W przypadku **usługi Azure CDN Standard firmy Verizon** i azure **CDN Premium z** profili Verizon skompresowane są tylko kwalifikujące się pliki. Aby kwalifikować się do kompresji, plik musi:
- Być większy niż 128 bajtów
- Być mniejsza niż 3 MB

Profile te obsługują następujące kodowania kompresji:
- gzip (GNU zip)
- Deflate
- bzip2
- brotli 

Jeśli żądanie obsługuje więcej niż jeden typ kompresji, te typy kompresji mają pierwszeństwo przed kompresją brotli.

Gdy żądanie zasobu określa kompresji brotli `Accept-Encoding: br`(nagłówek HTTP jest) i żądanie powoduje pominięcie pamięci podręcznej, usługa Azure CDN wykonuje kompresji brotli zasobu bezpośrednio na serwerze POP. Następnie skompresowany plik jest obsługiwany z pamięci podręcznej.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Standard usługi Azure CDN z profili Akamai

W przypadku **usługi Azure CDN Standard z profilów Akamai** wszystkie pliki kwalifikują się do kompresji. Jednak plik musi być typu MIME, który został [skonfigurowany do kompresji](#enabling-compression).

Profile te obsługują tylko kodowanie kompresji gzip. Gdy punkt końcowy profilu żąda pliku zakodowanego w gzip, jest zawsze wymagany od źródła, niezależnie od żądania klienta. 

## <a name="compression-behavior-tables"></a>Tabele zachowania kompresji
W poniższych tabelach opisano zachowanie kompresji usługi Azure CDN dla każdego scenariusza:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Kompresja jest wyłączona lub plik nie kwalifikuje się do kompresji
| Format żądany przez klienta (za pośrednictwem nagłówka Akceptowanie kodowania) | Format pliku buforowanego | Odpowiedź sieci CDN na klienta | &nbsp; &nbsp; Uwagi&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Skompresowane |Skompresowane |Skompresowane | |
| Skompresowane |Nieskompresowane |Nieskompresowane | |
| Skompresowane |Nie buforowane |Skompresowane lub nieskompresowane |Odpowiedź pochodzenia określa, czy sieć CDN wykonuje kompresję. |
| Nieskompresowane |Skompresowane |Nieskompresowane | |
| Nieskompresowane |Nieskompresowane |Nieskompresowane | |
| Nieskompresowane |Nie buforowane |Nieskompresowane | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Kompresja jest włączona, a plik kwalifikuje się do kompresji
| Format żądany przez klienta (za pośrednictwem nagłówka Akceptowanie kodowania) | Format pliku buforowanego | Odpowiedź cdn na klienta | Uwagi |
| --- | --- | --- | --- |
| Skompresowane |Skompresowane |Skompresowane |Transkoduje sieć CDN między obsługiwanymi formatami. |
| Skompresowane |Nieskompresowane |Skompresowane |Cdn wykonuje kompresję. |
| Skompresowane |Nie buforowane |Skompresowane |Sieć CDN wykonuje kompresję, jeśli pochodzenie zwraca nieskompresowany plik. <br/>**Usługa Azure CDN firmy Verizon** przekazuje nieskompresowany plik na pierwsze żądanie, a następnie kompresuje i buforuje plik dla kolejnych żądań. <br/>Pliki z `Cache-Control: no-cache` nagłówkiem nigdy nie są kompresowane. |
| Nieskompresowane |Skompresowane |Nieskompresowane |Cdn wykonuje dekompresji. |
| Nieskompresowane |Nieskompresowane |Nieskompresowane | |
| Nieskompresowane |Nie buforowane |Nieskompresowane | |

## <a name="media-services-cdn-compression"></a>Kompresja cdn usług multimedialnych
W przypadku punktów końcowych włączonych dla usługi Media Services przesyłanie strumieniowe usługi CDN kompresja jest domyślnie włączona dla następujących typów MIME: 
- aplikacja/vnd.ms-sstr+xml 
- aplikacja/kreska+xml
- aplikacja/vnd.apple.mpegurl
- aplikacja/f4m+xml 

## <a name="see-also"></a>Zobacz też
* [Rozwiązywanie problemów związanych z kompresją pliku CDN](cdn-troubleshoot-compression.md)    

