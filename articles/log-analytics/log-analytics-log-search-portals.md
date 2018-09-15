---
title: Wyświetlanie i analizowanie danych w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano portale, które można używać w przeszukiwania dzienników w usłudze Azure Log Analytics można tworzyć i edytować.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: 996502ffe5a31fcfa1b73dab9a041c336c4ea98f
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45602635"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Wyświetlanie i analizowanie danych w usłudze Log Analytics
Dostępne są dwie opcje dostępne w witrynie Azure portal do analizowania danych przechowywanych w usłudze Log analytics oraz do tworzenia zapytań dotyczących analizy ad-hoc. Zapytania, które możesz utworzyć przy użyciu tych portali może służyć do innych funkcji, takich jak alerty i pulpity nawigacyjne.

## <a name="log-analytics-page"></a>Strona analizy dziennika
Otwórz stronę usługi Log Analytics z **dzienniki** w menu usługi Log Analytics. Jest to nowe środowisko pracy z danymi dzienników i tworzenie zapytań. Możesz zapoznaj się z wprowadzeniem do tego portalu i sprawdzić jego funkcje na [wprowadzenie stronie Log Analytics w witrynie Azure portal](query-language/get-started-analytics-portal.md).

Na stronie usługi Log Analytics udostępnia następujące ulepszenia w porównaniu z [wyszukiwanie w dzienniku](#log-search) środowiska.

* Wiele kart — Utwórz osobne karty, aby pracować z wieloma zapytaniami.
* Rozbudowane wizualizacje — różne opcje wykresów.
* Ulepszona funkcja Intellisense i język automatycznego uzupełniania.
* Wyróżnianie składni — poprawia czytelność zapytań. 
* Eksplorator zapytań — dostęp zapisane zapytania i funkcje.
* Schemat wyświetlić — Przejrzyj strukturę danych, aby pomóc w pisaniu zapytań.
* Udostępnianie — tworzenie łączy do zapytań lub zapytań numeru pin do dowolnego udostępnionego pulpitu nawigacyjnego platformy Azure.
* Blokada Smart analizy — identyfikuje skoków wykresów i szybka analiza przyczyn.
* Wybór kolumn — sortowanie i grupowanie kolumn w wynikach zapytania.

> [!NOTE]
> Strona usługi Log Analytics ma taką samą funkcjonalność jak portalu analizy zaawansowanej, czyli narzędzie zewnętrzne poza witryny Azure portal. Portalu analizy zaawansowanej jest nadal dostępna, ale linki i inne odwołania do niego w witrynie Azure portal są zastępowane przy użyciu tej nowej strony.

![Portal analizy zaawansowanej](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


### <a name="firewall-requirements"></a>Wymagania dotyczące zapory
Przeglądarka wymaga dostępu do następujących adresów, aby uzyskać dostęp do strony usługi Log Analytics i portalu analizy zaawansowanej.  Jeśli przeglądarka jest dostęp do witryny Azure portal za pośrednictwem zapory, należy włączyć dostęp do tych adresów.

| Identyfikator URI | Adres IP | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamiczny | 80,443 |
| api.loganalytics.io    | Dynamiczny | 80,443 |
| docs.loganalytics.io   | Dynamiczny | 80,443 |


## <a name="log-search-classic"></a>Wyszukiwanie w dzienniku (wersja klasyczna)
Otwórz stronę wyszukiwania dziennika z **dzienniki (wersja klasyczna)** w menu usługi Log Analytics lub z **usługi Log Analytics** w menu usługi Azure Monitor. Jest to odpowiednie do analizowania danych dziennika przy użyciu podstawowych zapytań. Oferuje ona wiele funkcji edytowania zapytania bez konieczności pełnej znajomości języka zapytań.  Może uzyskać podsumowanie informacji o tych funkcji w [tworzenia wyszukiwań w dziennikach w usłudze Azure Log Analytics przy użyciu wyszukiwania dziennika](log-analytics-log-search-log-search-portal.md). 


![Strona wyszukiwania dziennika](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>Kolejne kroki

- Przeprowadzenie [samouczku, korzystając z wyszukiwania w dziennikach](log-analytics-tutorial-viewdata.md) dowiesz się, jak tworzyć zapytania przy użyciu języka zapytań
- Przeprowadzenie [lekcja przy użyciu portalu analizy zaawansowanej](query-language/get-started-analytics-portal.md) zapewniającą taki sam sposób jak strona usługi Log Analytics.

