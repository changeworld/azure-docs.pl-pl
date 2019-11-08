---
title: Informacje o wersji dla sprzedającego
description: Zawiera informacje o zmianach wprowadzonych w funkcji sprzedającego szczegółowe dane.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: 4f7e461241793b27d838c04311509f43932df291
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808954"
---
# <a name="seller-insights-release-notes"></a>Informacje o wersji dla sprzedającego 

(Data wydania: 1 marca 2019)

Ten artykuł zawiera informacje o zmianach w funkcji sprzedającego Insights w [Portal Cloud partner](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Najważniejsze informacje o wersji 1 marca 2019

* *Trend klienta* został dodany do podsumowania
* *Pięć najważniejszych klientów* z podsumowaniem odzwierciedla wszystkie subskrypcje platformy Azure, dla których klient ma
* Trend *znormalizowanego użycia & trendu aktywnych zamówień* na podstawie podsumowania w *kolejności miesięcznej*
* Zaktualizowany *raport uzgadniania wypłaty*
* *Pięć najważniejszych klientów* z wypłatami odzwierciedla wszystkie subskrypcje platformy Azure, dla których klient ma
* Zaktualizowano *raport użycia* o identyfikatorze klienta
* Status *klienta* w przypadku zamówień & użycie odzwierciedla wszystkie subskrypcje platformy Azure, dla których klient ma


(Data wydania: 28 lipca 2018)

## <a name="release-highlights-for-july-28-2018"></a>Najważniejsze wersje dla 28 lipca 2018


-   *Szacowane ceny* zapewniają koszty klientów z uwzględnieniem konsekwencji konwersji walut.
-   *Prognozowane wypłaty* przedstawiają wcześniejszy wgląd w potencjalne wypłaty.
-  *Identyfikatory odwołań użycia* zapewniają wierność danych między użyciem klienta i zamówieniami z wypłatami
-   *Użycie na dziennym ziarnie* zapewnia większy stopień szczegółowości i lepszy wgląd w użycie klientów.


### <a name="changes-to-data-structure-and-taxonomy"></a>Zmiany struktury i taksonomii danych

Poniższa tabela zawiera listę metryk, które zostały dodane lub znacząco zmienione w tej wersji. 

| **Nowy termin**                   |    **Definicja**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Cena (DW)                     | Cena jednostki użycia dla danej jednostki SKU (w walucie klienta).       |
| Szacowana opłata rozszerzona (DW) | Szacowana rozszerzona opłata za liczbę jednostek użytkowania dla danej jednostki SKU (w walucie klienta). Ta wartość nie może być dokładna ze względu na błędy zaokrągleń lub obcinania.   |
| Waluta wydawcy (komputer)        | Waluta wypłaty przez wydawcę.                               |
| Szacowana cena (komputer)           | Szacowana cena jednostki użycia dla danej jednostki SKU opartej na konwersji wymiany walut na użycie jest obliczana (w walucie wydawcy). Ta wartość nie może być dokładna ze względu na błędy zaokrągleń lub obcinania.   |
| Szacowana opłata rozszerzona (komputer) | Szacowana rozszerzona opłata za liczbę jednostek użytkowania dla danej jednostki SKU w oparciu o konwersję wymiany na użycie jest obliczana (w walucie wydawcy). Ta wartość nie może być dokładna ze względu na błędy zaokrągleń lub obcinania. |
| Szacowana wypłata (komputer)          | Szacowana płatność za liczbę jednostek użytkowania dla danej jednostki SKU na podstawie konwersji wymiany walut w dniu, w którym obliczono użycie (w walucie wydawcy). Ta wartość nie może być dokładna ze względu na błędy zaokrągleń lub obcinania.   |
| Informacje o użyciu                | Identyfikator dla co najmniej jednego dnia użycia klienta dla danej jednostki SKU skojarzonej z wpisem w raporcie wypłaty. |
|  |  |
