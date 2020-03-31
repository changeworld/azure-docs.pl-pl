---
title: Tworzenie pulpitu nawigacyjnego w czasie rzeczywistym przy użyciu usługi Azure Cosmos DB, usługi Azure Analysis Services i usługi Power BI
description: Dowiedz się, jak utworzyć pulpit nawigacyjny pogody na żywo w usłudze Power BI przy użyciu usługi Azure Cosmos DB i usługi Azure Analysis Services.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376595"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Tworzenie pulpitu nawigacyjnego w czasie rzeczywistym przy użyciu usługi Azure Cosmos DB i usługi Power BI

W tym artykule opisano kroki wymagane do utworzenia pulpitu nawigacyjnego pogody na żywo w usłudze Power BI przy użyciu usługi Azure Cosmos DB i usług Azure Analysis Services. Pulpit nawigacyjny usługi Power BI wyświetli wykresy pokazujące w czasie rzeczywistym informacje o temperaturze i opadach w regionie.

## <a name="reporting-scenarios"></a>Scenariusze raportowania

Istnieje wiele sposobów konfigurowania pulpitów nawigacyjnych raportowania danych przechowywanych w usłudze Azure Cosmos DB. W zależności od wymagań dotyczących nieaktualności i rozmiaru danych w poniższej tabeli opisano konfigurację raportowania dla każdego scenariusza:


