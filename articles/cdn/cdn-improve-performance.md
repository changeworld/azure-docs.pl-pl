---
title: Poprawianie wydajności poprzez kompresowanie plików w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zwiększyć szybkość transferu plików i zwiększyć wydajność ładowania strony poprzez kompresowanie plików w usłudze Azure CDN.
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
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593799"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Poprawianie wydajności poprzez kompresowanie plików w usłudze Azure CDN
Kompresja plików jest prosta i skuteczna metoda zwiększanie szybkości transferu plików i poprawia wydajność ładowania strony, zmniejszając rozmiar tego pliku przed wysłaniem ich z serwera. Kompresja plików można obniżyć koszty przepustowości i zapewnić bardziej dynamiczne środowisko dla użytkowników.

Istnieją dwa sposoby, aby włączyć kompresję pliku:

- Włącz kompresję serwera pochodzenia. W tym przypadku usługa Azure CDN przekazywane wraz z pliki skompresowane i przekazuje je klientom żądającym je.
- Włącz kompresję bezpośrednio na serwerach POP sieci CDN (*kompresji w locie*). W tym przypadku usługa CDN kompresuje pliki i służy je do użytkowników końcowych, nawet jeśli nie zostały skompresowane przez serwer pochodzenia.

> [!IMPORTANT]
> Zmiany konfiguracji w usłudze Azure CDN może zająć trochę czasu, do propagowania za pośrednictwem sieci: 
> - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut. 
> 
> Jeśli podczas konfigurowania kompresji po raz pierwszy dla punktu końcowego usługi CDN, należy wziąć pod uwagę oczekiwania 1 – 2 godzin przed zajmujesz upewnij się, że ustawienia kompresji wykonaniu propagacji do lokalizacji POP.

