---
title: Podstawowe raporty z usługi Verizon | Dokumentacja firmy Microsoft
description: 'Aby wyświetlić wzorców użycia dla sieci CDN, za pomocą następujących raportów: Przepustowość, przetransferowane dane, trafienia, stany pamięci podręcznej, współczynnik, przesłanych danych protokołów IPV4/IPV6 trafień w pamięci podręcznej.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6eb0fe592196466f7f49c21ce38afdf13b254d86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61061539"
---
# <a name="core-reports-from-verizon"></a>Raporty podstawowe z usługi Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Za pomocą Verizon podstawowych raportów za pośrednictwem portalu zarządzania dla profilów Verizon, można wyświetlić wzorców użycia dla sieci CDN za pomocą następujących raportów:

* Przepustowość
* Przetransferowane dane
* Trafienia
* Stany pamięci podręcznej
* Stosunek trafień w pamięci podręcznej
* Przesyłane dane IPv4/IPV6.

## <a name="accessing-verizon-core-reports"></a>Uzyskiwanie dostępu do Raporty podstawowe z usługi Verizon
1. Z poziomu bloku profil CDN kliknij **Zarządzaj** przycisku.
   
    ![Przycisk Zarządzaj w profilu CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **Analytics** kartę, a następnie umieść kursor nad **Raporty podstawowe** okno wysuwane. Kliknij raport, w menu.
   
    ![Portal zarządzania usługi CDN — menu Raporty podstawowe](./media/cdn-reports/cdn-core-reports.png)

3. Dla każdego raportu, wybierz zakres dat z **zakres dat** listy. Można albo wybierz zakres dat wstępnie zdefiniowane, takich jak **już dziś** lub **ten tydzień**, lub wybrać **niestandardowe** i ręcznie wprowadź zakres dat, klikając ikony kalendarza. 

4. Po wybraniu zakresu dat kliknij **Przejdź** podczas generowania raportu. 

4. Jeśli chcesz wyeksportować dane w formacie programu Excel, kliknij ikonę programu Excel powyżej **Przejdź** przycisku.

## <a name="bandwidth"></a>Przepustowość
Raport przepustowości składa się z tabeli programu graph i danych, która wskazuje wykorzystanie przepustowości sieci CDN dla protokołu HTTP i HTTPS za pośrednictwem w danym okresie, w MB/s. Można wyświetlić użycie przepustowości, we wszystkich punktów POP, lub dla określonego punktu obecności. Ten raport służy do wyświetlania, wzrostów ruchu i rozpowszechniania POP.

Z **węzłów brzegowych** listy wybierz **wszystkich węzłów brzegowych** widoczny ruch z wszystkich węzłów lub wybrania określonego regionu.

Raport jest aktualizowany co pięć minut.

![Raport przepustowości](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Przetransferowane dane
Ten raport zawiera wykres i tabelę danych wskazującą obciążenie ruchu w sieci CDN dla protokołu HTTP i HTTPS za pośrednictwem w danym okresie, w GB. Można wyświetlić użycie ruchu, we wszystkich punktów POP, lub dla określonego punktu obecności. Ten raport służy do wyświetlania wzrostów ruchu i dystrybucji w lokalizacji POP.

Z **węzłów brzegowych** listy wybierz **wszystkich węzłów brzegowych** widoczny ruch z wszystkich węzłów lub wybrania określonego regionu.

Raport jest aktualizowany co pięć minut.

![Przetransferowane dane raportu](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Trafienia (kody stanu)
W tym raporcie przedstawiono dystrybucję kodów stanu żądania dla zawartości. Każde żądanie zawartości generuje kod stanu HTTP. Kod stanu w tym artykule opisano sposób granicznych punktów obecności obsługi żądania. Na przykład kod stanu 2xx wskazuje, że żądanie zostało pomyślnie obsłużone do klienta, podczas kod stanu 4xx oznacza, że wystąpił błąd. Aby uzyskać więcej informacji na temat kodów stanu HTTP, zobacz [kodów stanu listy HTTP](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Trafienia w raporcie](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Stan pamięci podręcznej
Ten raport zawiera opis rozkład trafień w pamięci podręcznej i Chybienia pamięci podręcznej do obsługi żądań klientów. Ponieważ największą wydajność wynika z trafień w pamięci podręcznej, można zoptymalizować szybkości dostarczania danych, minimalizując Chybienia pamięci podręcznej i trafień w pamięci podręcznej wygasła. 

Aby zmniejszyć Chybienia pamięci podręcznej, należy skonfigurować serwer pochodzenia w celu zminimalizowania użycia z następujących czynności: 
 * `no-cache` Nagłówki odpowiedzi
 * Buforowanie ciągu zapytania, chyba że niezbędnych  
 * Kody odpowiedzi nie podlega buforowaniu

Aby zmniejszyć trafień w pamięci podręcznej wygasłe, ustaw zasobów `max-age` na długi okres, aby zminimalizować liczbę żądań do serwera pochodzenia.

![Raport stany pamięci podręcznej](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Stany pamięci podręcznej głównego obejmują:
* TCP_HIT: Obsługiwane z serwer graniczny. Obiekt w pamięci podręcznej i nie przekroczył jego max-age.
* TCP_MISS: Obsługiwane z serwera źródłowego. Obiekt nie był w pamięci podręcznej, a odpowiedź była do źródła.
* TCP_EXPIRED _MISS: Obsługiwane z serwera źródłowego po ponownej weryfikacji za pomocą źródła. Obiekt był w pamięci podręcznej, ale przekroczył jego max-age. Ponowne sprawdzenie poprawności zależnego przy użyciu źródła spowodowała obiektu pamięci podręcznej, jest zastępowany przez nową odpowiedź od źródła.
* TCP_EXPIRED _HIT: Obsługiwane z przeglądarki Edge po ponownej weryfikacji za pomocą źródła. Obiekt w pamięci podręcznej, ale przekroczył jego max-age. Ponowne sprawdzenie poprawności zależnego w serwerze źródłowym w wyniku obiektu pamięci podręcznej są modyfikowane.

### <a name="full-list-of-cache-statuses"></a>Pełna lista stany pamięci podręcznej
* TCP_HIT — ten stan jest zgłaszany, gdy żądanie jest obsługiwany bezpośrednio w punkcie POP do klienta. Element zawartości natychmiast są dostarczane z punktu POP, gdy jest buforowana na POP najbliżej danego klienta, a ma nieprawidłowy czas wygaśnięcia (TTL). Czas wygaśnięcia jest określany przez następujące nagłówki odpowiedzi:
  
  * Cache-Control: s-maxage
  * Cache-Control: max-age
  * Wygasa
* TCP_MISS: Ten stan wskazuje, że zbuforowaną wersję żądany zasób nie został odnaleziony w obecności najbliżej danego klienta. Element zawartości jest wymagany serwer pochodzenia lub serwer pochodzenia tarczy. Jeśli serwer pochodzenia lub serwera tarczy pochodzenia zwraca element zawartości, jest obsługiwane dla klienta i buforowana na kliencie i serwerze granicznym. W przeciwnym razie kod stanu – 200 (na przykład 403 zabronione lub 404 Nie znaleziono) jest zwracany.
* TCP_EXPIRED_HIT: Ten stan jest zgłaszany podczas obsługi żądania, który jest przeznaczony dla zasobu z wygasłych TTL bezpośrednio w punkcie POP do klienta. Na przykład, jeśli element zawartości przez max-age wygasł. 
  
   Wygasłe żądanie zwykle powoduje żądanie ponownego sprawdzania poprawności do serwera pochodzenia. Stanu TCP_EXPIRED_HIT występują serwer pochodzenia musi wskazywać, nie istnieje nowsza wersja elementu zawartości. Ta sytuacja zwykle powoduje aktualizację Cache-Control zasobów i plików nagłówkowych Expires.
* TCP_EXPIRED_MISS: Ten stan jest zgłaszany, gdy nowszą wersję elementu wygasłe zawartości pamięci podręcznej jest obsługiwany w punkcie POP do klienta. Ten stan występuje, jeśli wygasł czas wygaśnięcia pamięci podręcznej zasobów (na przykład wygasła max-age) i serwer pochodzenia zwraca nowszą wersję tego zasobu. Nowa wersja elementu zawartości są dostarczane do klienta, a nie w pamięci podręcznej wersji. Ponadto jest ona buforowana na serwerze granicznym, jak i klienta.
* CONFIG_NOCACHE: Ten stan wskazuje, konfiguracji specyficznych dla klientów punktów obecności krawędzi zablokowaną zasób z pamięci podręcznej.
* Brak — ten stan wskazuje, czy nie było wykonywane sprawdzanie aktualności zawartości pamięci podręcznej.
* TCP_CLIENT_REFRESH_MISS: Ten stan jest zgłaszany, gdy klient HTTP, takie jak przeglądarki, wymusza krawędź POP, aby pobrać nową wersję starych zasobów z serwera pochodzenia. Domyślnie serwery uniemożliwić klienta HTTP wymuszanie serwerów brzegowych, aby pobrać nową wersję elementu zawartości z serwera pochodzenia.
* TCP_PARTIAL_HIT: Ten stan jest zgłaszany, gdy żądania zakresu bajtów powoduje trafień dla częściowo pamięci podręcznej elementu zawartości. Żądany zakres bajtów natychmiast jest obsługiwany w punkcie POP do klienta.
* UNCACHEABLE: Ten stan jest zgłaszany, gdy zasób `Cache-Control` i `Expires` nagłówki wskazują, że go powinien nie być buforowany w menu Podręcznym przez klienta HTTP. Te typy żądań, które są obsługiwane z serwera pochodzenia.

## <a name="cache-hit-ratio"></a>Stosunek trafień w pamięci podręcznej
Ten raport wskazuje procent żądań, które były przekazywane bezpośrednio z pamięci podręcznej z pamięci podręcznej.

Raport zawiera następujące informacje:

* Żądanej zawartości był buforowany w najbardziej zbliżony do osoby żądającej POP.
* Żądanie zostało obsłużone bezpośrednio z krawędzią naszej sieci.
* Żądanie nie wymagało ponownego sprawdzania poprawności w serwerze źródłowym.

Raport nie zawiera:

* Żądania, które zostaną odrzucone z powodu opcji filtrowania kraju.
* Żądania zasobów, w których nagłówki wskazują, że nie powinny one zapisywane. Na przykład `Cache-Control: private`, `Cache-Control: no-cache`, lub `Pragma: no-cache` nagłówki zabrania buforowania zasobów.
* Żądania zakresu bajtów dla częściowo buforowanej zawartości.

Formuła jest następująca: (TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

![Raport Stosunek trafień w pamięci podręcznej](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Przetransferowane dane IPv4/IPV6.
Ten raport przedstawia rozkład użycia ruchu sieciowego w programie vs IPV4 IPV6.

![Przetransferowane dane IPv4/IPV6.](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Zagadnienia do rozważenia
Raporty mogą być generowane tylko w ciągu ostatnich 18 miesięcy.

