---
title: Wizualizacja danych usługi Azure Data Explorer
description: Dowiedz się więcej o różnych sposobach wizualizacji danych Eksploratora danych platformy Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139066"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Wizualizacja danych za pomocą Eksploratora danych platformy Azure 

Usługa Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych dla danych dziennika i telemetrii, która jest używana do tworzenia złożonych rozwiązań analitycznych dla ogromnych ilości danych. Usługa Azure Data Explorer integruje się z różnymi narzędziami do wizualizacji, dzięki czemu można wizualizować dane i udostępniać wyniki w całej organizacji. Te dane można przekształcić w użyteczne informacje, aby mieć wpływ na Twoją firmę.

Wizualizacja i raportowanie danych jest krytycznym krokiem w procesie analizy danych. Usługa Azure Data Explorer obsługuje wiele usług analizy biznesowej, dzięki czemu można użyć tego, który najlepiej pasuje do twojego scenariusza i budżetu.

## <a name="kusto-query-language-visualizations"></a>Wizualizacje języka zapytań Kusto

Język [`render operator`](/azure/kusto/query/renderoperator) zapytania Kusto oferuje różne wizualizacje, takie jak tabele, wykresy kołowe i wykresy słupkowe, aby przedstawić wyniki kwerendy. Wizualizacje zapytań są przydatne w wykrywaniu anomalii i prognozowaniu, uczeniu maszynowym i innych.

## <a name="power-bi"></a>Power BI

Usługa Azure Data Explorer umożliwia łączenie się z [usługą Power BI](https://powerbi.microsoft.com) przy użyciu różnych metod: 

  * [Wbudowane natywne złącze usługi Power BI](/azure/data-explorer/power-bi-connector)

  * [Importowanie kwerend z Eksploratora danych platformy Azure do usługi Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Zapytanie SQL](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Eksplorator danych platformy Azure umożliwia łączenie się z [programem Microsoft Excel](https://products.office.com/excel) przy użyciu [wbudowanego natywnego łącznika programu Excel](excel-connector.md)lub [importowanie kwerendy](excel-blank-query.md) z Eksploratora danych platformy Azure do programu Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) udostępnia wtyczkę Usługi Azure Data Explorer, która umożliwia wizualizację danych z Eksploratora danych platformy Azure. Konfigurowanie [Usługi Azure Data Explorer jako źródła danych dla grawany, a następnie wizualizowanie danych](/azure/data-explorer/grafana). 

## <a name="kibana"></a>Kibana

Usługa Azure Data Explorer umożliwia łączenie się z [Kibaną (stroną Discover)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) przy użyciu K2Bridge, łącznika open source. Konfigurowanie [Usługi Azure Data Explorer jako źródła danych dla kibana, a następnie wizualizować dane](/azure/data-explorer/k2bridge).

## <a name="odbc-connector"></a>Łącznik ODBC

Usługa Azure Data Explorer udostępnia [łącznik łączności otwartej bazy danych (ODBC),](connect-odbc.md) dzięki czemu każda aplikacja obsługująca odbc może łączyć się z Eksploratorem danych platformy Azure.

## <a name="tableau"></a>Tableau

Usługa Azure Data Explorer umożliwia łączenie się z [Tableau](https://www.tableau.com) przy użyciu [łącznika ODBC,](/azure/data-explorer/connect-odbc) a następnie [wizualizowanie danych w tableau](tableau.md).

## <a name="qlik"></a>Qlik

Usługa Azure Data Explorer umożliwia łączenie się z [funkcją Qlik](https://www.qlik.com) przy użyciu [łącznika ODBC,](/azure/data-explorer/connect-odbc) a następnie tworzenie pulpitów nawigacyjnych Qlik Sense i wizualizowanie danych. Korzystając z poniższego klipu wideo, możesz nauczyć się wizualizować dane usługi Azure Data Explorer za pomocą qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Usługa Azure Data Explorer udostępnia możliwość łączenia się z [Sisense](https://www.sisense.com) przy użyciu łącznika JDBC. Konfigurowanie [Usługi Azure Data Explorer jako źródła danych dla Sisense, a następnie wizualizowanie danych](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

Redash [Redash](https://redash.io/) służy do tworzenia pulpitów nawigacyjnych i wizualizacji danych. [Skonfiguruj Eksploratora danych platformy Azure jako źródło danych dla programu Redash, a następnie wizualizuj dane](/azure/data-explorer/redash).