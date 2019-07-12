---
title: Raporty niestandardowe z usługi Verizon | Dokumentacja firmy Microsoft
description: 'Aby wyświetlić wzorców użycia dla sieci CDN, za pomocą następujących raportów: Przepustowość, przetransferowane dane, trafienia, stany pamięci podręcznej, współczynnik, przesłanych danych protokołów IPV4/IPV6 trafień w pamięci podręcznej.'
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: magattus
ms.openlocfilehash: 15f17ac6556c4ff731372dc7f738d0f58bdc3e31
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593287"
---
# <a name="custom-reports-from-verizon"></a>Raporty niestandardowe z usługi Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Za pomocą raportów niestandardowych Verizon za pośrednictwem portalu zarządzania dla profilów Verizon, można zdefiniować typ danych, które mają być zbierane dla raportów rekordy CNAME krawędzi.


## <a name="accessing-verizon-custom-reports"></a>Uzyskiwanie dostępu do raportów niestandardowych Verizon
1. Z poziomu bloku profil CDN kliknij **Zarządzaj** przycisku.
   
    ![Przycisk Zarządzaj w profilu CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **Analytics** kartę, a następnie umieść kursor nad **raporty niestandardowe** okno wysuwane. Kliknij przycisk **krawędzi rekordy CNAME**.
   
    ![Raporty niestandardowe portalu zarządzania usługi CDN — menu](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Niestandardowy raport rekordy CNAME usługi Edge
Niestandardowy raport rekordy CNAME krawędzi trafień i statystyki dane przesyłane przewiduje krawędzi rekordów CNAME, na których włączono rejestrowanie niestandardowy raport. Rekordy CNAME krawędzi składają się z nazwy hostów punktu końcowego usługi Azure CDN i nazwy hostów dowolnym skojarzone domeny niestandardowej. 

Rejestrowanie danych raportu niestandardowego rozpoczyna się jedną godzinę, po włączeniu CNAME krawędzi niestandardowych funkcji raportowania. Aby wyświetlić dane raportu, generowania raportu krawędzi rekordy CNAME dla określonej platformy, lub dla wszystkich platform. Pokrycie dla tego raportu jest ograniczona do krawędzi rekordów CNAME, dla których raport niestandardowy dane zostały zebrane w określonym przedziale czasu. Krawędź rekordy CNAME raport składa się z wykres i tabelę danych dla górnej krawędzi 10 rekordów CNAME zgodnie z Metryka zdefiniowana w opcji metryki. 

Generuje niestandardowy raport, definiując następujące opcje:

- Metryki: Obsługiwane są następujące opcje:

   - Trafienia: Wskazuje, całkowita liczba żądań, które są kierowane do krawędzi rekordu CNAME, na których jest włączona niestandardowych funkcji raportowania. Ta metryka nie ma kod stanu zwrócony do klienta.

   - Data Transferred: Wskazuje całkowitą ilość danych przesyłanych z serwerów brzegowych klientom HTTP (na przykład przeglądarki sieci web) dla żądań, które są kierowane do krawędzi rekordu CNAME, na których jest włączona niestandardowych funkcji raportowania. Ilość przesyłanych danych jest obliczany przez dodanie nagłówków odpowiedzi HTTP do treści odpowiedzi. W rezultacie ilość danych przesyłanych dla każdego zasobu jest większa niż jego bieżący rozmiar pliku.

- Grupowania: Określa typ statystyk, które zostały wymienione poniżej wykres słupkowy. Obsługiwane są następujące opcje:

   - HTTP Response Codes: Organizuje statystyki według kodu odpowiedzi HTTP (na przykład 200, 403, itp.) zwracana do klienta. 

   - Stan pamięci podręcznej: Organizuje statystyki według stanu pamięci podręcznej.


Aby ustawić zakres dat dla raportu, należy można albo wybierz wstępnie zdefiniowany zakres dat, takich jak **już dziś** lub **ten tydzień**, z listy rozwijanej lub możesz wybrać **niestandardowe** i ręcznie wprowadź zakres dat, klikając ikony kalendarza. 

Po wybraniu w zakresie dat., kliknij przycisk **Przejdź** podczas generowania raportu.

Możesz wyeksportować dane w formacie programu Excel, klikając symbol programu Excel po prawej stronie **Przejdź** przycisku.

![Rekordy CNAME raportu](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Pola raportu niestandardowe rekordy CNAME usługi Edge

| Pole                     | Opis   |
|---------------------------|---------------|
| 2xx                       | Wskazuje całkowitą liczbę żądań lub przesłanych danych (MB) dla przeglądarki edge CNAME, który skutkuje kod stanu HTTP 2xx (na przykład 200 OK). |
| 3xx                       | Wskazuje całkowitą liczbę żądań lub przesłanych danych (MB) dla przeglądarki edge CNAME, który skutkuje kod stanu HTTP 3xx (np. Znaleziono 302 lub 304 nie jest modyfikowany. |
| 4xx                       | Wskazuje całkowitą liczbę żądań lub przesłanych danych (MB) dla przeglądarki edge CNAME, który skutkuje kod stanu HTTP 4xx (na przykład odpowiedź 400 Niewłaściwe żądanie, 403 zabronione lub 404 Nie znaleziono). |
| 5xx                       | Wskazuje całkowitą liczbę żądań lub transferu danych (MB) Microsoft Edge CNAME, który daje kod stanu HTTP 5xx (na przykład, 500 Wewnętrzny błąd serwera lub bramy zły 502). |
| Procent liczby trafień pamięci podręcznej               | Wskazuje procent podlega buforowaniu, żądań, które zostały obsłużone bezpośrednio z pamięci podręcznej do zleceniodawcy. |
| Trafienia w pamięci podręcznej                | Wskazuje całkowitą liczbę żądań lub przesłanych danych (MB) dla przeglądarki edge CNAME, który skutkuje liczby trafień pamięci podręcznej (na przykład TCP_EXPIRED_HIT, TCP_HIT lub TCP_PARTIAL_HIT). Trafienie w pamięci podręcznej występuje, gdy zostanie znaleziony w pamięci podręcznej wersji żądanej zawartości. |
| Dane przesyłane (MB)     | Wskazuje łączną ilość danych przesyłanych (MB) z serwerów krawędzi do klientów HTTP (przeglądarki sieci web) Microsoft Edge CNAME. Ilość przesyłanych danych jest obliczany przez dodanie nagłówków odpowiedzi HTTP do treści odpowiedzi. W rezultacie ilość danych przesyłanych dla każdego zasobu jest większa niż jego bieżący rozmiar pliku. |
| Opis               | Identyfikuje krawędź CNAME według jego nazwy hosta |
| Trafienia                      | Wskazuje, całkowita liczba żądań na krawędzi rekordu CNAME |
| Chybienia                    | Wskazuje całkowitą liczbę żądań lub przesłanych danych (MB) dla przeglądarki edge CNAME, który skutkuje to Chybienie pamięci podręcznej (na przykład TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS lub TCP_MISS). Trafienia pamięci podręcznej występuje, gdy żądana zawartość nie jest buforowana na serwerze granicznym, który honorowane żądania. | 
| Brak pamięci podręcznej                  | Wskazuje całkowitą liczbę żądań lub przesłanych danych (MB) dla przeglądarki edge CNAME, powstałego CONFIG_NOCACHE kod stanu pamięci podręcznej.  |
| Inne                     | Wskazuje, całkowita liczba żądań lub danych wskazany przeniesionych (MB) dla przeglądarki edge CNAME powodujący kod stanu HTTP, która wykracza poza 2xx — zakresu 5xx. |
| Platforma                  | Wskazuje platforma, która obsługuje ruch CNAME krawędzi. |
| Nieprzypisane               | Wskazuje całkowita liczba żądań lub dane przesłane (MB) dla rekordu CNAME krawędzi, dla których kod stanu pamięci podręcznej lub kod stanu HTTP nie zostało zarejestrowane informacje.  |
| Uncacheable               | Wskazuje, całkowita liczba żądań lub przesłanych danych (MB) dla przeglądarki edge CNAME powodujący kod stanu UNCACHEABLE pamięci podręcznej.  |


## <a name="considerations"></a>Zagadnienia do rozważenia
Raporty mogą być generowane tylko w ciągu ostatnich 18 miesięcy.

