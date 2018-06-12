---
title: Zwiększenie wydajności przez kompresowanie plików w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zwiększyć szybkość transferu plików i zwiększyć wydajność ładowania strony przez kompresowanie plików w usłudze Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: bdff57275cf123079004ada732fe782d98399d71
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260400"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Zwiększenie wydajności przez kompresowanie plików w usłudze Azure CDN
Kompresji plików to prosta i skuteczna metoda zwiększenia szybkości transferu plików i zwiększyć wydajność ładowanie stron dzięki zmniejszeniu rozmiar tego pliku przed wysłaniem go z serwera. Kompresja plików można obniżyć koszty przepustowości i zapewnia bardziej odpowiednie środowisko dla użytkowników.

Istnieją dwa sposoby, aby włączyć kompresję pliku:

- Włącz kompresję na serwerze pochodzenia. W takim przypadku Azure CDN przekazuje skompresowane pliki i dostarcza je klientom żądającym je.
- Włącz kompresję bezpośrednio na serwerach POP w sieci CDN (*kompresji w locie*). W takim przypadku CDN kompresuje pliki i służy je dla użytkowników końcowych, nawet jeśli nie zostały skompresowane przez serwer pochodzenia.

> [!IMPORTANT]
> Zmiany konfiguracji usługi Azure CDN może zająć trochę czasu na rozpropagowanie za pośrednictwem sieci: 
- W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
- W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
- Aby uzyskać **Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** profile, propagacji zazwyczaj kończy w ciągu 10 minut. 
>
> Jeśli podczas konfigurowania kompresji po raz pierwszy dla punktu końcowego CDN, należy wziąć pod uwagę oczekiwania 1 – 2 godz. przed Rozwiązywanie problemów z do upewnij się, że ustawienia kompresji zostaną rozpropagowane do lokalizacji POP.
> 
> 