|Scenariusz |Konfiguracja |
|---------|---------|
|1. Generowanie raportów ad hoc (bez odświeżania)    |  [Łącznik usługi Power BI Azure Cosmos DB z trybem importu](powerbi-visualize.md)       |
|2. Generowanie raportów ad hoc z okresowym odświeżaniem   |  [Łącznik usługi Power BI Azure Cosmos DB z trybem importu (zaplanowane okresowe odświeżanie)](powerbi-visualize.md)       |
|3. Raportowanie dużych zbiorów danych (< 10 GB)     |  Łącznik usługi Power BI Azure Cosmos DB z odświeżaniem przyrostowym       |
|4. Raportowanie w czasie rzeczywistym na dużych zbiorach danych    |  Łącznik usług Analysis Services usługi Power BI Azure z zapytaniami bezpośrednimi + usługami Azure Analysis Services (łącznik usługi Azure Cosmos DB)       |
|5. Raportowanie danych na żywo z agregatami     |  [Łącznik platformy Power BI Spark z zapytaniami bezpośrednimi + łącznikiem azure databricks + cosmos DB Spark.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Raportowanie danych na żywo z agregatami dużych zbiorów danych   |  Łącznik usług Analysis Services usługi Power BI Azure z zapytaniami bezpośrednimi + usługi Azure Analysis Services + łącznik Azure Databricks + Cosmos DB Spark.       |

Scenariusze 1 i 2 można łatwo skonfigurować przy użyciu łącznika usługi Power BI usługi Azure Cosmos DB. W tym artykule opisano konfiguracje scenariuszy 3 i 4.

### <a name="power-bi-with-incremental-refresh"></a>Usługa Power BI z odświeżaniem przyrostowym

Usługa Power BI ma tryb, w którym można skonfigurować odświeżanie przyrostowe. Ten tryb eliminuje konieczność tworzenia partycji usług Azure Analysis Services i zarządzania nimi. Odświeżanie przyrostowe można skonfigurować w celu filtrowania tylko najnowszych aktualizacji w dużych zestawach danych. Jednak ten tryb działa tylko z usługą Power BI Premium, która ma ograniczenie zestawu danych do 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Łącznik usługi Power BI Azure Analysis + usługi Azure Analysis Services

Usługa Azure Analysis Services zapewnia w pełni zarządzaną platformę jako usługę obsługującą modele danych klasy korporacyjnej w chmurze. Ogromne zestawy danych można załadować z usługi Azure Cosmos DB do usługi Azure Analysis Services. Aby uniknąć wykonywania zapytań całego zestawu danych przez cały czas, zestawy danych można podzielić na partycje usługi Azure Analysis Services, które można odświeżyć niezależnie przy różnych częstotliwościach.

## <a name="power-bi-incremental-refresh"></a>Odświeżanie przyrostowe usługi Power BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Pochłonienie danych pogodowych do usługi Azure Cosmos DB

Skonfiguruj potok pozyskiwania, aby załadować [dane pogodowe](https://catalog.data.gov/dataset/local-weather-archive) do usługi Azure Cosmos DB. Można skonfigurować zadanie [usługi Azure Data Factory (ADF),](../data-factory/connector-azure-cosmos-db.md) aby okresowo ładować najnowsze dane pogodowe do usługi Azure Cosmos DB przy użyciu źródła HTTP i ujścia usługi Cosmos DB.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Łączenie usługi Power BI z usługą Azure Cosmos DB

1. **Połącz konto usługi Azure Cosmos z usługą Power BI** — otwórz pulpit usługi Power BI i użyj łącznika usługi Azure Cosmos DB, aby wybrać odpowiednią bazę danych i kontener.

   ![Łącznik usługi PowerBI w usłudze Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Konfigurowanie odświeżania przyrostowego** — postępuj zgodnie z instrukcjami w artykule [przyrostowego odświeżania za pomocą](/power-bi/service-premium-incremental-refresh) usługi Power BI, aby skonfigurować odświeżanie przyrostowe dla zestawu danych. Dodaj parametry **RangeStart** i **RangeEnd,** jak pokazano na poniższym zrzucie ekranu:

   ![Konfigurowanie parametrów zakresu](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Ponieważ zestaw danych ma kolumnę Data, która jest w formie tekstowej, **RangeStart** i **RangeEnd** parametry powinny zostać przekształcone, aby użyć następującego filtru. W okienku **Edytor zaawansowany** zmodyfikuj kwerendę, dodaj następujący tekst, aby filtrować wiersze na podstawie parametrów RangeStart i RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   W zależności od tego, która kolumna i typ danych są obecne w źródłowym zestawie danych, można odpowiednio zmienić pola RangeStart i RangeEnd

   
   |Właściwość  |Typ danych  |Filtr  |
   |---------|---------|---------|
   |_ts     |   Liczbowe      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime(1970, 1, 1, 0, 0,0)) i [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0)))       |
   |Data (na przykład:- 2019-08-19)     |   Ciąg      | [Document.date]> DateTime.ToText(RangeStart,"yyy-MM-dd") i [Document.date] < DateTime.ToText(RangeEnd,"yyy-MM-dd")        |
   |Data (na przykład:- 2019-08-11 12:00:00)   |  Ciąg       |  [Document.date]> DateTime.ToText(RangeStart", yyy-mm-dd HH:mm:ss") i [Document.date] < DateTime.ToText(RangeEnd,"yyy-mm-dd HH:mm:ss")       |


1. **Zdefiniuj zasady odświeżania** — zdefiniuj zasady odświeżania, przechodząc do karty **Odświeżanie przyrostowe w** menu **kontekstowym** tabeli. Ustaw zasady odświeżania, aby odświeżyć **codziennie** i przechowywać dane z ostatniego miesiąca.

   ![Definiowanie zasad odświeżania](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Zignoruj ostrzeżenie, które mówi, że *kwerenda M nie może zostać potwierdzona do złożenia*. Łącznik usługi Azure Cosmos DB składa zapytania filtrów.

1. **Załaduj dane i wygeneruj raporty** — korzystając z danych załadowanych wcześniej, utwórz wykresy do raportowania temperatury i opadów deszczu.

   ![Ładowanie danych i generowanie raportu](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Publikowanie raportu** w usłudze Power BI premium — ponieważ odświeżanie przyrostowe jest funkcją tylko premium, okno dialogowe publikowania umożliwia wybór obszaru roboczego na pojemność Premium. Pierwsze odświeżanie może trwać dłużej ze względu na konieczność zaimportowania danych historycznych. Kolejne odświeżanie danych jest znacznie szybsze, ponieważ używają odświeżania przyrostowego.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Łącznik usługi Power BI Azure Analysis + usługi Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Pochłonienie danych pogodowych do usługi Azure Cosmos DB 

Skonfiguruj potok pozyskiwania, aby załadować [dane pogodowe](https://catalog.data.gov/dataset/local-weather-archive) do usługi Azure Cosmos DB. Można skonfigurować zadanie usługi Azure Data Factory(ADF), aby okresowo ładować najnowsze dane pogodowe do usługi Azure Cosmos DB przy użyciu źródła HTTP i usługi Cosmos DB Sink.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Łączenie usług Azure Analysis Services z kontem usługi Azure Cosmos

1. **Utwórz nowy klaster** - usług Azure Analysis Services[Utwórz wystąpienie usług Usługi Azure Analysis](../analysis-services/analysis-services-create-server.md) w tym samym regionie co konto usługi Azure Cosmos i klaster Databricks.

1. **Utwórz nowy projekt tabelaryczny usług Analysis Services w programie Visual Studio** -  [Zainstaluj narzędzia danych programu SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) i utwórz projekt tabelaryczny usług Analysis Services w programie Visual Studio.

   ![Tworzenie projektu usługi Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Wybierz **wystąpienie zintegrowanego obszaru roboczego** i ustaw poziom zgodności na **SQL Server 2017 / Usługi Azure Analysis Services (1400)**

   ![Projektant modeli tabelarów usług Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Dodaj źródło danych usługi Azure Cosmos DB** — przejdź do nowych**źródeł** > **danych** **modeli**> i dodaj źródło danych usługi Azure Cosmos DB, jak pokazano na poniższym zrzucie ekranu:

   ![Dodawanie źródła danych usługi Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Połącz się z usługą Azure Cosmos DB, podając **identyfikator URI konta,** **nazwę bazy danych**i nazwę **kontenera**. Teraz możesz zobaczyć dane z kontenera usługi Azure Cosmos są importowane do usługi Power BI.

   ![Podgląd danych usługi Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Konstruuj model Usług Analysis Services** — otwórz edytor zapytań, wykonaj wymagane operacje w celu optymalizacji załadowanego zestawu danych:

   * Wyodrębnij tylko kolumny związane z pogodą (temperatura i opady)

   * Wyodrębnij informacje o miesiącu z tabeli. Te dane są przydatne w tworzeniu partycji, jak opisano w następnej sekcji.

   * Konwertowanie kolumn temperatury na liczbę

   Wynikowe wyrażenie M jest następujące:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Ponadto zmień typ danych kolumn temperatury na dziesiętny, aby upewnić się, że wartości te można wykreślić w usłudze Power BI.

1. **Tworzenie partycji analizy platformy Azure** — tworzenie partycji w usługach Analizy platformy Azure, aby podzielić zestaw danych na partycje logiczne, które można odświeżyć niezależnie i z różnymi częstotliwościami. W tym przykładzie utworzysz dwie partycje, które podzielą zestaw danych na dane z ostatniego miesiąca i wszystko inne.

   ![Tworzenie partycji usług analitycznych](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Utwórz następujące dwie partycje w usługach Azure Analysis Services:

   * **Ostatni miesiąc** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Historyczne** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Wdrażanie modelu na serwerze analizy platformy Azure** — kliknij prawym przyciskiem myszy projekt usługi Azure Analysis Services i wybierz polecenie **Wdrażanie**. Dodaj nazwę serwera w okienku **właściwości serwera wdrażania.**

   ![Wdrażanie modelu usług Analizy Platformy Azure](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Konfigurowanie odświeżania i scalania partycji** — usługa Azure Analysis Services umożliwia niezależne przetwarzanie partycji. Ponieważ chcemy, aby partycja **Ostatni miesiąc** była stale aktualizowana o najnowsze dane, ustaw interwał odświeżania na 5 minut. Nie jest wymagane odświeżanie danych w partycji historycznej. Ponadto należy napisać kod, aby skonsolidować partycję ostatniego miesiąca do partycji historycznej i utworzyć nową partycję ostatniego miesiąca.


## <a name="connect-power-bi-to-analysis-services"></a>Łączenie usługi Power BI z usługami Analysis Services

1. **Połącz się z serwerem analizy platformy Azure przy użyciu łącznika bazy danych usług Azure Analysis Services** — wybierz tryb na **żywo** i połącz się z wystąpieniem usługi Azure Analysis Services, jak pokazano na poniższym zrzucie ekranu:

   ![Pobierz dane z usług Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Załaduj dane i generuj raporty** — korzystając z danych załadowanych wcześniej, utwórz wykresy do raportowania temperatury i opadów deszczu. Ponieważ tworzysz połączenie na żywo, kwerendy powinny być wykonywane na podstawie danych w modelu usługi Azure Analysis Services, które zostały wdrożone w poprzednim kroku. Wykresy temperatury zostaną zaktualizowane w ciągu pięciu minut po załadowaniu nowych danych do usługi Azure Cosmos DB.

   ![Ładowanie danych i generowanie raportów](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o usłudze Power BI, zobacz [Wprowadzenie do usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Łączenie aplikacji Qlik Sense z usługą Azure Cosmos DB i wizualizowanie danych](visualize-qlik-sense.md)