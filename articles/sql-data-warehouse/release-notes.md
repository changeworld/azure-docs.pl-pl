---
title: Usługa Azure SQL Data Warehouse — informacje o wersji | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/11/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 7a02a7fb8043c63ce47f7f0c7dccf025ab5ab402
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421457"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Informacje o wersji usługi Azure SQL Data Warehouse

Usługa Azure SQL Data Warehouse (SQL data Warehouse) jest oparta na chmurze Enterprise magazynu danych, który wykorzystuje wysoce równoległe przetwarzanie (MPP) do szybkiego uruchamiania złożonych zapytań obejmujących petabajty danych. Użyj usługi SQL Data Warehouse jako głównego składnika rozwiązania danych big data. Zaimportuj dane big data do usługi SQL Data Warehouse za pomocą prostych zapytań PolyBase T-SQL, a następnie użyj zaawansowanych funkcji architektury MPP do uruchomienia analityki wysokiej wydajności. Podczas integracji i analizy hurtownia danych stanie się pojedynczym źródłem danych, na którym Twoja firma może polegać przy szukaniu szczegółowych informacji.

Kliknij poniższe łącza, aby dowiedzieć się więcej na temat nowych funkcji i ulepszeń, które można oczekiwać, że w najnowszej wersji usługi Azure SQL Data Warehouse. Można oczekiwać, że do odbierania aktualizacji tej usługi podczas harmonogramu konserwacji zidentyfikowane.

- [SQL DW Version 10.0.10106.0](./release-notes-10-0-10106-0.md)
- [Grudnia 2018 r.](./release-notes-december-2018.md)
- [Października 2018 r.](./release-notes-october-2018.md)
- [Września 2018 r.](./release-notes-september-2018.md)
- [Sierpnia 2018](./release-notes-august-2018.md)
- [Lipca 2018 r.](./release-notes-july-2018.md)
- [Czerwca 2018 r.](./release-notes-june-2018.md)
- [Maja 2018 r.](./release-notes-may-2018.md)

## <a name="checking-the-code-version-that-has-been-applied-to-your-data-warehouse"></a>Sprawdzanie wersji kodu, która została zastosowana do magazynu danych

Aby upewnić się, która wersja została jako zostały zastosowane do magazynu danych. Łączenie z magazynem danych za pomocą programu SSMS, a następnie uruchom następującej składni, aby powrócić do bieżącej wersji usługi SQL Data Warehouse.

```sql
SELECT @@VERSION AS 'SQL Data Warehouse';
```

Przykładowe dane wyjściowe: ![Wersja SQL Data Warehouse](./media/release-notes/dw-version.png)

Użyj wersji identyfikowane, aby upewnić się, która wersja została zastosowana do usługi Azure SQL Data Warehouse. 


## <a name="next-steps"></a>Kolejne kroki
- [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule) o wyświetlaniu harmonogram konserwacji. 
- [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule) o zmienianiu harmonogram konserwacji.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric) o tworzenie, wyświetlanie i Zarządzanie alertami przy użyciu usługi Azure Monitor.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) o akcje elementu webhook dla reguł alertów dzienników.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/service-health-overview) dotyczące usługi Azure Service Health