## <a name="enabling-compression"></a>Włączanie kompresji
Warstwy sieci CDN w warstwie standardowa i premium zapewniają te same funkcje kompresji, ale różni się w interfejsie użytkownika. Aby uzyskać więcej informacji na temat różnic między warstwami sieci CDN w warstwie standardowa i premium, zobacz [Omówienie usługi Azure CDN](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Profile sieci CDN w warstwie standardowa 
> [!NOTE]
> Ta sekcja dotyczy **Azure CDN Standard from Microsoft**, **Azure CDN Standard from Verizon**, i **Azure CDN Standard from Akamai** profilów.
> 
> 

1. Na stronie profilu CDN wybierz punkt końcowy CDN, którą chcesz zarządzać.
   
    ![Punkty końcowe profilu CDN](./media/cdn-file-compression/cdn-endpoints.png)
   
    Zostanie otwarta strona punktu końcowego CDN.
2. Wybierz **kompresji**.

    ![Wybór kompresji CDN](./media/cdn-file-compression/cdn-compress-select-std.png)
   
    Zostanie otwarta strona kompresji.
3. Wybierz **na** włączenie kompresji.
   
    ![Opcje kompresji pliku CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Użyj domyślnych typów MIME lub zmodyfikuj listę przez dodanie lub usunięcie typów MIME.
   
   > [!TIP]
   > Mimo że jest to możliwe, nie zaleca się zastosowanie kompresji w formatach skompresowany. Na przykład ZIP, MP3, MP4 lub JPG.
   > 
 
5. Po wprowadzeniu zmiany, wybierz **zapisać**.

### <a name="premium-cdn-profiles"></a>Profile sieci CDN w warstwie Premium
> [!NOTE]
> W tej sekcji dotyczą tylko **Azure CDN Premium from Verizon** profilów.
> 

1. Na stronie profilu CDN wybierz **Zarządzaj**.
   
    ![Wybierz Zarządzanie CDN](./media/cdn-file-compression/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **HTTP dużych** , a następnie umieść kursor nad **ustawienia pamięci podręcznej** wysuwane okno. Wybierz **kompresji**.

    ![Wybór kompresji CDN](./media/cdn-file-compression/cdn-compress-select.png)
   
    Opcje kompresji są wyświetlane.
   
    ![Opcje kompresji pliku CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Włącz kompresję, wybierając **włączyć kompresji,**. Wprowadź typy MIME chcesz kompresować jako listę rozdzielaną przecinkami (bez spacji) **typów plików** pole.
   
   > [!TIP]
   > Mimo że jest to możliwe, nie zaleca się zastosowanie kompresji w formatach skompresowany. Na przykład ZIP, MP3, MP4 lub JPG.
   > 
    
4. Po wprowadzeniu zmiany, wybierz **aktualizacji**.

## <a name="compression-rules"></a>Reguły kompresji

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard from profile firmy Microsoft

Aby uzyskać **Azure CDN Standard from Microsoft** profile, wszystkie pliki kwalifikują się do kompresji. Niemniej jednak plik musi być typu MIME, który został [skonfigurowane kompresji](#enabling-compression).

Te profile obsługuje następujące rodzaje kodowania kompresji:
- gzip (GNU zip)
- brotli 
 
Jeśli żądanie obsługuje więcej niż jeden typ kompresji, te typy kompresji mają pierwszeństwo przed brotli kompresji.

Gdy żądanie zasobu określa kompresję gzip i wyniki żądania w Chybienie pamięci podręcznej, usługi Azure CDN wykonuje kompresję gzip trwałego bezpośrednio na serwer protokołu POP. W efekcie skompresowany plik jest udostępniany z pamięci podręcznej.

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN from Verizon profilów

Dla **Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** profile, tylko odpowiednie pliki są skompresowane. Aby kwalifikować się do kompresji, plik musi:
- Jest większe niż 128 bajtów
- Być mniejszy niż 1 MB
 
Te profile obsługuje następujące rodzaje kodowania kompresji:
- gzip (GNU zip)
- KORYGOWANIA
- bzip2
- brotli 
 
Jeśli żądanie obsługuje więcej niż jeden typ kompresji, te typy kompresji mają pierwszeństwo przed brotli kompresji.

Gdy żądanie zasobu określa kompresji brotli (nagłówek HTTP jest `Accept-Encoding: br`) i z żądania powoduje Chybienie pamięci podręcznej, usługa Azure CDN wykonuje kompresji brotli trwałego bezpośrednio na serwer protokołu POP. W efekcie skompresowany plik jest udostępniany z pamięci podręcznej.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard from Akamai profilów

Aby uzyskać **Azure CDN Standard from Akamai** profile, wszystkie pliki kwalifikują się do kompresji. Niemniej jednak plik musi być typu MIME, który został [skonfigurowane kompresji](#enabling-compression).

Te profile obsługuje gzip kompresji tylko kodowania. Gdy punkt końcowy profilu żąda pliku w formacie gzip, jest zawsze wymagany ze źródła, niezależnie od tego, w żądaniu klienta. 

## <a name="compression-behavior-tables"></a>Kompresja tabel zachowanie
W poniższych tabelach opisano zachowanie kompresji Azure CDN dla każdego scenariusza:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Kompresja jest wyłączona lub plik nie kwalifikuje się do kompresji
| Klient zażądał formatu (za pośrednictwem nagłówka Accept-Encoding) | Format pliku pamięci podręcznej | Odpowiedź sieci CDN do klienta | Uwagi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Skompresowane |Skompresowane |Skompresowane | |
| Skompresowane |Nieskompresowanych |Nieskompresowanych | |
| Skompresowane |Nie w pamięci podręcznej |Skompresowane lub nieskompresowane |Odpowiedź pochodzenia określa, czy CDN wykonuje kompresji. |
| Nieskompresowanych |Skompresowane |Nieskompresowanych | |
| Nieskompresowanych |Nieskompresowanych |Nieskompresowanych | |
| Nieskompresowanych |Nie w pamięci podręcznej |Nieskompresowanych | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Kompresja jest włączona i pliku nie kwalifikuje się do kompresji
| Klient zażądał formatu (za pośrednictwem nagłówka Accept-Encoding) | Format pliku pamięci podręcznej | Odpowiedź z sieci CDN do klienta | Uwagi |
| --- | --- | --- | --- |
| Skompresowane |Skompresowane |Skompresowane |Transcodes CDN pomiędzy obsługiwanych formatów. |
| Skompresowane |Nieskompresowanych |Skompresowane |CDN wykonuje kompresji. |
| Skompresowane |Nie w pamięci podręcznej |Skompresowane |Jeśli punkt początkowy zwraca nieskompresowanego pliku, w sieci CDN wykonuje kompresji. <br/>**Usługi Azure CDN from Verizon** przekazuje nieskompresowanego pliku na pierwsze żądanie i kompresuje i buforuje plik dla kolejnych żądań. <br/>Pliki z `Cache-Control: no-cache` nigdy nie są kompresowane nagłówka. |
| Nieskompresowanych |Skompresowane |Nieskompresowanych |CDN wykonuje dekompresji. |
| Nieskompresowanych |Nieskompresowanych |Nieskompresowanych | |
| Nieskompresowanych |Nie w pamięci podręcznej |Nieskompresowanych | |

## <a name="media-services-cdn-compression"></a>Kompresja CDN usługi multimediów
Punkty końcowe włączone do przesyłania strumieniowego multimediów usługi CDN kompresji jest domyślnie włączona dla następujących typów MIME: 
- application/vnd.ms-sstr+xml 
- Aplikacja/dash + xml
- application/vnd.apple.mpegurl
- application/f4m+xml 

## <a name="see-also"></a>Zobacz także
* [Rozwiązywanie problemów związanych z kompresją pliku CDN](cdn-troubleshoot-compression.md)    

