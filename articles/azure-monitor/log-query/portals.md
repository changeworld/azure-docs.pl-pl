---
title: Wyświetlanie i analizowanie danych dzienników w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano używanie usługi Log Analytics w witrynie Azure portal do tworzenia i edytowania zapytań dzienników w usłudze Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/22/2018
ms.author: bwren
ms.openlocfilehash: 9567f8a6b581d7c246ebaa8eb8d72ad201bf2641
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990415"
---
# <a name="viewing-and-analyzing-log-data-in-azure-monitor"></a>Wyświetlanie i analizowanie danych dzienników w usłudze Azure Monitor
Log Analytics jest podstawowym umożliwiający pracę z danymi dzienników i tworzenie zapytań w usłudze Azure Monitor. Otwórz program Log Analytics z **dzienniki** w **usługi Azure Monitor** menu. Możesz zapoznaj się z wprowadzeniem do tego portalu i sprawdzić jego funkcje na [Rozpoczynanie pracy z usługą Log Analytics w witrynie Azure portal](get-started-portal.md).

Usługa log Analytics oferuje następujące funkcje do pracy z zapytaniami dziennika.

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
> Usługa log Analytics ma taką samą funkcjonalność jak portalu analizy zaawansowanej, czyli narzędzie zewnętrzne poza witryny Azure portal. Portalu analizy zaawansowanej jest nadal dostępna, ale linki i inne odwołania do niego w witrynie Azure portal są zastępowane przy użyciu tej nowej strony.

![Log Analytics](media/portals/log-analytics.png)

### <a name="resource-logs"></a>Dzienniki zasobów
Usługa log Analytics integruje się z różnymi zasobami platformy Azure, takie jak maszyny wirtualne. Oznacza to, możesz otworzyć usługi Log Analytics bezpośrednio za pomocą menu monitorowania zasobów, bez konieczności przełączania do usługi Azure Monitor i utraty kontekstu zasobu. **Dzienniki** nie jeszcze została włączona dla wszystkich zasobów platformy Azure, ale zaczną się pojawiać w menu portalu dla różnych zasobów typów.

Otwieranie usługi Log Analytics z określonego zasobu, jest objęty zakresem automatycznie do rejestrowania rekordów tylko do tego zasobu.   Jeśli chcesz napisać zapytanie, które zawiera inne rekordy, czy należy ją otworzyć z menu usługi Azure Monitor.

Następujące opcje nie są jeszcze dostępne za pośrednictwem widoku zasobów usługi Log Analytics:

- Zapisz
- Ustawianie alertu
- Eksplorator zapytań
- Przełączanie do innego obszaru roboczego/zasobu (aktualnie nie zaplanowana)


### <a name="firewall-requirements"></a>Wymagania dotyczące zapory
Przeglądarka wymaga dostępu do następujących adresów do dostępu do usługi Log Analytics.  Jeśli przeglądarka jest dostęp do witryny Azure portal za pośrednictwem zapory, należy włączyć dostęp do tych adresów.

| Identyfikator URI | Adres IP | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamiczny | 80,443 |
| api.loganalytics.io    | Dynamiczny | 80,443 |
| docs.loganalytics.io   | Dynamiczny | 80,443 |


## <a name="log-search-classic"></a>Wyszukiwanie w dzienniku (wersja klasyczna)
Przeszukiwanie dzienników to starszego środowiska użytkownika w witrynie Azure portal do odpytywania i analizowania danych dziennika w usłudze Azure Monitor. Będzie zostanie wkrótce wycofany, ale obecnie jest nadal dostępna. Otworzyć wyszukiwanie w dzienniku z **dzienniki (wersja klasyczna)** w menu usługi Log Analytics.



![Przeszukiwanie dzienników](media/portals/log-search-portal.png)


## <a name="next-steps"></a>Kolejne kroki

- Przeprowadzenie [samouczku, korzystając z usługi Log Analytics](../../azure-monitor/log-query/get-started-portal.md).
- Przeprowadzenie [samouczku, korzystając z wyszukiwania w dziennikach](../../azure-monitor/learn/tutorial-viewdata.md).

