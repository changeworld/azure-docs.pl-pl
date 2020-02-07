---
title: Wizualizacja danych Eksplorator danych platformy Azure
description: Dowiedz się więcej na temat różnych sposobów wizualizacji danych Eksplorator danych platformy Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 32ccc5ad236c87d3609798a8432db14ee440d067
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064567"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Wizualizacja danych za pomocą usługi Azure Eksplorator danych 

Azure Eksplorator danych to szybka i wysoce skalowalna usługa eksploracji danych służąca do tworzenia złożonych rozwiązań analitycznych w przypadku dużych ilości danych. Platforma Azure Eksplorator danych integruje się z różnymi narzędziami do wizualizacji, dzięki czemu możesz wizualizować dane i udostępniać wyniki w całej organizacji. Te dane można przekształcać w szczegółowe informacje umożliwiające podejmowanie działań, aby mieć wpływ na działalność firmy.

Wizualizacja i raportowanie danych to krytyczny krok w procesie analizy danych. Usługa Azure Eksplorator danych obsługuje wiele usług analizy biznesowej, dzięki czemu możesz użyć tego, który najlepiej pasuje do Twojego scenariusza i budżetu.

## <a name="kusto-query-language-visualizations"></a>Wizualizacje języka zapytań Kusto

Język zapytania Kusto [`render operator`](/azure/kusto/query/renderoperator) oferuje różne wizualizacje, takie jak tabele, wykresy kołowe i wykresy słupkowe, aby przedstawić wyniki zapytania. Wizualizacje zapytań są przydatne w przypadku wykrywania anomalii i prognozowania, uczenia maszynowego i innych.

## <a name="power-bi"></a>Power BI

Usługa Azure Eksplorator danych oferuje możliwość nawiązywania połączeń z [Power BI](https://powerbi.microsoft.com) przy użyciu różnych metod: 

  * [Wbudowany natywny łącznik Power BI](/azure/data-explorer/power-bi-connector)

  * [Zaimportuj zapytania z usługi Azure Eksplorator danych do Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Zapytanie SQL](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Usługa Azure Eksplorator danych oferuje możliwość nawiązywania połączenia z [programem Microsoft Excel](https://products.office.com/excel) przy użyciu [wbudowanego natywnego łącznika programu Excel](excel-connector.md)lub [importowania zapytania](excel-blank-query.md) z usługi Azure Eksplorator danych do programu Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) zapewnia wtyczkę Eksplorator danych platformy Azure, która umożliwia wizualizację danych z usługi Azure Eksplorator danych. Należy [skonfigurować Eksplorator danych platformy Azure jako źródło danych dla Grafana, a następnie wizualizować dane](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>Łącznik ODBC

Usługa Azure Eksplorator danych udostępnia [łącznik Open Database Connectivity (ODBC)](connect-odbc.md) , dzięki czemu dowolna aplikacja obsługująca ODBC może łączyć się z usługą Azure Eksplorator danych.

## <a name="tableau"></a>Tableau

Usługa Azure Eksplorator danych oferuje możliwość nawiązywania połączenia z usługą [Tableau](https://www.tableau.com) przy użyciu [łącznika ODBC](/azure/data-explorer/connect-odbc) , a następnie [wizualizowania danych w Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Usługa Azure Eksplorator danych oferuje możliwość nawiązywania połączenia z usługą [Qlik](https://www.qlik.com) przy użyciu [łącznika ODBC](/azure/data-explorer/connect-odbc) , a następnie tworzenia pulpitów nawigacyjnych wykrywania Qlik i wizualizacji danych. Korzystając z poniższego wideo, możesz dowiedzieć się, jak wizualizować dane Eksplorator danych platformy Azure za pomocą Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Usługa Azure Eksplorator danych oferuje możliwość nawiązywania połączenia z usługą [SiSense](https://www.sisense.com) przy użyciu łącznika JDBC. Należy [skonfigurować Eksplorator danych platformy Azure jako źródło danych dla SiSense, a następnie wizualizować dane](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

Aby tworzyć pulpity nawigacyjne i wizualizować dane, można użyć pozostałego [łącznika](https://redash.io/) . [Skonfiguruj Eksplorator danych platformy Azure jako źródło danych do oddzielenia, a następnie Wizualizuj dane](/azure/data-explorer/redash).