---
title: Informacje o publikacji statystyk sprzedawcy
description: Zawiera informacje o zmianach w funkcji Statystyka sprzedawcy.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: dsindona
ms.openlocfilehash: 1b88a8353c1cb9f97f18d720171f0827d7d85254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285405"
---
# <a name="seller-insights-release-notes"></a>Informacje o wersji Insights dla sprzedawcy 

(Data wydania: 1 marca 2019 r.)

Ten artykuł zawiera informacje o zmianach w funkcji Statystyka sprzedawcy w [portalu cloud partnerów](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Najważniejsze informacje o wersji na 1 marca 2019 r.

* *Trend klienta* dodany do podsumowania
* *Pięciu najlepszych klientów* w podsumowaniu odzwierciedla wszystkie subskrypcje platformy Azure, które klient
* *Trend znormalizowanego wykorzystania & trendu aktywnych zamówień* w podsumowaniu przeniesionym w obszarze *Zamówienia miesięczne w skrócie*
* Zaktualizowano *raport uzgodnienia wypłat*
* *Pięciu najlepszych klientów* w sprawie wypłat odzwierciedla wszystkie subskrypcje platformy Azure, które klient
* *Raport użycia* zaktualizowany o identyfikator klienta
* *Kadencja klienta* w zamówieniach & użytkowania odzwierciedla wszystkie subskrypcje platformy Azure, które klient ma


(Data wydania: 28 lipca 2018 r.)

## <a name="release-highlights-for-july-28-2018"></a>Najważniejsze informacje o wersji na 28 lipca 2018 r.


-   *Szacowane ceny* zapewniają wgląd w opłaty klientów z konsekwencjami przeliczania walut.
-   *Prognozowane wypłaty* zapewniają wcześniejszy wgląd w potencjalne wypłaty.
-  *Identyfikatory odwołań użycia* zapewniają wierność danych między użyciem klienta a zamówieniami z wypłatami
-   *Użycie w codziennym ziarno* zapewnia większą szczegółowość i lepszy wgląd w wykorzystanie klienta.


### <a name="changes-to-data-structure-and-taxonomy"></a>Zmiany w strukturze danych i taksonomii

W poniższej tabeli wymieniono metryki, które zostały dodane lub znacznie zmienione w tej wersji. 

| **Nowy termin**                   |    **Definicja**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Cena (CC)                     | Cena jednostki użycia dla danej jednostki SKU (w walucie odbiorcy).       |
| Szacowana opłata rozszerzona (CC) | Szacowana opłata rozszerzona za ilość jednostek użycia dla danej jednostki SKU (w walucie odbiorcy). Ta wartość może nie być dokładna z powodu błędów zaokrąglania lub obcinania.   |
| Waluta wydawcy (PC)        | Waluta preferowana przez wydawcę do wypłaty.                               |
| Szacowana cena (PC)           | Szacowana cena jednostki użycia dla danej jednostki SKU na podstawie konwersji kursowej w dniu użycia jest obliczana (w walucie wydawcy). Ta wartość może nie być dokładna z powodu błędów zaokrąglania lub obcinania.   |
| Szacowane obciążenie rozszerzone (PC) | Szacowana opłata rozszerzona za ilość jednostek użycia dla danej jednostki SKU na podstawie konwersji kursowej w dniu użycia jest obliczana (w walucie wydawcy). Ta wartość może nie być dokładna z powodu błędów zaokrąglania lub obcinania. |
| Szacowana wypłata (PC)          | Szacowana płatność za ilość jednostek użycia dla danej jednostki SKU na podstawie konwersji kursowej w dniu obliczania użycia (w walucie wydawcy). Ta wartość może nie być dokładna z powodu błędów zaokrąglania lub obcinania.   |
| Odwołanie do użycia                | Identyfikator jednego lub więcej dni użycia klienta dla danej jednostki SKU skojarzonej z zapisem w raporcie wypłat. |
|  |  |
