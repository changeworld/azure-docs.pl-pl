---
title: Wizualizacja danych w usłudze Azure Eksplorator danych
description: Dowiedz się więcej o różnych sposobów można wizualizować dane Eksploratora danych usługi Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536723"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Wizualizacja danych za pomocą Eksploratora danych platformy Azure 

Eksplorator danych usługi Azure to usługa eksploracji danych szybka i wysoce skalowalne na dane dzienników i danych telemetrycznych, który jest używany do tworzenia rozwiązań do analizy złożonych ogromnych ilości danych. Eksplorator danych usługi Azure integruje się z różnych narzędzi do wizualizacji, dzięki czemu możesz wizualizować dane i udostępniać wyniki w swojej organizacji. Te dane mogą zostać przekształcone do podejmowania określonych działań analizy dzięki wpływ na działalność.

Wizualizacja danych i raportowania jest krytycznym kroku w procesie analizy danych. Eksplorator danych usługi Azure obsługuje wiele usług Power BI, aby można było używać jednego, która najlepiej pasuje do własnego scenariusza i budżetu.

## <a name="kusto-query-language-visualizations"></a>Wizualizacje języka zapytania Kusto

Język zapytania Kusto [ `render operator` ](/azure/kusto/query/renderoperator) oferuje różne wizualizacje, takie jak tabele, wykresy kołowe i wykresów słupkowych, aby przedstawić wyniki zapytania. Wizualizacji zapytań są pomocne w wykrywanie anomalii i prognozowania, uczenie maszynowe i inne.

## <a name="power-bi"></a>Power BI

Eksplorator danych usługi Azure oferuje możliwość nawiązywania [usługi Power BI](https://powerbi.microsoft.com) przy użyciu różnych metod: 

  * [Wbudowany natywny łącznik usługi Power BI](/azure/data-explorer/power-bi-connector)

  * [Importowanie zapytań z Eksploratora danych usługi Azure do usługi Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Zapytanie SQL](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Program Microsoft Excel

Eksplorator danych usługi Azure oferuje możliwość nawiązywania [programu Microsoft Excel](https://products.office.com/excel) przy użyciu wbudowanych native łącznik programu Excel lub zaimportować zapytanie w Eksploratorze danych platformy Azure do programu Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) zapewnia wtyczki Eksploratora danych usługi Azure, która pozwala na wizualizowanie danych z Eksploratora danych usługi Azure. Możesz [Eksploratora danych usługi Azure jako źródło danych dla platformy Grafana, a następnie wizualizować te dane](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>Łącznik ODBC

Eksplorator danych platformy Azure zapewnia [łącznika Open Database Connectivity (ODBC)](connect-odbc.md) dowolnej aplikacji, która obsługuje ODBC do połączenia się do Eksploratora danych platformy Azure.

## <a name="tableau"></a>TABLEAU

Eksplorator danych usługi Azure oferuje możliwość nawiązywania [Tableau](https://www.tableau.com) przy użyciu [łącznik ODBC](/azure/data-explorer/connect-odbc) i następnie [wizualizować dane w Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Eksplorator danych usługi Azure oferuje możliwość nawiązywania [Qlik](https://www.qlik.com) przy użyciu [łącznik ODBC](/azure/data-explorer/connect-odbc) tworzyć usługa Qlik Sense pulpity nawigacyjne, a następnie wizualizować te dane. Korzystając z poniższego wideo, nauczysz się Wizualizacja danych Eksplorator danych Azure przy użyciu Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Eksplorator danych usługi Azure oferuje możliwość nawiązywania [Sisense](https://www.sisense.com) przy użyciu sterownika JDBC łącznika. Możesz [Eksploratora danych usługi Azure jako źródło danych dla Sisense, a następnie wizualizować te dane](/azure/data-explorer/sisense).