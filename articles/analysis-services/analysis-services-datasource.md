---
title: Źródła danych obsługiwane w usługach Azure Analysis Services | Dokumenty firmy Microsoft
description: W tym artykule opisano źródła danych i łączniki obsługiwane dla modeli danych tabelaryczne 1200 i wyższe w usłudze Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461661"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Źródła danych obsługiwane w usługach Azure Analysis Services.

Źródła danych i łączniki pokazane w Kreatorze pobierania danych lub importu tabel w programie Visual Studio z projektami analysis services są wyświetlane zarówno dla usług Azure Analysis Services, jak i usług SQL Server Analysis Services. Jednak nie wszystkie źródła danych i łączniki pokazane są obsługiwane w usłudze Azure Analysis Services. Typy źródeł danych, z którymi można się połączyć, zależą od wielu czynników, takich jak poziom zgodności modelu, dostępne łączniki danych, typ uwierzytelniania i obsługa lokalnej bramy danych. W poniższych tabelach opisano obsługiwane źródła danych dla usług Azure Analysis Services.

## <a name="azure-data-sources"></a>Źródła danych na platformie Azure

|Źródło danych  |W pamięci  |Tryb DirectQuery  |Uwagi |
|---------|---------|---------|---------|
|Azure SQL Database      |   Tak      |    Tak      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Usługa Azure Synapse Analytics (magazyn danych SQL)      |   Tak      |   Tak       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   Tak       |    Nie      | <sup>[1](#tab1400a)</sup> |
|Azure Table Storage     |   Tak       |    Nie      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Tak        |  Nie        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Tak       |    Nie      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Tak       |    Nie      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|Usługi HDF usługi Azure HDInsight    |     Tak     |   Nie       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Tak       |   Nie       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Notatki:**   
<a name="tab1400a">1</a> - Tylko modele tabelaryczne 1400 i wyższe.  
<a name="azprovider">2</a> - Po określeniu jako źródło danych *dostawcy* w modelach tabelarycznym 1200 i nowszych, zarówno modele w pamięci, jak i zapytania bezpośrednie wymagają sterownika bazy danych Ole DB firmy Microsoft dla programu SQL Server MSOLEDBSQL (zalecane), klienta macierzystego programu SQL Server 11.0 lub dostawcy danych .NET Framework dla programu SQL Server.    
<a name="azsqlmanaged">3</a> — obsługiwane jest wystąpienie zarządzanej bazy danych SQL platformy Azure. Ponieważ wystąpienie zarządzane jest uruchamiane w sieci wirtualnej platformy Azure z prywatnym adresem IP, publiczny punkt końcowy musi być włączony w wystąpieniu. Jeśli opcja ta nie jest włączona, wymagana jest [lokalna brama danych.](analysis-services-gateway.md)    
<a name="databricks">4</a> — usługa Azure Databricks przy użyciu łącznika platformy Spark nie jest obecnie obsługiwana.   
<a name="gen2">5</a> — łącznik ADLS Gen2 nie jest obecnie obsługiwany, jednak łącznik usługi Azure Blob Storage może być używany ze źródłem danych ADLS Gen2.   

## <a name="other-data-sources"></a>Inne źródła danych

|Źródło danych | W pamięci | Tryb DirectQuery |Uwagi   |
|  --- | --- | --- | --- |
|Baza danych programu Access     |  Tak | Nie |  |
|Usługa Active Directory     |  Tak | Nie | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Tak | Nie |  |
|System platformy analitycznej     |  Tak | Nie |  |
|Plik CSV  |Tak | Nie |  |
|Dynamics 365     |  Tak | Nie | <sup>[6](#tab1400b)</sup> |
|Skoroszyt programu Excel     |  Tak | Nie |  |
|Exchange      |  Tak | Nie | <sup>[6](#tab1400b)</sup> |
|Folder      |Tak | Nie | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Tak | Nie |  |
|Dokument JSON      |  Tak | Nie | <sup>[6](#tab1400b)</sup> |
|Wiersze z binarnych      | Tak | Nie | <sup>[6](#tab1400b)</sup> |
|Baza danych MySQL     | Tak | Nie |  |
|Źródło danych OData      |  Tak | Nie | <sup>[6](#tab1400b)</sup> |
|Zapytanie ODBC     | Tak | Nie |  |
|OLE DB     |   Tak | Nie |  |
|Oracle  | Tak  |Tak  | <sup>[9](#oracle)</sup> |
|Baza danych PostgreSQL   | Tak | Nie | <sup>[6](#tab1400b)</sup> |
|Obiekty Salesforce|  Tak | Nie | <sup>[6](#tab1400b)</sup> |
|Raporty usługi Salesforce |Tak | Nie | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Tak | Nie |  |
|SAP Business Warehouse    |  Tak | Nie | <sup>[6](#tab1400b)</sup> |
|Listy programu SharePoint      |   Tak | Nie | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Tak   | Tak  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> | 
|Magazyn danych programu SQL Server |Tak   | Tak  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Baza danych Sybase     |  Tak | Nie |  |
|Teradata | Tak  | Tak  | <sup>[10](#teradata)</sup> |
|Plik TXT  |Tak | Nie |  |
|Tabela XML    |  Tak | Nie | <sup>[6](#tab1400b)</sup> |
| | | |

**Notatki:**   
<a name="tab1400b">6</a> - Tylko modele tabelaryczne 1400 i wyższe.  
<a name="sqlim">7</a> - Po określeniu jako źródło danych *dostawcy* w modelach tabelarycznym 1200 i wyższych, określ sterownik bazy danych OLE FIRMY Microsoft dla programu SQL Server MSOLEDBSQL (zalecane), klienta macierzystego programu SQL Server 11.0 lub .NET Framework Data Provider dla programu SQL Server.  
<a name="instgw">8</a> - Jeśli określaszudOLEDBSQL jako dostawcy danych, może być konieczne pobranie i zainstalowanie [sterownika bazy danych OLE FIRMY Microsoft dla programu SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) na tym samym komputerze co lokalna brama danych.  
<a name="oracle">9</a> - W przypadku modeli tabelaryczne 1200 lub jako źródło danych *dostawcy* w tabelarycznym modelu 1400+ określ dostawcę danych Oracle dla platformy .NET.  
<a name="teradata">10</a> - Dla modeli tabelaryczne 1200 lub jako źródło danych *dostawcy* w tabelaryczne 1400+ modeli, określ Teradata Dostawcy danych dla .NET.   
<a name="filesSP">11</a> - Pliki w lokalnym programie SharePoint nie są obsługiwane.

Łączenie się z lokalnymi źródłami danych z serwera usług Azure Analysis Services wymaga [bramy lokalnej.](analysis-services-gateway.md) Podczas korzystania z bramy wymagane są dostawcy 64-bitowi. 

## <a name="understanding-providers"></a>Zrozumienie dostawców

Podczas tworzenia tabelaryczne 1400 i wyższe projekty modelu w programie Visual Studio, domyślnie nie należy określić dostawcy danych podczas łączenia się ze źródłem danych przy użyciu **Pobierz dane**. Modele w tabeli 1400 i nowsze używają łączników [dodatku Power Query](/power-query/power-query-what-is-power-query) do zarządzania połączeniami, zapytaniami o dane i mashupami między źródłem danych a usługami Analysis Services. Są one czasami określane jako *połączenia ze strukturalnym* źródłem danych w tym ustawienia właściwości połączenia są ustawione dla Ciebie. Można jednak włączyć starsze źródła danych. Po włączeniu można użyć **Kreatora importu tabel,** aby połączyć się z niektórymi źródłami danych tradycyjnie obsługiwanymi w tabelarycznym 1200 i niższych modelach jako *starsze*źródła danych lub źródła danych *dostawcy.* Po określeniu jako źródło danych dostawcy można określić określonego dostawcy danych i inne zaawansowane właściwości połączenia. Na przykład można połączyć się z lokalnym magazynem danych programu SQL Server lub nawet z usługą Azure SQL Database jako starsze źródło danych. Następnie można wybrać sterownik ole db dla dostawcy danych SQL Server MSOLEDBSQL. W takim przypadku wybranie dostawcy danych OLE DB może zapewnić lepszą wydajność za pomocą łącznika dodatku Power Query. 

Korzystając z Kreatora importu tabel w programie Visual Studio, połączenia z dowolnym źródłem danych wymagają dostawcy danych. Domyślny dostawca danych jest wybrany dla Ciebie. W razie potrzeby można zmienić dostawcę danych. Typ wybranego dostawcy może zależeć od wydajności, od tego, czy model używa magazynu w pamięci, czy zapytania bezpośredniego i platformy usług Analysis Services, na której wdrażasz model.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Określanie źródeł danych dostawcy w tabelarycznym 1400 i wyższych projektach modelu

Aby włączyć źródła danych dostawcy, w programie Visual Studio kliknij pozycję **Narzędzia** > **Opcje** > analizy opcji**Importowanie danych****tabelaryczne** > , wybierz pozycję Włącz starsze źródła **danych**.

![Włączanie starszych źródeł danych](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Po włączeniu starszych źródeł danych w **Eksploratorze modelu tabelaryczne**kliknij prawym przyciskiem myszy **pozycję Źródło** > danych**Importuj ze źródła danych (Starsza wersja)**.

![Starsze źródła danych w Eksploratorze modeli tabelaryczne](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Podobnie jak w przypadku modeli tabelaryczne 1200, użyj **Kreatora importu tabel,** aby połączyć się ze źródłem danych. Na stronie łączenia kliknij pozycję **Zaawansowane**. Określ dostawcę danych i inne ustawienia połączenia w **obszarze Ustaw właściwości zaawansowane**.

![Starsze źródła danych Zaawansowane właściwości](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Personifikacja
W niektórych przypadkach może być konieczne określenie innego konta personifikacji. Konto personifikacji można określić w programie Visual Studio lub SSMS.

W przypadku lokalnych źródeł danych:

* W przypadku korzystania z uwierzytelniania SQL personifikacji powinno być konto usługi.
* Jeśli korzystasz z uwierzytelniania systemu Windows, ustaw użytkownika/hasło systemu Windows. W przypadku programu SQL Server uwierzytelnianie systemu Windows przy użyciu określonego konta personifikacji jest obsługiwane tylko w przypadku modeli danych w pamięci.

W przypadku źródeł danych w chmurze:

* W przypadku korzystania z uwierzytelniania SQL personifikacji powinno być konto usługi.

## <a name="oauth-credentials"></a>Poświadczenia OAuth

W przypadku modeli tabelaralnych na poziomie zgodności 1400 i wyższym przy użyciu trybu w pamięci usługa Azure SQL Database, usługa Azure Synapse Analytics (MAGAZYN DANYCH SQL), Dynamics 365 i lista programu SharePoint obsługują poświadczenia OAuth. Usługa Azure Analysis Services zarządza odświeżaniem tokenów dla źródeł danych OAuth, aby uniknąć przesuń czasu dla długotrwałych operacji odświeżania. Aby wygenerować prawidłowe tokeny, ustaw poświadczenia przy użyciu usługi SSMS.

Tryb direct query nie jest obsługiwany z poświadczeniami OAuth.

## <a name="next-steps"></a>Następne kroki
[Brama lokalna](analysis-services-gateway.md)   
[Zarządzanie serwerem](analysis-services-manage.md)   

