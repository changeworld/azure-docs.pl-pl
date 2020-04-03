---
title: Tworzenie zintegrowanych rozwiązań
description: Narzędzia rozwiązań i partnerów, które integrują się z puli SQL Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c8e3598e55d3f90ab2b7401380406677f56c0ce3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586478"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool"></a>Integracja innych usług z pulą SQL Synapse

Funkcja puli sql synapse w usłudze Azure Synapse Analytics umożliwia użytkownikom integrację z wieloma innymi usługami na platformie Azure. Za pomocą Synapse SQL, można utworzyć magazyn danych za pośrednictwem zasobu puli SQL, które następnie można wykorzystać kilka dodatkowych usług, z których niektóre obejmują:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Usługa Azure Stream Analytics

Aby uzyskać więcej informacji dotyczących usług integracji na platformie Azure, zapoznaj się z [artykułem partnerów integracji.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI
Integracja usługi Power BI umożliwia połączenie mocy obliczeniowej magazynu danych z dynamicznym raportowaniem i wizualizacją usługi Power BI. Integracja usługi Power BI obejmuje obecnie:

* **Direct Connect:** bardziej zaawansowane połączenie z logicznym wypychaniem względem magazynu danych aprowizowanego przy użyciu puli SQL. Pushdown zapewnia szybszą analizę na większą skalę.
* **Otwórz w usłudze Power BI:** Przycisk "Otwórz w usłudze Power BI" przekazuje informacje o wystąpieniu do usługi Power BI w celu uproszczonego połączenia.

Aby uzyskać więcej informacji, zobacz [Integrowanie z programem Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)lub [dokumentacja usługi Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Usługa Azure Data Factory udostępnia użytkownikom zarządzaną platformę do tworzenia złożonych potoków wyodrębniania i ładowania. Integracja puli SQL z usługą Azure Data Factory obejmuje:

* **Procedury przechowywane:** Aranżuj wykonywanie procedur przechowywanych.
* **Kopiuj**: Przenoszenie danych do puli SQL za pomocą podajnika ADF. Ta operacja może używać standardowego mechanizmu przenoszenia danych ADF lub PolyBase pod osłonami. 

Aby uzyskać więcej informacji, zobacz [Integrowanie z usługą Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Usługa Azure Machine Learning to w pełni zarządzana usługa analityczna, która umożliwia tworzenie skomplikowanych modeli przy użyciu dużego zestawu narzędzi predykcyjnych. Pula SQL jest obsługiwana zarówno jako źródło, jak i miejsce docelowe dla tych modeli i ma następujące funkcje:

* **Odczyt danych:** Dysk modeli na dużą skalę przy użyciu T-SQL przeciwko puli SQL.
* **Zapis danych:** Zatwierdzanie zmian z dowolnego modelu z powrotem do puli SQL.

Aby uzyskać więcej informacji, zobacz [Integrowanie z usługą Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
Usługa Azure Stream Analytics to złożona, w pełni zarządzana infrastruktura do przetwarzania i korzystania z danych zdarzeń generowanych przez usługę Azure Event Hub.  Integracja z pulą SQL umożliwia efektywne przetwarzanie i przechowywanie danych strumieniowych wraz z danymi relacyjnymi, umożliwiając głębszą, bardziej zaawansowaną analizę.  

* **Wydajność zadania:** Wyślij dane wyjściowe z zadań usługi Stream Analytics bezpośrednio do puli SQL.

Aby uzyskać więcej informacji, zobacz [Integrowanie z usługą Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


