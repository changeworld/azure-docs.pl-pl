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
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: bwren
ms.openlocfilehash: 9a5472a6dfc944eb793e863704897c92b1a7572e
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183356"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Wyświetlanie i analizowanie danych w usłudze Log Analytics
Dostępne są dwie opcje dostępne w witrynie Azure portal do analizowania danych przechowywanych w usłudze Log analytics oraz do tworzenia zapytań dotyczących analizy ad-hoc. Zapytania, które możesz utworzyć przy użyciu tych portali może służyć do innych funkcji, takich jak alerty i pulpity nawigacyjne.

## <a name="log-analytics-page"></a>Strona analizy dziennika
Otwórz stronę usługi Log Analytics z **dzienniki** w menu usługi Log Analytics. Jest to nowe środowisko pracy z danymi dzienników i tworzenie zapytań. Możesz zapoznaj się z wprowadzeniem do tego portalu i sprawdzić jego funkcje na [wprowadzenie stronie Log Analytics w witrynie Azure portal](../../azure-monitor/log-query/get-started-portal.md).

Na stronie usługi Log Analytics udostępnia następujące ulepszenia w porównaniu z [wyszukiwanie w dzienniku (wersja klasyczna)](#log-search-classic) środowiska.

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

![Portal analizy zaawansowanej](media/portals/advanced-analytics-portal.png)

### <a name="resource-logs"></a>Dzienniki zasobów
Nowe środowisko usługi Log Analytics jest zintegrowana z różnymi zasobami platformy Azure, takie jak maszyny wirtualne. Oznacza to, że można otworzyć stronę usługi Log Analytics, bezpośrednio za pomocą menu monitorowania zasobów bez przełączania się do usługi Azure Monitor lub usługi Log Analytics i utraty kontekstu zasobu. **Dzienniki** nie jeszcze została włączona dla wszystkich zasobów platformy Azure, ale zaczną się pojawiać w menu portalu dla różnych zasobów typów.

Otwieranie usługi Log Analytics z określonego zasobu, jest objęty zakresem automatycznie do rejestrowania rekordów tylko do tego zasobu.   Jeśli chcesz napisać zapytanie, które zawiera inne rekordy, czy należy ją otworzyć z menu usługi Log Analytics lub Azure Monitor.

Następujące opcje nie są jeszcze dostępne za pośrednictwem widoku zasobów usługi Log Analytics:

- Zapisz
- Ustawianie alertu
- Eksplorator zapytań
- Przełączanie do innego obszaru roboczego/zasobu (aktualnie nie zaplanowana)


### <a name="firewall-requirements"></a>Wymagania dotyczące zapory
Przeglądarka wymaga dostępu do następujących adresów, aby uzyskać dostęp do strony usługi Log Analytics i portalu analizy zaawansowanej.  Jeśli przeglądarka jest dostęp do witryny Azure portal za pośrednictwem zapory, należy włączyć dostęp do tych adresów.

| Identyfikator URI | Adres IP | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamiczny | 80,443 |
| api.loganalytics.io    | Dynamiczny | 80,443 |
| docs.loganalytics.io   | Dynamiczny | 80,443 |


## <a name="log-search-classic"></a>Wyszukiwanie w dzienniku (wersja klasyczna)
Otwórz stronę wyszukiwania dziennika z **dzienniki (wersja klasyczna)** w menu usługi Log Analytics lub z **usługi Log Analytics** w menu usługi Azure Monitor. Jest to strona programu klasycznego używane do pracy za pomocą zapytań usługi Log Analytics, które nie ma dodatkowych funkcji [strony Log Analytics](#log-analytics-page) wymienionych powyżej.



![Strona wyszukiwania dziennika](media/portals/log-search-portal.png)


## <a name="next-steps"></a>Kolejne kroki

- Przeprowadzenie [samouczku, korzystając z wyszukiwania w dziennikach](../../azure-monitor/learn/tutorial-viewdata.md) dowiesz się, jak tworzyć zapytania przy użyciu języka zapytań
- Przeprowadzenie [lekcja przy użyciu portalu analizy zaawansowanej](../../azure-monitor/log-query/get-started-portal.md) zapewniającą taki sam sposób jak strona usługi Log Analytics.

