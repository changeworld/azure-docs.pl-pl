---
title: Tworzenie zintegrowanych rozwiązań z usługą SQL Data Warehouse | Dokumentacja firmy Microsoft
description: 'Narzędzia i partnerów oferujących rozwiązania, które integrują się z usługą SQL Data Warehouse. '
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 221d5d05906e7e162013c0d4cdddc01a95f4024c
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302706"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrowanie innych usług z usługą SQL Data Warehouse
Oprócz podstawowych funkcji SQL Data Warehouse umożliwia integrację z wielu innych usług na platformie Azure. Oto niektóre z tych usług:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Usługa Azure Stream Analytics

Usługa SQL Data Warehouse w dalszym ciągu integrowania dzięki większej liczbie usług platformy Azure i nie tylko [partnerów integracji](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Integracja usługi Power BI pozwala połączyć mocy obliczeniowej usługi SQL Data Warehouse przy użyciu dynamicznych raportów i wizualizacji usługi Power BI. Integracja usługi Power BI aktualnie obejmuje:

* **Bezpośrednie łączenie**: bardziej zaawansowanej połączenia przy użyciu przekazywania logicznej względem SQL Data Warehouse. Przekazywanie zapewnia szybsze analiz na większą skalę.
* **Otwórz w usłudze Power BI**: przycisk "Otwórz w usłudze Power BI" przekazuje informacje o wystąpieniu do usługi Power BI dla simplifed sposób łączenia.

Aby uzyskać więcej informacji, zobacz [Integracja z usługą Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md), lub [dokumentacji usługi Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

## <a name="azure-data-factory"></a>Azure Data Factory
Usługa Azure Data Factory zapewnia użytkownikom zarządzana platforma umożliwiająca tworzenie złożonych wyodrębnianie i ładowanie potoków. Usługa SQL Data Warehouse integracji z usługą Azure Data Factory obejmuje:

* **Procedury składowane**: organizowanie wykonywania procedur składowanych w usłudze SQL Data Warehouse.
* **Kopiuj**: skorzystaj z usługi ADF w celu przeniesienia danych do usługi SQL Data Warehouse. Ta operacja służy mechanizm przenoszenia danych w warstwie standardowa usługi ADF lub technologii PolyBase w sposób niewidoczny. 

Aby uzyskać więcej informacji, zobacz [integracji z usługą Azure Data Factory](sql-data-warehouse-get-started-visualize-with-power-bi.md).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Usługa Azure Machine Learning to usługa analizy w pełni zarządzana, co pozwala na tworzenie skomplikowanych modeli przy użyciu duży zestaw funkcji wspomagających predykcyjne. Usługa SQL Data Warehouse jest obsługiwany jako źródło i miejsce docelowe dla tych modeli następujące funkcje:

* **Odczyt danych:** dysku modeli na dużą skalę przy użyciu języka T-SQL wobec SQL Data Warehouse.
* **Zapis danych:** zatwierdzenia zmieni się z dowolnego modelu do SQL Data Warehouse.

Aby uzyskać więcej informacji, zobacz [integracji z usługą Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
Usługa Azure Stream Analytics jest złożone, w pełni zarządzana infrastruktura do przetwarzania i wykorzystywanie danych zdarzeń generowanych przez usługi Azure Event Hub.  Integracja z usługą SQL Data Warehouse umożliwia przesyłanie strumieniowe danych do efektywnego przetwarzania i przechowywania oraz włączenie bardziej, bardziej zaawansowanych analiz w relacyjnej bazie danych.  

* **Dane wyjściowe zadania:** wysyłania danych wyjściowych z zadania usługi Stream Analytics bezpośrednio w usłudze SQL Data Warehouse.

Aby uzyskać więcej informacji, zobacz [Integracja z usługą Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).

## <a name="next-steps"></a>Kolejne kroki
Aby zintegrować z usługą Azure SQL Database, zobacz sekcję [zapytanie elastyczne konfigurowanie bazy danych SQL](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)

