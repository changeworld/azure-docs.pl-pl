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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ec3784a91f8aeb7f0fedd13c9ab86a844832a578
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810254"
---
<a name="seller-insights-release-notes"></a>Sprzedawcy szczegółowe informacje o wersji 
===============================
(Data wydania: 28 lipca 2018 r.)

Ten artykuł zawiera informacje na temat zmian z tą funkcją sprzedawcy wgląd w [portalu Cloud Partner](https://cloudpartner.azure.com/#insights).


<a name="release-highlights"></a>Najważniejsze informacje o wersji
------------------

-   *Szacowane ceny* udostępnić widok opłat klienta waluty skutki konwersji.
-   *Prognozowane wypłaty* podać wcześniej wgląd w potencjalne wypłaty.
-  *Użycie odwołania identyfikatory* udostępnianie danych wierność między użycia przez klientów i zamówień wypłaty
-   *Użycie dokładnością* zapewnia większą szczegółowość i lepszy wgląd w użycie klienta.


<a name="changes-to-data-structure-and-taxonomy"></a>Zmiany struktury danych i taksonomii
--------------------------------------

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
