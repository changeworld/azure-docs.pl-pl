---
title: Podstawowe raporty od Verizon | Dokumenty firmy Microsoft
description: 'Wzorce użycia sieci CDN można wyświetlać przy użyciu następujących raportów: Przepustowość, Przesyłane dane, Trafienia, Stany pamięci podręcznej, Współczynnik trafień w pamięci podręcznej, Przesyłane dane IPV4/IPV6.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d48ddafdc1ec30ae1533b3a3101582f33e7f4b5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594159"
---
# <a name="core-reports-from-verizon"></a>Podstawowe raporty z Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Korzystając z raportów Verizon Core za pośrednictwem profilu Zarządzaj portalem dla Verizon, można wyświetlić wzorce użycia sieci CDN za pomocą następujących raportów:

* Przepustowość
* Przesyłane dane
* Trafienia
* Stany pamięci podręcznej
* Współczynnik trafień w pamięci podręcznej
* Przesyłane dane IPV4/IPV6

## <a name="accessing-verizon-core-reports"></a>Uzyskiwanie dostępu do raportów Verizon Core
1. W bloku profilu sieci CDN kliknij przycisk **Zarządzaj.**
   
    ![Przycisk Zarządzanie profilem CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania siecią CDN.
2. Umieść wskaźnik myszy na karcie **Analytics,** a następnie umieść wskaźnik myszy na wysu wysu wysu **wysu wysu wysu wysu.** Kliknij raport w menu.
   
    ![Portal zarządzania CDN — menu Raporty podstawowe](./media/cdn-reports/cdn-core-reports.png)

3. Dla każdego raportu wybierz zakres dat z listy **Zakres dat.** Można wybrać wstępnie zdefiniowany zakres dat, taki jak **Dzisiaj** lub **Ten tydzień,** lub wybrać **opcję Niestandardowy** i ręcznie wprowadzić zakres dat, klikając ikony kalendarza. 

4. Po wybraniu zakresu dat kliknij przycisk **Przejdź,** aby wygenerować raport. 

4. Jeśli chcesz wyeksportować dane w formacie programu Excel, kliknij ikonę programu Excel nad przyciskiem **Przejdź.**

## <a name="bandwidth"></a>Przepustowość
Raport przepustowości składa się z wykresu i tabeli danych, która wskazuje użycie przepustowości sieci CDN dla protokołu HTTP i HTTPS w określonym okresie czasu, w Mb/s. Można wyświetlić użycie przepustowości we wszystkich punktu obecności lub dla określonego punktu obecności. Ten raport umożliwia wyświetlanie skoków ruchu i dystrybucji dla punktów usługowych.

Na liście **Węzły krawędzi** wybierz **pozycję Wszystkie węzły krawędzi,** aby wyświetlić ruch ze wszystkich węzłów, lub wybierz określony region.

Raport jest aktualizowany co pięć minut.

![Raport przepustowości](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Przesyłane dane
Ten raport składa się z wykresu i tabeli danych, która wskazuje użycie ruchu usługi CDN dla protokołu HTTP i HTTPS w określonym okresie czasu, w GB. Można wyświetlić użycie ruchu we wszystkich punktu obecności lub dla określonego punktu obecności. Ten raport umożliwia wyświetlanie skoków ruchu i dystrybucji między punktami punktu widzenia.

Na liście **Węzły krawędzi** wybierz **pozycję Wszystkie węzły krawędzi,** aby wyświetlić ruch ze wszystkich węzłów, lub wybierz określony region.

Raport jest aktualizowany co pięć minut.

![Raport przesyłany danymi](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Trafienia (kody stanu)
W tym raporcie opisano dystrybucję kodów stanu żądania dla zawartości. Każde żądanie zawartości generuje kod stanu HTTP. Kod stanu opisuje, jak punkty brzegowe krawędzi obsługiwane żądania. Na przykład kod stanu 2xx wskazuje, że żądanie zostało pomyślnie doręczone klientowi, podczas gdy kod stanu 4xx wskazuje, że wystąpił błąd. Aby uzyskać więcej informacji o kodach stanu HTTP, zobacz [Lista kodów stanu HTTP](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Raport trafień](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Stany pamięci podręcznej
W tym raporcie opisano dystrybucję trafień pamięci podręcznej i braków w pamięci podręcznej dla żądań klientów. Ponieważ najszybsza wydajność wynika z trafień pamięci podręcznej, można zoptymalizować szybkość dostarczania danych, minimalizując pominięcia w pamięci podręcznej i wygasłe trafienia w pamięci podręcznej. 

Aby zmniejszyć liczbę pominiętych pamięci podręcznej, skonfiguruj serwer pochodzenia, aby zminimalizować użycie następujących elementów: 
 * `no-cache`nagłówki odpowiedzi
 * Buforowanie ciągów zapytań, chyba że jest to bezwzględnie konieczne  
 * Kody odpowiedzi nie kryjówkowe

Aby zmniejszyć liczbę trafień wygasłej `max-age` pamięci podręcznej, ustaw zasób na długi okres, aby zminimalizować liczbę żądań do serwera pochodzenia.

![Raport o statusach pamięci podręcznej](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Stany głównej pamięci podręcznej obejmują:
* TCP_HIT: Obsługiwane z serwera brzegowego. Obiekt znajdował się w pamięci podręcznej i nie przekroczył jego maksymalnego wieku.
* TCP_MISS: Obsługiwane z serwera pochodzenia. Obiekt nie był w pamięci podręcznej i odpowiedź została z powrotem do początku.
* TCP_EXPIRED _MISS: Obsługiwane z serwera pochodzenia po ponownym zadaniu z pochodzeniem. Obiekt znajdował się w pamięci podręcznej, ale przekroczył jego maksymalny wiek. Ponowne prawo ważności ze źródłem spowodowało, że obiekt pamięci podręcznej został zastąpiony przez nową odpowiedź od początku.
* TCP_EXPIRED _HIT: Podawane z krawędzi po ponownym zadłużeniu z początkiem. Obiekt był w pamięci podręcznej, ale przekroczył jego maksymalny wiek. Ponowne zadośćuczynienie za pomocą serwera pochodzenia spowodowało, że obiekt pamięci podręcznej nie został zmodyfikowany.

### <a name="full-list-of-cache-statuses"></a>Pełna lista stanów pamięci podręcznej
* TCP_HIT — ten stan jest zgłaszany, gdy żądanie jest obsługiwane bezpośrednio z punktu obecności do klienta. Zasób jest natychmiast obsługiwany z punktu obecności, gdy jest buforowany na przystaniu sieciowym najbliżej klienta i ma prawidłowy czas wygaśnięcia (TTL). Czas wygaśnięcia jest określany przez następujące nagłówki odpowiedzi:
  
  * Kontrola pamięci podręcznej: s-maxage
  * Kontrola pamięci podręcznej: max-age
  * Wygasa
* TCP_MISS: Ten stan wskazuje, że nie znaleziono wersji buforowanej żądanego zasobu w posyłce obok klienta. Zasób jest wymagany z serwera pochodzenia lub serwera osłony początkowej. Jeśli serwer źródłowy lub serwer osłony pochodzenia zwraca zasób, jest on obsługiwany klientowi i buforowany zarówno na kliencie, jak i na serwerze brzegowym. W przeciwnym razie zwracany jest kod stanu inny niż 200 (na przykład 403 Zabronione lub 404 Nie znaleziono).
* TCP_EXPIRED_HIT: Ten stan jest zgłaszany, gdy żądanie, które dotyczy zasobu z wygasłym czasem wygaśnięcia, zostało doręczone bezpośrednio z punktu obecności do klienta. Na przykład, gdy maksymalny wiek zasobu wygasł. 
  
   Wygasłe żądanie zazwyczaj powoduje żądanie ponownego zweryfikowania na serwerze pochodzenia. Aby wystąpił stan TCP_EXPIRED_HIT, serwer pochodzenia musi wskazać, że nowsza wersja zasobu nie istnieje. Ta sytuacja zazwyczaj powoduje aktualizację nagłówków Cache-Control i Expires zasobu.
* TCP_EXPIRED_MISS: Ten stan jest zgłaszany, gdy nowsza wersja wygasłego zasobu w pamięci podręcznej jest obsługiwana z punktu obecności do klienta. Ten stan występuje, gdy czas wygaśnięcia dla zasobu w pamięci podręcznej wygasł (na przykład wygasły max-age) i serwer pochodzenia zwraca nowszą wersję tego zasobu. Ta nowa wersja zasobu jest obsługiwana klientowi zamiast wersji buforowanej. Ponadto jest buforowany na serwerze brzegowym i na kliencie.
* CONFIG_NOCACHE: Ten stan wskazuje, że konfiguracja specyficzne dla klienta, wyskakuje krawędź, uniemożliwiła buforowanie zasobu.
* BRAK — ten stan wskazuje, że nie przeprowadzono sprawdzania świeżości zawartości pamięci podręcznej.
* TCP_CLIENT_REFRESH_MISS: Ten stan jest zgłaszany, gdy klient HTTP, taki jak przeglądarka, wymusza punkt obecności krawędzi, aby pobrać nową wersję nieświeżego zasobu z serwera pochodzenia. Domyślnie serwery uniemożliwiają klientowi HTTP zmuszanie serwerów brzegowych do pobierania nowej wersji zasobu z serwera pochodzenia.
* TCP_PARTIAL_HIT: Ten stan jest zgłaszany, gdy żądanie zakresu bajtów powoduje trafienie dla częściowo buforowanego zasobu. Żądany zakres bajtów jest natychmiast obsługiwany z punktu obecności do klienta.
* UNCACHEABLE: Ten stan jest zgłaszany, gdy zasoby `Cache-Control` i `Expires` nagłówki wskazują, że nie powinny być buforowane na pop lub przez klienta HTTP. Tego typu żądania są obsługiwane z serwera pochodzenia.

## <a name="cache-hit-ratio"></a>Współczynnik trafień w pamięci podręcznej
Ten raport wskazuje procent buforowanych żądań, które były obsługiwane bezpośrednio z pamięci podręcznej.

Raport zawiera następujące szczegóły:

* Żądana zawartość została buforowana w poczcie podręcznej najbliżej żądacza.
* Wniosek został doręczony bezpośrednio z krawędzi naszej sieci.
* Żądanie nie wymaga ponownego zadośćuczynienia na serwerze pochodzenia.

Raport nie zawiera:

* Żądania, które zostały odrzucone z powodu opcji filtrowania kraju/regionu.
* Żądania dotyczące zasobów, których nagłówki wskazują, że nie powinny być buforowane. Na przykład `Cache-Control: private` `Cache-Control: no-cache`, `Pragma: no-cache` lub nagłówki uniemożliwiają buforowanie zasobu.
* Żądania zakresu bajtów dla częściowo buforowanej zawartości.

Wzór to: (TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

![Raport współczynnika trafień pamięci podręcznej](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Przesyłane dane IPV4/IPV6
Ten raport pokazuje rozkład użycia ruchu w ipv4 vs IPV6.

![Przesyłane dane IPV4/IPV6](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Zagadnienia do rozważenia
Raporty mogą być generowane tylko w ciągu ostatnich 18 miesięcy.

