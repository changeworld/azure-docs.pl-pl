---
title: Usługa Azure SQL Data Warehouse — informacje o wersji | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
ms.openlocfilehash: 2ac60287c9d92ab6230e1dd6777504036e54492d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244678"
---
# <a name="azure-sql-data-warehouse-release-notes-and-documentation-updates"></a>Informacje o wersji usługi Azure SQL Data Warehouse i aktualizacje dokumentacji

Usługa Azure SQL Data Warehouse (SQL data Warehouse) jest oparta na chmurze Enterprise magazynu danych, który wykorzystuje wysoce równoległe przetwarzanie (MPP) do szybkiego uruchamiania złożonych zapytań obejmujących petabajty danych. Użyj usługi SQL Data Warehouse jako głównego składnika rozwiązania danych big data. Zaimportuj dane big data do usługi SQL Data Warehouse za pomocą prostych zapytań PolyBase T-SQL, a następnie użyj zaawansowanych funkcji architektury MPP do uruchomienia analityki wysokiej wydajności. Podczas integracji i analizy hurtownia danych stanie się pojedynczym źródłem danych, na którym Twoja firma może polegać przy szukaniu szczegółowych informacji.

Kliknij poniższe łącza, aby dowiedzieć się więcej na temat nowych funkcji i ulepszeń, które można oczekiwać, że w najnowszej wersji usługi Azure SQL Data Warehouse. Można oczekiwać, że do odbierania aktualizacji tej usługi podczas harmonogramu konserwacji zidentyfikowane.

- [Wersja magazynu danych SQL 10.0.10106.0 (styczeń)](./release-notes-10-0-10106-0.md)
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