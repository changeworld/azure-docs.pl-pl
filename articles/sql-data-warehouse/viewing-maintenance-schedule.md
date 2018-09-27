---
title: Harmonogramy konserwacji platformy Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Planowanie konserwacji umożliwia klientom planować wokół zdarzenia niezbędne zaplanowanej konserwacji, korzysta z usługi Azure SQL Data warehouse to dystrybucję nowych funkcji, uaktualnienia i poprawki.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 6fbf914c8035344d33e8d2739fb9d92d5757c3d1
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228361"
---
# <a name="viewing-a-maintenance-schedule"></a>Wyświetlanie harmonogram konserwacji 

## <a name="portal"></a>Portal

Domyślnie wszystkie nowo utworzone Azure SQL Data Warehouse wystąpień będzie mieć 8 godz. zdefiniowane przez system podstawowym i pomocniczym okna obsługi stosowane podczas wdrażania to może być edytowana wkrótce wdrożenie jest ukończone. Nie obsługi będzie miała miejsce poza oknami obsługi określona, bez wcześniejszego powiadomienia.
Wykonaj poniższe kroki, aby wyświetlić harmonogramu konserwacji, która została zastosowana do usługi data warehouse w portalu.

Wykonaj poniższe kroki, aby wyświetlić harmonogramu konserwacji, która została zastosowana do usługi data warehouse w portalu.
1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  Wybierz magazyn danych, który chcesz wyświetlić. 
3.  Wybrane usługi Azure SQL Data Warehouse spowoduje otwarcie bloku Przegląd. Harmonogram konserwacji zastosowane do magazynu danych zostaną wyświetlone poniżej harmonogram konserwacji (wersja zapoznawcza).

![Blok przeglądu](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) o tworzenie, wyświetlanie i Zarządzanie alertami przy użyciu usługi Azure Monitor.
[Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) o akcje elementu Webhook dla reguł alertów dzienników.
[Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/service-health-overview) dotyczące usługi Azure Service Health


