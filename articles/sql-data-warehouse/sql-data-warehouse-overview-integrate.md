---
title: Tworzenie rozwiązań zintegrowanych z usługą Magazyn danych SQL | Dokumentacja firmy Microsoft
description: 'Narzędzia i partnerom rozwiązania, które integrują się z usługą Magazyn danych SQL. '
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: f198a99fc03a079be77c7f8167580bb7b758579e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrowanie innych usług z magazynu danych SQL
Oprócz jego podstawowych funkcji usługi SQL Data Warehouse pozwala użytkownikom na integrację z wielu innych usług Azure. Oto niektóre z tych usług:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Usługa Azure Stream Analytics

Magazyn danych SQL w dalszym ciągu integrują się z usługami więcej na platformie Azure i więcej [partnerów integracji](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Power BI integracji umożliwia łączenie moc obliczeniową usługi SQL Data Warehouse z dynamicznych raportowania i wizualizacji usługi Power BI. Power BI integracji obecnie obejmuje:

* **Bezpośrednie połączenia**: bardziej zaawansowane połączenia z logiczną przekazywanie do magazynu danych SQL. Przekazywanie zapewnia szybsze analizy na większą skalę.
* **Otwórz w usłudze Power BI**: przycisk "Otwórz w usłudze Power BI" przekazuje informacje o wystąpieniu do usługi Power BI dla simplifed sposobem łączenia.

Aby uzyskać więcej informacji, zobacz [integracji z usługą Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md), lub [dokumentacji usługi Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

## <a name="azure-data-factory"></a>Azure Data Factory
Fabryka danych Azure umożliwia użytkownikom zarządzanych platformę do tworzenia złożonych wyodrębniania i załadować potoków. Integracja SQL Data Warehouse z fabryką danych Azure obejmuje:

* **Procedury składowane**: organizowania wykonywania procedur składowanych na magazyn danych SQL.
* **Kopiuj**: ADF używany do przenoszenia danych do usługi SQL Data Warehouse. Ta operacja może używają standardowych danych ADF mechanizm lub PolyBase. 

Aby uzyskać więcej informacji, zobacz [integracji z fabryką danych Azure](sql-data-warehouse-get-started-visualize-with-power-bi.md).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Usługa Azure Machine Learning to usługa analityka w pełni zarządzana, co pozwala na tworzenie modeli skomplikowanych przy użyciu dużego zestawu narzędzi predykcyjnej. Magazyn danych SQL jest obsługiwany jako źródło i miejsce docelowe dla tych modeli następujące funkcje:

* **Odczyt danych:** dysków modeli na dużą skalę przed SQL Data Warehouse przy użyciu T-SQL.
* **Zapis danych:** zatwierdzania zmieni się z dowolnego modelu do usługi SQL Data Warehouse.

Aby uzyskać więcej informacji, zobacz [integracji z usługą Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
Usługa Azure Stream Analytics jest złożone, w pełni zarządzana infrastruktura przetwarzania i wykorzystywanie danych zdarzeń generowanych przez Centrum zdarzeń usługi Azure.  Integracja z usługą SQL Data Warehouse umożliwia strumieniowe przesyłanie danych do skutecznie przetwarzania i przechowywania obok włączenie głębiej, bardziej zaawansowane analizy danych relacyjnych.  

* **Dane wyjściowe zadania:** wysyłania danych wyjściowych z zadania usługi analiza strumienia bezpośrednio do usługi SQL Data Warehouse.

Aby uzyskać więcej informacji, zobacz [integracji z usługą Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).

## <a name="next-steps"></a>Kolejne kroki
Aby zintegrować z bazy danych SQL Azure, zobacz [zapytania elastycznej Konfigurowanie bazy danych SQL](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)

