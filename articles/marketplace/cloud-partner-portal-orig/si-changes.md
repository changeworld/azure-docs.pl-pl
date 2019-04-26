---
title: Sprzedawcy szczegółowe informacje o wersji | Dokumentacja firmy Microsoft
description: Zawiera informacje na temat zmian sprzedawcy wgląd w szczegółowe dane.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pbutlerm
ms.openlocfilehash: 7df96d53b02e80283f263901dc7e26ed4edb947d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311006"
---
# <a name="seller-insights-release-notes"></a>Sprzedawcy szczegółowe informacje o wersji 

(Data wydania: 1 marca 2019 r.)

Ten artykuł zawiera informacje na temat zmian z tą funkcją sprzedawcy wgląd w [portalu Cloud Partner](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Najważniejsze informacje o wersji dla 1 marca 2019 r.

* *Trend klienta* dodawane do podsumowania
* *Pierwszych pięć klientów* na podsumowanie odzwierciedlać wszystkich subskrypcji platformy Azure przez klienta
* *Znormalizowane trendów użycia i aktywne Trend zamówienia* na podsumowanie została przeniesiona do obszaru *miesięczne zamówienia na pierwszy rzut oka*
* *Informacje dotyczące wypłat raport uzgadniania* zaktualizowane
* *Pierwszych pięć klientów* na informacje dotyczące wypłat odzwierciedlać wszystkich subskrypcji platformy Azure przez klienta
* *Raport użycia* aktualizowane przy użyciu Identyfikatora klienta
* *Klientów na stanowisku* na zamówień i użycie odzwierciedla wszystkich subskrypcji platformy Azure przez klienta


(Data wydania: 28 lipca 2018 r.)

## <a name="release-highlights-for-july-28-2018"></a>Najważniejsze informacje o wersji dla 28 lipca 2018 r.


-   *Szacowane ceny* udostępnić widok opłat klienta waluty skutki konwersji.
-   *Prognozowane wypłaty* podać wcześniej wgląd w potencjalne wypłaty.
-  *Użycie odwołania identyfikatory* udostępnianie danych wierność między użycia przez klientów i zamówień wypłaty
-   *Użycie dokładnością* zapewnia większą szczegółowość i lepszy wgląd w użycie klienta.


### <a name="changes-to-data-structure-and-taxonomy"></a>Zmiany struktury danych i taksonomii

Poniższa tabela zawiera listę metryk, które zostały dodane lub znaczne zmiany w tej wersji. 

| **Nowy termin**                   |    **Definicja**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Cena (DW)                     | Cena jednostki użycia dla danej jednostki SKU (w walucie klienta).       |
| Szacowana opłata rozszerzone (DW) | Szacowany rozszerzonej opłaty za ilość jednostek użycia dla danej jednostki SKU (w walucie klienta). Ta wartość nie może być dokładne z powodu błędów zaokrąglania lub obcięcie.   |
| Wydawca waluty (PC)        | Preferowane przez wydawcę, aby uzyskać informacje dotyczące wypłat Waluta.                               |
| Szacowana cena (PC)           | Szacowana cena jednostki użycia dla danej jednostki SKU opartej na konwersji walutowych użycia Data jest obliczana (w walucie wydawcy). Ta wartość nie może być dokładne z powodu błędów zaokrąglania lub obcięcie.   |
| Szacowana opłata rozszerzone (PC) | Szacowany rozszerzonej opłaty za ilość jednostek użycia dla danej jednostki SKU opartej na konwersji walutowych użycia Data jest obliczana (w walucie wydawcy). Ta wartość nie może być dokładne z powodu błędów zaokrąglania lub obcięcie. |
| Informacje dotyczące wypłat szacowany (PC)          | Szacowany płatności za ilość jednostek użycia dla danej jednostki SKU na podstawie walutowych konwersja na datę, użycie jest obliczane (w walucie wydawcy). Ta wartość nie może być dokładne z powodu błędów zaokrąglania lub obcięcie.   |
| Odwołanie do użycia                | Identyfikator dla jednego lub więcej dni użycia przez klientów dla danej jednostki SKU, skojarzone z wpisem w raporcie informacje dotyczące wypłat. |
|  |  |
