---
title: Wizualizacja danych w usłudze Azure Eksplorator danych
description: Dowiedz się więcej o różnych sposobów można wizualizować dane Eksploratora danych usługi Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481837"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Wizualizacja danych za pomocą Eksploratora danych platformy Azure 

Eksplorator danych usługi Azure to usługa eksploracji danych szybka i wysoce skalowalne na dane dzienników i danych telemetrycznych, który jest używany do tworzenia rozwiązań do analizy złożonych ogromnych ilości danych. Eksplorator danych usługi Azure integruje się z różnych narzędzi do wizualizacji, dzięki czemu możesz wizualizować dane i udostępniać wyniki w swojej organizacji. Te dane mogą zostać przekształcone do podejmowania określonych działań analizy dzięki wpływ na działalność.

Wizualizacja danych i raportowania jest krytycznym kroku w procesie analizy danych. Eksplorator danych usługi Azure obsługuje wiele usług Power BI, aby można było używać jednego, która najlepiej pasuje do własnego scenariusza i budżetu.

* Usługa Azure Eksplorator danych w wizualizacji: Za pomocą języka zapytania Kusto [ `render operator` ](/azure/kusto/query/renderoperator) oferuje różne typy wizualizacji, aby przedstawić wyniki zapytania. Wizualizacji zapytań są pomocne w wykrywanie anomalii i prognozowania, uczenie maszynowe i inne.

* [Power BI](https://powerbi.microsoft.com): Eksplorator danych usługi Azure oferuje możliwość podłączania do usługi Power BI przy użyciu różnych metod: 

  * [Wbudowany natywny łącznik usługi Power BI](/azure/data-explorer/power-bi-connector)

  * [Importowanie zapytań z Eksploratora danych usługi Azure do usługi Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Zapytanie SQL](/azure/data-explorer/power-bi-sql-query).

* [Program Microsoft Excel](https://products.office.com/excel): Eksplorator danych usługi Azure oferuje możliwość połączenia z programu Excel za pomocą wbudowanego natywnych łącznika programu Excel lub zaimportować zapytanie w Eksploratorze danych platformy Azure do programu Excel.

* [Grafana](https://grafana.com): Grafana zapewnia wtyczki Eksploratora danych usługi Azure, która pozwala na wizualizowanie danych z Eksploratora danych usługi Azure. Możesz [Eksploratora danych usługi Azure jako źródło danych dla platformy Grafana, a następnie wizualizować te dane](/azure/data-explorer/grafana)

* [Sisense](https://www.sisense.com): Eksplorator danych usługi Azure oferuje możliwość nawiązywania Sisense przy użyciu sterownika JDBC łącznika. Możesz [Eksploratora danych usługi Azure jako źródło danych dla Sisense, a następnie wizualizować te dane](/azure/data-explorer/sisense).

* [Tableau](https://www.tableau.com): Eksplorator danych usługi Azure oferuje możliwość nawiązywania połączeń za pomocą Tableau [łącznik ODBC i wizualizować dane w Tableau](/azure/data-explorer/connect-odbc).

* [Qlik](https://www.qlik.com): Eksplorator danych usługi Azure oferuje możliwość nawiązywania połączeń za pomocą Qlik [łącznik ODBC](/azure/data-explorer/connect-odbc).