## <a name="enabling-compression"></a>Włączanie kompresji
Sieci CDN warstw standardowa i premium zapewniają taką samą funkcjonalność kompresji, ale różni się w interfejsie użytkownika. Aby uzyskać więcej informacji na temat różnic między warstwami sieci CDN w warstwie standardowa i premium, zobacz [Omówienie usługi CDN Azure](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Standardowa profilów usługi CDN 
> [!NOTE]
> Ta sekcja dotyczy **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Verizon**, i **Azure CDN Standard from Akamai** profilów.
> 
> 

1. Na stronie profilu usługi CDN wybierz punktu końcowego usługi CDN, którą chcesz zarządzać.

    ![Punkty końcowe profilu usługi CDN](./media/cdn-file-compression/cdn-endpoints.png)

    Zostanie otwarta strona punktu końcowego usługi CDN.
2. Wybierz **kompresji**.

    ![Wybór kompresja sieci CDN](./media/cdn-file-compression/cdn-compress-select-std.png)

    Zostanie otwarta strona kompresji.
3. Wybierz **na** włączenie kompresji.

    ![Opcje kompresją pliku CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Użyj domyślnych typów MIME, lub zmodyfikować tę listę, dodając lub usuwając typów MIME.

   > [!TIP]
   > Mimo że jest to możliwe, nie zaleca się dotyczą kompresji skompresowanych formatów. Na przykład pliku ZIP, MP3, MP4 lub JPG.
   > 

   > [!NOTE]
   > Modyfikowanie domyślną listę typów MIME nie jest obecnie obsługiwane w usłudze Azure CDN w warstwie standardowa firmy Microsoft.
   > 

5. Po wprowadzeniu zmian, wybierz **Zapisz**.

### <a name="premium-cdn-profiles"></a>Profile CDN w warstwie Premium
> [!NOTE]
> Ta sekcja dotyczy wyłącznie **Azure CDN Premium from Verizon** profilów.
> 

1. Na stronie profilu usługi CDN wybierz **Zarządzaj**.

    ![Wybierz zarządzania sieci CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **HTTP dużych** kartę, a następnie umieść kursor nad **ustawienia pamięci podręcznej** okno wysuwane. Wybierz **kompresji**.

    ![Wybór kompresja sieci CDN](./media/cdn-file-compression/cdn-compress-select.png)

    Opcje kompresji są wyświetlane.

    ![Opcje kompresją pliku CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Włącz kompresję, wybierając **Kompresja włączona**. Wprowadź typy MIME chcesz kompresować jako listę rozdzielonych przecinkami (bez spacji) **typów plików** pole.

   > [!TIP]
   > Mimo że jest to możliwe, nie zaleca się dotyczą kompresji skompresowanych formatów. Na przykład pliku ZIP, MP3, MP4 lub JPG.
   > 

4. Po wprowadzeniu zmian, wybierz **aktualizacji**.

## <a name="compression-rules"></a>Reguły kompresji

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Usługa Azure CDN Standard from profile firmy Microsoft

Aby uzyskać **Azure CDN Standard from Microsoft** profile, tylko odpowiednie pliki są kompresowane. Aby kwalifikować się do kompresji, plik musi:
- Mieć typ MIME, który został [skonfigurowany dla kompresji](#enabling-compression).
- Jest większe niż 1 KB?
- Być mniejszy niż 8 MB

Te profile obsługują następujące kodowania kompresji:
- gzip (GNU zip)
- brotli 

Jeśli żądanie obsługuje więcej niż jeden typ kompresji, pierwszeństwo ma brotli kompresji.

Podczas żądania zasobu określa kompresję gzip i wyniki żądania to Chybienie pamięci podręcznej, usługa Azure CDN wykonuje kompresję gzip zasobu bezpośrednio na serwer protokołu POP. W efekcie skompresowanego pliku jest obsługiwany z pamięci podręcznej.

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN from Verizon profilów

Aby uzyskać **Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** profile, tylko odpowiednie pliki są kompresowane. Aby kwalifikować się do kompresji, plik musi:
- Być większa niż 128 bajtów
- Być mniejszy niż 3 MB

Te profile obsługują następujące kodowania kompresji:
- gzip (GNU zip)
- KORYGOWANIA
- bzip2
- brotli 

Jeśli żądanie obsługuje więcej niż jeden typ kompresji, te typy kompresji mają pierwszeństwo przed brotli kompresji.

Podczas żądania zasobu określa brotli kompresji (nagłówek HTTP jest `Accept-Encoding: br`), a wyniki żądania to Chybienie pamięci podręcznej usługi Azure CDN wykonuje kompresji brotli zasobu bezpośrednio na serwer protokołu POP. W efekcie skompresowanego pliku jest obsługiwany z pamięci podręcznej.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Usługa Azure CDN Standard from Akamai profilów

Aby uzyskać **Azure CDN Standard from Akamai** profile, wszystkie pliki kwalifikują się do kompresji. Jednak plik musi być typ MIME, który został [skonfigurowany dla kompresji](#enabling-compression).

Te profile gzip kompresji kodowanie tylko obsługiwane. Gdy punkt końcowy profilu zażąda pliku w formacie gzip, zawsze jest wymagany ze źródła, niezależnie od tego, w żądaniu klienta. 

## <a name="compression-behavior-tables"></a>Kompresja zachowanie tabel
W poniższych tabelach opisano zachowanie kompresji sieć CDN systemu Azure dla każdego scenariusza:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Kompresja jest wyłączona lub plik nie kwalifikuje się do kompresji
| Klient zażądał formatu (przy użyciu nagłówka Accept-Encoding) | Format pliku pamięci podręcznej | Odpowiedź usługi CDN do klienta | Informacje o&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Skompresowane |Skompresowane |Skompresowane | |
| Skompresowane |Nieskompresowane |Nieskompresowane | |
| Skompresowane |Nie pamięci podręcznej |Skompresowane lub bez kompresji |Odpowiedź źródła Określa, czy usługi CDN wykonuje kompresji. |
| Nieskompresowane |Skompresowane |Nieskompresowane | |
| Nieskompresowane |Nieskompresowane |Nieskompresowane | |
| Nieskompresowane |Nie pamięci podręcznej |Nieskompresowane | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Kompresja jest włączona, a plik kwalifikuje się do kompresji
| Klient zażądał formatu (przy użyciu nagłówka Accept-Encoding) | Format pliku pamięci podręcznej | Odpowiedź usługi CDN do klienta | Uwagi |
| --- | --- | --- | --- |
| Skompresowane |Skompresowane |Skompresowane |Sieci CDN transkoduje między obsługiwanych formatów. |
| Skompresowane |Nieskompresowane |Skompresowane |Sieci CDN wykonuje kompresji. |
| Skompresowane |Nie pamięci podręcznej |Skompresowane |Jeśli punkt początkowy zwraca nieskompresowanego pliku, usługi CDN nie wykonuje kompresji. <br/>**Usługa Azure CDN from Verizon** przekazuje nieskompresowanego pliku na pierwsze żądanie i kompresuje i buforuje plik dla kolejnych żądań. <br/>Pliki z `Cache-Control: no-cache` nagłówka nigdy nie są kompresowane. |
| Nieskompresowane |Skompresowane |Nieskompresowane |Sieci CDN wykonuje dekompresji. |
| Nieskompresowane |Nieskompresowane |Nieskompresowane | |
| Nieskompresowane |Nie pamięci podręcznej |Nieskompresowane | |

## <a name="media-services-cdn-compression"></a>Kompresja sieci CDN usługi Media Services
Dla punktów końcowych włączone służącą do strumieniowego przesyłania multimediów usługi CDN kompresji jest domyślnie włączone dla następujących typów MIME: 
- application/vnd.ms-sstr+xml 
- Aplikacja/dash + xml
- application/vnd.apple.mpegurl
- application/f4m+xml 

## <a name="see-also"></a>Zobacz także
* [Rozwiązywanie problemów związanych z kompresją pliku CDN](cdn-troubleshoot-compression.md)    

