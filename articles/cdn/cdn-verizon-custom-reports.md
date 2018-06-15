---
title: Raporty niestandardowe from Verizon | Dokumentacja firmy Microsoft
description: 'Możesz wyświetlić wzorców użycia sieci CDN za pomocą następujących raportów: przepustowości, dane przesyłane, trafień, stany pamięci podręcznej, Stosunek trafień w pamięci podręcznej, przesłanych danych IPV4 i IPV6.'
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: v-deasim
ms.openlocfilehash: f09195dc07a96ebcca7f7a9e4bcf521fae13630c
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2017
ms.locfileid: "26343892"
---
# <a name="custom-reports-from-verizon"></a>Raporty niestandardowe from Verizon

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Za pomocą raportów niestandardowych Verizon za pośrednictwem portalu zarządzania dla profilów Verizon, można określić typu danych, które mają być zbierane dla raportów CNAME krawędzi.


## <a name="accessing-verizon-custom-reports"></a>Uzyskiwanie dostępu do raportów niestandardowych Verizon
1. Blok profilu CDN, kliknij **Zarządzaj** przycisku.
   
    ![Przycisk Zarządzaj profil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **Analytics** , a następnie umieść kursor nad **raporty niestandardowe** wysuwane okno. Kliknij przycisk **krawędzi CNAME**.
   
    ![Portal zarządzania w sieci CDN — menu Raporty niestandardowe](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Raport niestandardowy CNAME krawędzi
Raport niestandardowy CNAME krawędzi trafień i statystyki transferu danych przewiduje CNAME krawędzi, na których włączono rejestrowanie raportu niestandardowego. Krawędź CNAME składają się z nazwy hostów punktu końcowego usługi Azure CDN i wszystkie skojarzone domeny niestandardowej nazwy hostów. 

Rejestrowanie danych raportu niestandardowego rozpoczyna jednej godziny po włączeniu możliwości raportowania niestandardowych CNAME krawędzi. Wygenerowanie raportu krawędzi CNAME dla określonej platformy lub dla wszystkich platform, mogą wyświetlać dane raportu. Pokrycie dla tego raportu jest ograniczona do rekordów CNAME krawędzi, dla których zebrano dane raportu niestandardowego w określonym przedziale czasu. Krawędź raportu CNAME składa się z wykres i tabelę danych dla górnej krawędzi 10 rekordów CNAME zgodnie z Metryka zdefiniowana w opcji metryki. 

Generowanie raportu niestandardowego, definiując następujące opcje:

- Metryki: Obsługiwane są następujące opcje:

   - Liczba trafień: Wskazuje, całkowita liczba żądań, które są kierowane do CNAME krawędzi, dla którego włączono niestandardowe możliwości raportowania. Ta metryka nie zawiera kod stanu zwrócona do klienta.

   - Transferowanych danych: Określa łączną ilość danych przesyłanych z serwerów krawędzi do klientów protokołu HTTP (na przykład przeglądarki sieci web) dla żądań, które są kierowane do CNAME krawędzi, dla którego włączono niestandardowe możliwości raportowania. Ilość danych przesyłanych jest obliczana przez dodawanie nagłówków odpowiedzi HTTP do treści odpowiedzi. W związku z tym ilość danych przesyłanych za każdy zasób jest większa niż jego bieżący rozmiar pliku.

- Grupowanie: Określa typ statystyk, które zostały przedstawione poniżej na wykresie słupkowym. Obsługiwane są następujące opcje:

   - Kody odpowiedzi HTTP: Organizuje statystyki przez kod odpowiedzi HTTP (na przykład 200, 403, itp.) zwracana do klienta. 

   - Stan pamięci podręcznej: Organizuje statystyki według stanu pamięci podręcznej.


Aby ustawić zakres dat dla raportu, możesz wybrać zakres dat wstępnie zdefiniowane, takich jak **dzisiaj** lub **ten tydzień**, z listy rozwijanej lub możesz wybrać **niestandardowe** i ręcznie wprowadź zakres dat, klikając ikony kalendarza. 

Po wybraniu zakresu dat, kliknij przycisk **Przejdź** do wygenerowania raportu.

Można eksportować dane w formacie programu Excel, klikając symbol programu Excel z prawej strony **Przejdź** przycisku.

![Raport CNAME](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Pola niestandardowe raportu CNAME krawędzi

| Pole                     | Opis   |
|---------------------------|---------------|
| 2xx                       | Wskazuje całkowitą liczbę żądań lub transferu danych (MB) krawędzi CNAME, który daje 2xx kod stanu HTTP (na przykład 200 OK). |
| 3xx                       | Wskazuje całkowitą liczbę żądań lub transferu danych (MB) krawędzi CNAME, który daje 3xx kod stanu HTTP (na przykład znaleźć 302 lub 304 nie jest modyfikowany. |
| 4xx                       | Wskazuje całkowitą liczbę żądań lub transferu danych (MB) krawędzi CNAME, który daje 4xx kod stanu HTTP (na przykład 400 Nieprawidłowe żądanie, 403 Forbidden lub 404 Nie znaleziono). |
| 5xx                       | Wskazuje całkowitą liczbę żądań lub transferu danych (MB) Microsoft Edge CNAME, który daje kod stanu HTTP 5xx (na przykład, 500 Wewnętrzny błąd serwera lub bramy zły 502). |
| Procent trafień w pamięci podręcznej               | Wskazuje procent buforowalnej żądań, które były przekazywane bezpośrednio z pamięci podręcznej do zleceniodawcy. |
| Trafienia w pamięci podręcznej                | Wskazuje całkowitą liczbę żądań lub transferu danych (MB) krawędzi CNAME, który daje w trafienie pamięci podręcznej (na przykład TCP_EXPIRED_HIT, TCP_HIT lub TCP_PARTIAL_HIT). Trafienie w pamięci podręcznej występuje, gdy zostanie znaleziony wersja buforowana żądanej zawartości. |
| Przesyłane dane (MB)     | Wskazuje łączną ilość danych przesyłanych (MB) z serwerów krawędzi do klientów HTTP (przeglądarki sieci web) Microsoft Edge CNAME. Ilość danych przesyłanych jest obliczana przez dodawanie nagłówków odpowiedzi HTTP do treści odpowiedzi. W związku z tym ilość danych przesyłanych za każdy zasób jest większa niż jego bieżący rozmiar pliku. |
| Opis               | Identyfikuje krawędzi CNAME według jego nazwy hosta |
| Trafienia                      | Całkowita liczba żądań do krawędzi CNAME wskazuje |
| Chybienia                    | Wskazuje całkowitą liczbę żądań lub transferu danych (MB) krawędzi CNAME, który powoduje Chybienie pamięci podręcznej, (na przykład TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS lub TCP_MISS). Chybienia pamięci podręcznej występuje, gdy żądana zawartość nie jest buforowana na serwerze krawędzi, który honorowane żądania. | 
| Brak pamięci podręcznej                  | Wskazuje całkowitą liczbę żądań lub transferu danych (MB) krawędzi CNAME, który daje kod stanu CONFIG_NOCACHE pamięci podręcznej.  |
| Inne                     | Wskazuje całkowitą liczbę żądań lub dane wskazane przekazanych (MB) Edge CNAME powoduje kod stanu HTTP, która wykracza poza 2xx — 5xx zakresu. |
| Platforma                  | Wskazuje platforma, która obsługuje ruch CNAME krawędzi. |
| Nieprzypisane               | Wskazuje całkowitą liczbę żądań lub transferu danych (MB) dla CNAME krawędzi, dla których kod stanu pamięci podręcznej lub kod stanu HTTP nie zostało zarejestrowane informacje.  |
| Uncacheable               | Wskazuje całkowitą liczbę żądań lub transferu danych (MB) krawędzi CNAME, który daje w pamięci podręcznej UNCACHEABLE kod stanu.  |


## <a name="considerations"></a>Zagadnienia do rozważenia
Raporty mogą być generowane tylko w ciągu ostatnich 18 miesięcy.

