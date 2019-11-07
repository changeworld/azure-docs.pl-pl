---
title: Tworzenie zintegrowanych rozwiązań
description: Narzędzia i partnerzy z rozwiązaniami, które integrują się z Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e159e0e254ae8b2515515dfaeb2c514e0f25e0b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685645"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrowanie innych usług z usługą SQL Data Warehouse
Oprócz podstawowych funkcji, SQL Data Warehouse umożliwia użytkownikom integrację z wieloma innymi usługami na platformie Azure. Niektóre z tych usług obejmują:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse nadal integrują się z większą liczbą usług na platformie Azure i większą liczbą [partnerów integracji](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Integracja Power BI umożliwia łączenie mocy obliczeniowej SQL Data Warehouse z raportowaniem dynamicznym i wizualizacją Power BI. Obecnie integracja Power BI obejmuje:

* **Połączenie bezpośrednie**: bardziej zaawansowane połączenie z przekazywaniem logicznym względem SQL Data Warehouse. Przekazywanie zapewnia szybszą analizę w większej skali.
* **Otwórz w Power BI**: przycisk "otwórz w Power BI" przekazuje informacje o wystąpieniu do Power BI w uproszczony sposób nawiązywania połączenia.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)lub [Dokumentacja Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory zapewnia użytkownikom zarządzaną platformę do tworzenia złożonej wyodrębniania i potoków ładowania. Integracja SQL Data Warehouse z Azure Data Factory obejmuje:

* **Procedury składowane**: Organizuj wykonywanie procedur składowanych na SQL Data Warehouse.
* **Kopiuj**: Użyj podajnika APD do przenoszenia danych do SQL Data Warehouse. Ta operacja może korzystać ze standardowego mechanizmu przenoszenia danych ADF lub bazy w ramach okładek. 

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning to w pełni zarządzana usługa analizy, która umożliwia tworzenie modeli Intricate przy użyciu dużego zestawu narzędzi predykcyjnych. SQL Data Warehouse jest obsługiwane zarówno jako źródło, jak i miejsce docelowe dla tych modeli o następujących funkcjach:

* **Odczytaj dane:** Modele dysków na dużą skalę przy użyciu języka T-SQL względem SQL Data Warehouse.
* **Zapisz dane:** Zatwierdź zmiany z dowolnego modelu z powrotem do SQL Data Warehouse.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics to złożona, w pełni zarządzana infrastruktura służąca do przetwarzania i zużywania danych zdarzeń generowanych z usługi Azure Event Hub.  Integracja z SQL Data Warehouse umożliwia wydajne przetwarzanie danych przesyłanych strumieniowo i przechowywanie ich wraz z danymi relacyjnymi, co pozwala na dokładniejszą i bardziej zaawansowaną analizę.  

* **Dane wyjściowe zadania:** Wyślij dane wyjściowe z zadań Stream Analytics bezpośrednio do SQL Data Warehouse.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


