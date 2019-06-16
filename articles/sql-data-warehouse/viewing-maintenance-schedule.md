---
title: Harmonogramy konserwacji platformy Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Planowanie konserwacji umożliwia klientom w planowaniu wokół zdarzenia niezbędne zaplanowanej konserwacji, które korzysta z usługi Azure SQL Data warehouse to dystrybucję nowych funkcji, uaktualnienia i poprawki.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 61537a4cb8ca1e6e98f2fa04959861eb03480fe9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61474887"
---
# <a name="view-a-maintenance-schedule"></a>Wyświetl harmonogram konserwacji 

## <a name="portal"></a>Portal

Domyślnie wszystkie nowo utworzonego wystąpienia usługi Azure SQL Data Warehouse ma ośmiu godzin podstawowych i pomocniczych konserwacyjne stosowane podczas wdrażania. Można zmieniać systemu windows, jak szybko wdrożenie jest ukończone. Nie obsługi będzie miała miejsce poza określonym oknami bez wcześniejszego powiadomienia.

Aby wyświetlić harmonogramu konserwacji, która została zastosowana do magazynu danych, wykonaj następujące czynności:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  Wybierz magazyn danych, który chcesz wyświetlić. 
3.  Otwiera hurtownią danych wybranych w bloku przeglądu. Harmonogram konserwacji, która jest stosowana w magazynie danych, który pojawia się poniżej **harmonogram konserwacji (wersja zapoznawcza)** .

![Blok przeglądu](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Kolejne kroki
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) o tworzenie, wyświetlanie i Zarządzanie alertami przy użyciu usługi Azure Monitor.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) o akcje elementu webhook dla reguł alertów dzienników.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) tworzenie grup i zarządzanie nimi akcji.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/service-health-overview) dotyczące usługi Azure Service Health.


