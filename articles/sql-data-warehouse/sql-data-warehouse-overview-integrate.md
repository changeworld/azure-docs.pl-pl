---
title: Tworzenie zintegrowanych rozwiązań z usługą SQL Data Warehouse | Dokumentacja firmy Microsoft
description: 'Narzędzia i partnerów oferujących rozwiązania, które integrują się z usługą SQL Data Warehouse. '
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 43a714ae175e0d60f20b5e7ad79e1fa90125b0f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873339"
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

* **O połączeniu bezpośrednim**: Bardziej zaawansowane połączenie przy użyciu przekazywania logicznej względem SQL Data Warehouse. Przekazywanie zapewnia szybsze analiz na większą skalę.
* **Otwórz w usłudze Power BI**: Przycisk "Otwórz w usłudze Power BI" przekazuje informacje o wystąpieniu usługi Power BI dla uproszczony sposób na połączenie.

Aby uzyskać więcej informacji, zobacz [Integracja z usługą Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md), lub [dokumentacji usługi Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Usługa Azure Data Factory zapewnia użytkownikom zarządzana platforma umożliwiająca tworzenie złożonych wyodrębnianie i ładowanie potoków. Usługa SQL Data Warehouse integracji z usługą Azure Data Factory obejmuje:

* **Procedury składowane**: Możesz odpowiednio organizować wykonywania procedur składowanych w usłudze SQL Data Warehouse.
* **Kopiuj**: Użyj usługi ADF, aby przenieść dane do usługi SQL Data Warehouse. Ta operacja służy mechanizm przenoszenia danych w warstwie standardowa usługi ADF lub technologii PolyBase w sposób niewidoczny. 

Aby uzyskać więcej informacji, zobacz [integracji z usługą Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Usługa Azure Machine Learning to usługa analizy w pełni zarządzana, co pozwala na tworzenie skomplikowanych modeli przy użyciu duży zestaw funkcji wspomagających predykcyjne. Usługa SQL Data Warehouse jest obsługiwany jako źródło i miejsce docelowe dla tych modeli następujące funkcje:

* **Odczyt danych:** Podejmuj modeli na dużą skalę przy użyciu języka T-SQL wobec SQL Data Warehouse.
* **Zapis danych:** Zatwierdź zmiany z każdego modelu do SQL Data Warehouse.

Aby uzyskać więcej informacji, zobacz [integracji z usługą Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
Usługa Azure Stream Analytics jest złożone, w pełni zarządzana infrastruktura do przetwarzania i wykorzystywanie danych zdarzeń generowanych przez usługi Azure Event Hub.  Integracja z usługą SQL Data Warehouse umożliwia przesyłanie strumieniowe danych do efektywnego przetwarzania i przechowywania oraz włączenie bardziej, bardziej zaawansowanych analiz w relacyjnej bazie danych.  

* **Dane wyjściowe zadania:** Wyślij dane wyjściowe z zadania usługi Stream Analytics bezpośrednio w usłudze SQL Data Warehouse.

Aby uzyskać więcej informacji, zobacz [Integracja z usługą Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


