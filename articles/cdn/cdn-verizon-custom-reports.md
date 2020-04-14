---
title: Raporty niestandardowe z Verizon | Dokumenty firmy Microsoft
description: 'Wzorce użycia sieci CDN można wyświetlać przy użyciu następujących raportów: Przepustowość, Przesyłane dane, Trafienia, Stany pamięci podręcznej, Współczynnik trafień w pamięci podręcznej, Przesyłane dane IPV4/IPV6.'
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: allensu
ms.openlocfilehash: 737803f5b7fcddb07787afbb48354b4ca922e9ed
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259752"
---
# <a name="custom-reports-from-verizon"></a>Raporty niestandardowe z Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Korzystając z raportów niestandardowych Verizon za pośrednictwem portalu Zarządzaj dla profilów Verizon, można zdefiniować typ danych, które mają być zbierane dla raportów CNAMEs krawędzi.


## <a name="accessing-verizon-custom-reports"></a>Uzyskiwanie dostępu do raportów niestandardowych Verizon
1. W bloku profilu sieci CDN kliknij przycisk **Zarządzaj.**
   
    ![Przycisk Zarządzanie profilem CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania siecią CDN.
2. Umieść wskaźnik myszy na karcie **Analytics,** a następnie umieść wskaźnik myszy na **wysuwaku raportów niestandardowych.** Kliknij **pozycję Edge CNAMEs**.
   
    ![Portal zarządzania siecią CDN — menu Raporty niestandardowe](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Raport niestandardowy EDGE CNAMES
Raport niestandardowy Edge CNAMES zawiera trafienia i dane przesyłane statystyki dla CNAMEs krawędzi, na których niestandardowe rejestrowanie raportu została włączona. CnAMEs krawędzi składają się z nazwy hosta punktu końcowego usługi Azure CDN i wszystkie skojarzone nazwy hostów domeny niestandardowej. 

Rejestrowanie danych raportu niestandardowego rozpoczyna się godzinę po włączeniu możliwości raportowania niestandardowego CNAME na krawędzi. Można wyświetlić dane raportu, generując raport EDGE CNAMEs dla określonej platformy lub dla wszystkich platform. Zakres tego raportu jest ograniczony do cnames krawędzi, dla których dane raportu niestandardowego zostały zebrane w określonym okresie czasu. Raport CNAMEs krawędzi składa się z wykresu i tabeli danych dla 10 głównych CNAMEs krawędzi zgodnie z metryką zdefiniowaną w opcji Metryki. 

Generowanie raportu niestandardowego przez zdefiniowanie następujących opcji raportu:

- Metryki: obsługiwane są następujące opcje:

   - Trafienia: wskazuje całkowitą liczbę żądań, które są kierowane do cname krawędzi, na którym jest włączona funkcja raportowania niestandardowego. Ta metryka nie zawiera kodu stanu zwróconego do klienta.

   - Dane przesyłane: wskazuje całkowitą ilość danych przesyłanych z serwerów brzegowych do klientów HTTP (na przykład przeglądarek internetowych) dla żądań kierowanych do cname krawędzi, na którym jest włączona funkcja raportowania niestandardowego. Ilość przesyłanych danych jest obliczana przez dodanie nagłówków odpowiedzi HTTP do treści odpowiedzi. W rezultacie ilość danych przesłanych dla każdego zasobu jest większa niż jego rzeczywisty rozmiar pliku.

- Grupowania: Określa typ statystyk wyświetlanych poniżej wykresu słupkowego. Obsługiwane są następujące opcje:

   - Kody odpowiedzi HTTP: organizuje statystyki według kodu odpowiedzi HTTP (na przykład 200, 403 itp.) zwrócone do klienta. 

   - Stan pamięci podręcznej: organizuje statystyki według stanu pamięci podręcznej.


Aby ustawić zakres dat dla raportu, można wybrać wstępnie zdefiniowany zakres dat, taki jak **Dzisiaj** lub **Ten tydzień,** z listy rozwijanej lub wybrać **opcję Niestandardowe** i ręcznie wprowadzić zakres dat, klikając ikony kalendarza. 

Po wybraniu zakresu dat kliknij przycisk **Przejdź,** aby wygenerować raport.

Dane w formacie programu Excel można wyeksportować, klikając symbol programu Excel po prawej stronie przycisku **Przejdź.**

![Raport CNAMEs](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Pola raportu niestandardowego CNAMES krawędzi

| Pole                     | Opis   |
|---------------------------|---------------|
| 2xx                       | Wskazuje całkowitą liczbę przesłanych żądań lub danych (MB) dla cname krawędzi, która powoduje 2xx kod stanu HTTP (na przykład 200 OK). |
| 3xx (włas ie)                       | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla cname krawędzi, która powoduje 3xx kod stanu HTTP (na przykład 302 Znaleziono lub 304 Nie zmodyfikowano. |
| 4xx (włas iem)                       | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla cname krawędzi, co powoduje 4xx kod stanu HTTP (na przykład 400 Bad Request, 403 Zabronione lub 404 Nie znaleziono). |
| 5xx (właso)                       | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla cname krawędzi, która powoduje 5xx kod stanu HTTP (na przykład 500 Wewnętrzny błąd serwera lub 502 Bad Gateway). |
| Procent trafienia w pamięci podręcznej               | Wskazuje procent żądań, które były obsługiwane bezpośrednio z pamięci podręcznej do żądacza. |
| Trafienia w pamięci podręcznej                | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla cname krawędzi, które powoduje trafienie pamięci podręcznej (na przykład TCP_EXPIRED_HIT, TCP_HIT lub TCP_PARTIAL_HIT). Trafienie pamięci podręcznej występuje, gdy zostanie znaleziona buforowana wersja żądanej zawartości. |
| Przesyłane dane (MB)     | Wskazuje całkowitą ilość danych przesyłanych (MB) z serwerów brzegowych do klientów HTTP (przeglądarek internetowych) dla cname krawędzi. Ilość przesyłanych danych jest obliczana przez dodanie nagłówków odpowiedzi HTTP do treści odpowiedzi. W rezultacie ilość danych przesłanych dla każdego zasobu jest większa niż jego rzeczywisty rozmiar pliku. |
| Opis               | Identyfikuje cname krawędzi przez jego nazwa hosta |
| Trafienia                      | Wskazuje całkowitą liczbę żądań do cname krawędzi |
| Chybienia                    | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla cname krawędzi, która powoduje pominięcie pamięci podręcznej (na przykład TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS lub TCP_MISS). Brak pamięci podręcznej występuje, gdy żądana zawartość nie została buforowana na serwerze brzegowym, który honorował żądanie. | 
| Brak pamięci podręcznej                  | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla cname krawędzi, który powoduje CONFIG_NOCACHE kod stanu pamięci podręcznej.  |
| Inne                     | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla cname krawędzi wskazane, że wyniki w kod stanu HTTP, który mieści się poza zakresem 2xx - 5xx. |
| Platforma                  | Wskazuje platformę, która obsługuje ruch CNAME krawędzi. |
| Nieprzypisane               | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla cname krawędzi, dla których kod stanu pamięci podręcznej lub informacje o kodzie stanu HTTP nie zostały zarejestrowane.  |
| Nie do zakaszu nie do zakaszu               | Wskazuje całkowitą liczbę żądań lub danych przesłanych (MB) dla cname krawędzi, co powoduje kod stanu pamięci podręcznej UNCACHEABLE.  |


## <a name="considerations"></a>Zagadnienia do rozważenia
Raporty mogą być generowane tylko w ciągu ostatnich 18 miesięcy.

