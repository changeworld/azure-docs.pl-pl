---
title: Uaktualnij Azure Data Lake Storage z Gen1 do Gen2
description: Uaktualnij Azure Data Lake Storage z Gen1 do Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 11/19/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 41074561b4805fef1889bd889b625e1a59d57d91
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327879"
---
# <a name="upgrade-azure-data-lake-storage-from-gen1-to-gen2"></a>Uaktualnij Azure Data Lake Storage z Gen1 do Gen2

Jeśli używasz Azure Data Lake Storage Gen1 w rozwiązaniach do analizy danych Big Data, ten przewodnik pomoże Ci uaktualnić te rozwiązania do korzystania z Azure Data Lake Storage Gen2. W tym dokumencie można użyć do oceny, zależności, które rozwiązanie ma na Data Lake Storage Gen1. Ten przewodnik pokazuje także sposobu planowania i przeprowadzić uaktualnienie.

Pomożemy Ci przez następujące zadania:

: heavy_check_mark: ocenić gotowość do uaktualnienia

: heavy_check_mark: Planowanie uaktualnienia

: heavy_check_mark: uaktualnienie

## <a name="assess-your-upgrade-readiness"></a>Oceń gotowość do uaktualnienia

Naszym celem jest, że wszystkie funkcje, które znajdują się w Data Lake Storage Gen1 zostanie udostępniona w Data Lake Storage Gen2. Jak te funkcje są udostępniane np. w zestawie SDK, interfejsu wiersza polecenia itd., mogą się różnić między Data Lake Storage Gen1 i Data Lake Storage Gen2. Aplikacje i usługi działające z usługą Data Lake Storage Gen1 muszą mieć możliwość działają podobnie, za pomocą Data Lake Storage Gen2. Ponadto niektóre funkcje nie będzie dostępny w Data Lake Storage Gen2 natychmiast. Po ich udostępnieniu ogłosimy ich w tym dokumencie.

Następujące sekcje dalej pomoże wybrać najlepszy sposób uaktualnić do Data Lake Storage Gen2 i kiedy może być najbardziej przydatne, aby to zrobić.

### <a name="data-lake-storage-gen1-solution-components"></a>Składniki rozwiązania Gen1 magazynu jeziora danych

Najprawdopodobniej korzystając z programu Data Lake Storage Gen1 w ramach rozwiązania do analizy lub potoków, istnieje wiele dodatkowych technologii, które zostanie zastosowana do osiągnięcia funkcji ogólne end-to-end. W tym [artykule](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) opisano różne składniki przepływu danych, które obejmują pozyskiwanie, przetwarzanie i zużywanie danych.

Ponadto istnieją składniki przekrojowe aprowizację, zarządzanie i monitorowanie tych składników. Poszczególne składniki pracują z Data Lake Storage Gen1 przy użyciu interfejsu najlepiej nadaje się do nich. Gdy planowane jest uaktualnienie rozwiązania do Data Lake Storage Gen2, należy mieć świadomość interfejsów, które są używane. Należy uaktualnić zarówno interfejsy zarządzania jak i interfejsy danych, ponieważ każdy interfejs mają różne wymagania.

![Składniki rozwiązania Lake magazynu danych](./media/data-lake-storage-upgrade/solution-components.png)

Powyższy **rysunek 1** pokazuje składniki funkcjonalności, które zobaczysz w większości rozwiązań analitycznych.

Na **rysunku 2** przedstawiono przykład sposobu implementowania tych składników przy użyciu określonych technologii.

Funkcja przechowywania w programie **rysunek 1** jest udostępniana przez Data Lake Storage Gen1 (**rysunek 2**). Należy zwrócić uwagę interakcje różnych składników przepływu danych z Data Lake Storage Gen1 przy użyciu interfejsów API REST lub zestawu SDK języka Java. Należy również zauważyć, jak interakcji składników funkcji przekrojowe Data Lake Storage Gen1. Składnik aprowizacji używa szablonów zasobów platformy Azure, natomiast składnik monitorowania, który używa dzienników Azure Monitor, wykorzystuje dane operacyjne pochodzące z Data Lake Storage Gen1.

Aby uaktualnić to rozwiązanie z przy użyciu Data Lake Storage Gen1 do Data Lake Storage Gen2, należy skopiować dane i metadane, ponowne utworzenie punktu zaczepienia przepływów danych, a następnie wszystkie składniki będą musiały mieć możliwość pracy z Data Lake Storage Gen2.

Poniższe sekcje zawierają informacje ułatwiające podejmowanie lepszych decyzji:

: heavy_check_mark: możliwości platformy

: heavy_check_mark: interfejsy programowania

: heavy_check_mark: ekosystem platformy Azure

: heavy_check_mark: ekosystem partnerów

: heavy_check_mark: informacje operacyjne

W każdej sekcji można określić "musi-identyfikatorów istniejących" dla aktualizacji. Jeśli masz pewność, że możliwości są dostępne, lub masz pewność, że istnieją odpowiednie obejścia, przejdź do sekcji [Planowanie uaktualnienia](#planning-for-an-upgrade) tego przewodnika.

### <a name="platform-capabilities"></a>Możliwości platformy

W tej sekcji opisano, które Data Lake Storage Gen1 możliwości platformy, które są obecnie dostępne w Data Lake Storage Gen2.

| | Usługa Data Lake Storage 1. generacji | Data Lake Storage Gen2 — cel | Data Lake Storage Gen2 — stan dostępności  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Dane organizacji| Obsługuje dane przechowywane jako pliki i foldery. | Program obsługuje dane przechowywane jako obiekty/elementy BLOB, a także foldery i pliki — [link](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Obsługuje dane przechowywane jako foldery i pliki — *teraz dostępne* <br><br> Obsługuje dane przechowywane jako obiekty/obiektów blob, które *nie są jeszcze dostępne* |
| Przestrzeń nazw| Hierarchiczna | Hierarchiczna |  *Dostępne teraz*  |
| Interfejs API  | Interfejs API REST przy użyciu protokołu HTTPS | Interfejs API REST za pośrednictwem protokołu HTTP/HTTPS| *Dostępne teraz* |
| Interfejs API po stronie serwera| [Interfejs API REST zgodny z WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Interfejs API REST usługi Azure Blob Service [Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Interfejs API REST Data Lake Storage Gen2 — *teraz jest dostępny* <br><br> Interfejs API REST usługi Azure Blob Service — *teraz jest dostępny*       |
| Klient systemu plików usługi Hadoop | Tak ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Tak ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Dostępne teraz*  |  
| Operacje na danych — autoryzacji  | Plików i folderów na poziomie POSIX list kontroli dostępu (ACL) oparte na usługi Azure Active Directory tożsamości  | Na poziomie plików i folderów w systemie POSIX Access Control List (ACL) na podstawie tożsamości Azure Active Directory [udostępnienia klucza](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) dla ról autoryzacji na poziomie konta Access Control ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) w celu uzyskania dostępu do kontenerów | *Dostępne teraz* |
| Operacje na danych — dzienniki  | Yes | Yes | *Dostępne teraz* (wersja zapoznawcza). Zobacz [znane problemy](data-lake-storage-known-issues.md).<br><br> Integracja monitorowania platformy Azure — *nie jest jeszcze dostępna* |
| Szyfrowanie danych magazynowanych | Przezroczyste, po stronie serwera za pomocą kluczy zarządzanych przez usługę i za pomocą kluczy zarządzanych przez klienta w usłudze Azure KeyVault | Przezroczyste, po stronie serwera za pomocą kluczy zarządzanych przez usługę i za pomocą kluczy zarządzanych kluczy klienta w usłudze Azure KeyVault | Klucze zarządzane przez usługę — *teraz dostępne*<br><br> Klucze zarządzane przez klienta — *teraz dostępne*  |
| Operacje zarządzania (np. Tworzenie konta) | [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) udostępniana przez platformę Azure do zarządzania kontami | [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) udostępniana przez platformę Azure do zarządzania kontami | *Dostępne teraz*|
| Zestawy SDK dla deweloperów | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| Zestaw BLOB SDK — *dostępny teraz*. Zestaw Azure Data Lake Storage Gen2 SDK — *nie jest jeszcze dostępny*  |
| |Optymalizacji wydajności dla obciążeń analizy równoległej. Wysoka przepływność i operacje We/Wy. | Optymalizacji wydajności dla obciążeń analizy równoległej. Wysoka przepływność i operacje We/Wy. | *Dostępne teraz* |
| Obsługa Virtual Network (VNet)  | [Korzystanie z integracji Virtual Network](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Korzystanie z punktu końcowego usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Dostępne teraz* |
| Limity rozmiaru | Brak limitów rozmiarów kont, rozmiarów plików lub liczby plików | Brak limitów rozmiarów kont lub liczbę plików. Rozmiar pliku jest ograniczony do 5TB. | *Dostępne teraz*|
| Nadmiarowość geograficzna| Lokalnie nadmiarowy (LRS) | Lokalnie nadmiarowy (LRS) strefa nadmiarowa (ZRS) Geograficznie nadmiarowy (GRS) z dostępem do odczytu (RA-GRS) — zobacz [tutaj](https://docs.microsoft.com/azure/storage/common/storage-redundancy) , aby uzyskać więcej informacji| *Dostępne teraz* |
| Dostępność regionalna | Zobacz [tutaj](https://azure.microsoft.com/regions/) | Wszystkie [regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Dostępne teraz*                                                                                                                           |
| Cena                                       | Zobacz [Cennik](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Zobacz [Cennik](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Dostępność umowy SLA                            | [Zobacz umowa SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Zobacz umowa SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Dostępne teraz*                                                                                                                           |
| Zarządzanie danymi                             | Wygaśnięcie pliku                                                                                                                                        | Zasady cyklu życia                                                                                                                                                                                                                                                                                                                                                                                                          | Dostępne są *teraz* zasady cyklu życia (wersja zapoznawcza). Zobacz [znane problemy](data-lake-storage-known-issues.md).                                                                                                                     |

### <a name="programming-interfaces"></a>Interfejsy programowania

W tej tabeli opisano ustawiająca interfejsu API, które są dostępne dla niestandardowych aplikacji. Obsługa zestawu SDK dla operacji na poziomie katalogu nie jest jeszcze obsługiwana.

|  Zestaw API                           |  Usługa Data Lake Storage 1. generacji                                                                                                                                                                                                                                                                                                   | Dostępność Data Lake Storage Gen2 — za pomocą uwierzytelniania klucza wspólnego | Dostępność Data Lake Storage Gen2 — za pomocą uwierzytelniania OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zestaw SDK .NET                  | [Link](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet) | *Dostępne teraz* | *Dostępne teraz* |
| Zestaw SDK Java                | [Link](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Dostępne teraz*                                                      | *Dostępne teraz*                                     |
| Node.js                |   [łącza](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                               | *Dostępne teraz*                                                     | *Dostępne teraz*                                                                                           |
| Zestaw SDK dla języka Python                   |   [łącza](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                | *Dostępne teraz*                                                      | *Dostępne teraz*                                        |
| Interfejs API REST                 | [Link](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Dostępne teraz*                                                      | *Dostępne teraz*                                                                                                                                               |
| PowerShell | [Link](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | *Dostępne teraz* |
| Interfejs wiersza polecenia                 | [Link](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Dostępne teraz*                                                      | *Dostępne teraz*                                                               |
| Szablony usługi Azure Resource Manager — Zarządzanie             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Dostępne teraz*                                                      | *Dostępne teraz*                                          |

### <a name="azure-ecosystem"></a>Ekosystem platformy Azure

Korzystając z programu Data Lake Storage Gen1, można użyć różnych produktów i usług firmy Microsoft w potoków end-to-end. Te usługi i produkty pracować Data Lake Storage Gen1 bezpośrednio lub pośrednio. Ta tabela zawiera listę usług, firma Microsoft została zmodyfikowana, aby pracować z Data Lake Storage Gen1 i pokazuje, które z nich jest obecnie zgodna z Data Lake Storage Gen2.

| **Obszar**             | **Dostępność Data Lake Storage Gen1**                                                                                                                                    | **Dostępność dla Data Lake Storage Gen2 — z uwierzytelnianiem za pomocą klucza współużytkowanego**                                                                                                           | **Dostępność dla Data Lake Storage Gen2 — z uwierzytelnianiem OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Framework analizy  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Dostępne teraz*                                                                                                                                                              | *Dostępne teraz*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [Hdinsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3,6 — *dostępna teraz* Usługa HDInsight 4,0 — *nie jest jeszcze dostępna*      | Usługa HDInsight 3,6 ESP — *dostępna teraz* <br><br>  Usługa HDInsight 4,0 ESP — *nie jest jeszcze dostępna*                                                                 |
|                      | Środowisko uruchomieniowe usługi Databricks 3.1 lub nowszej                                                                                                                                               | [Databricks Runtime 5,1 i nowsze](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *— dostępne teraz* | [Databricks Runtime 5,1 i nowsze](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) — *teraz dostępne*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Nieobsługiwane*                                                                                                                                                              | [Link](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) *dostępne teraz* |
| Integracja danych     | [Fabryka danych](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Wersja 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) — wersja *dostępna teraz* 1 — *nieobsługiwana*                               | [Wersja 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) — *teraz dostępna* <br><br> Wersja 1 — *nieobsługiwane*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Nieobsługiwane*                                                                                                                                                              | *Nieobsługiwane*                                                                                                                                 |
|                      | [Usługi SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Dostępne teraz*                                                                                                                                                          | *Dostępne teraz*                                                                                                                             |
|                      | [Wykaz danych](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Dostępne teraz*                                                                                                                                                          | *Dostępne teraz*                                                                                                                             |
| IoT                  | [Event Hubs — Przechwyć](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Dostępne teraz*                                                                                                                                                          | *Dostępne teraz*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Dostępne teraz*                                                                                                                                                          | *Dostępne teraz*                                                                                                                             |
| Zużycie          | [Program Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Dostępne teraz*                                                                                                                                                          | *Dostępne teraz*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
| Produktywność         | [Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Nieobsługiwane*                                                                                                                                                              | Zarządzanie kontami *— teraz dostępne* <br><br>Operacje na danych *—*  *nie są jeszcze dostępne*                                                                   |
|                      | [Narzędzia Data Lake Tools for Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
|                      | [Azure Storage Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Dostępne teraz*                                                                                                                                                              | *Dostępne teraz*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |

### <a name="partner-ecosystem"></a>Ekosystem partnerów

Poniższa tabela pokazuje listę usług innych firm i produktów, które zostały zmodyfikowane, aby pracować z Data Lake Storage Gen1. Pokazuje również, które nie są obecnie zgodna z Data Lake Storage Gen2.

| Obszar            | Partner  | Produkt/usługę  | Dostępność usługi Data Lake Storage Gen1                                                                                                                                               | Dostępność Data Lake Storage Gen2 — za pomocą uwierzytelniania klucza wspólnego                                                   | Dostępność usługi Data Lake Storage Gen2 — przy użyciu protokołu Oauth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Framework analizy | Cloudera     | PROGRAMU CDH                  | [Link](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Jeszcze niedostępne*                                                                                                  | [Link](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Link](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Nieobsługiwane*                                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              |   [łącza](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                      | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | Qubole       |                      | [Link](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
| ETL                 | StreamSets   |                      | [Link](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | Informatica  |                      | [Link](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | Firmy Attunity     |                      | [Link](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | Alteryx      |                      | [Link](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | ImanisData   |                      | [Link](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | Platforma WANdisco     |                      | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Informacje operacyjne

Data Lake Storage Gen1 wypycha szczegółowe informacje i dane do innych usług, które ułatwia operacjonalizować potoków. W poniższej tabeli przedstawiono dostępność odpowiedniej pomocy technicznej w Data Lake Storage Gen2.

| Typ danych                                                                                       | Dostępność usługi Data Lake Storage Gen1                                                                 | Dostępność usługi Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Dane dotyczące rozliczeń - liczniki, które są wysyłane do handlu zespołu do rozliczeń i następnie udostępniane klientom  | *Dostępne teraz*                                                                                             | *Dostępne teraz*                                                                                                                           |
| Dzienniki aktywności                                                                                          | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Jednokrotne żądania dzienników dla określonego czasu trwania przy użyciu biletu pomocy technicznej — *teraz dostępna* integracja z usługą Azure Monitoring — *nie jest jeszcze dostępna* |
| Dzienniki diagnostyczne                                                                                        | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | *Dostępne teraz* (wersja zapoznawcza). Zobacz [znane problemy](data-lake-storage-known-issues.md). <br>Integracja z monitorowaniem platformy Azure — *nie jest jeszcze dostępna* |
| Metryki                                                                                                | *Nieobsługiwane*                                                                                               | *Dostępne teraz —* [link](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planowanie uaktualnienia

W tej sekcji założono, że sekcja [Ocena gotowości do uaktualnienia](#assess-your-upgrade-readiness) w tym przewodniku została sprawdzona i zostały spełnione wszystkie zależności. W przypadku funkcji, które nadal nie są dostępne w Data Lake Storage Gen2 kontynuować tylko wtedy, gdy znasz odpowiednie rozwiązania. Poniższe sekcje zawierają wskazówki na jak można zaplanować dla uaktualnienie potoków. Wykonanie rzeczywistego uaktualnienia zostanie opisane w sekcji [wykonywanie uaktualnienia](#performing-the-upgrade) w tym przewodniku.

### <a name="upgrade-strategy"></a>Strategii uaktualniania

Najbardziej krytycznych części procesu uaktualniania, wybierają strategii. Ta decyzja określi opcje dostępne dla Ciebie.

Ta tabela zawiera listę dobrze znanych strategii, które zostały użyte do migracji baz danych, klastrów usługi Hadoop itd. Firma Microsoft przyjmuje podobne strategie w naszych wskazówkach i dostosowuje je do naszego kontekstu.

| Strategia                   | Specjaliści                                                                                  | Wady                                                           | Kiedy należy używać?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Lift-and-shift                 | Najprostsza.                                                                                 | Wymaga przestojów skopiowanie danych, przenoszenie zadania i przenoszenie przychodzący i wychodzący                                             | Prostsze rozwiązań w przypadku, gdy nie są wiele rozwiązań uzyskiwania dostępu do tego samego Gen1 konta, a więc można było przenieść razem w szybki sposób kontrolowany.                                                  |
| Kopiowania raz i kopii przyrostowej | Zmniejszenie przestojów, wykonując kopii w tle, gdy system źródłowy jest nadal aktywne. | Wymaga przestoju do przenoszenia ruchu przychodzącego i wychodzącego.                   | Ilość danych do skopiowania za pośrednictwem jest duży i przestój skojarzone z życia and-shift jest niedopuszczalny. Testy mogą być wymagane z danymi produkcyjnymi znaczące przed przejścia, w systemie docelowym. |
| Wdrożenie równoległe              | Co najmniej czas przestoju zezwala dla aplikacji przeprowadzić migrację według własnego uznania.           | Najbardziej rozbudowane od 2 sposób synchronizacji jest wymagany między dwoma systemami. | Dla złożonych scenariuszy, w którym aplikacji utworzonych na Data Lake Storage Gen1 nie może być jednocześnie jednorazowej migracji i przeniesiony w sposób przyrostowy.                                                      |

Poniżej przedstawiono szczegółowe informacje na temat czynności związanych dla każdej strategii. Kroki listę co możesz zrobić, za pomocą elementów związanych z uaktualnienia. Obejmuje to ogólny system źródłowy, całego systemu docelowego, źródła ruchu przychodzącego dla systemu źródłowego, docelowych ruchu wychodzącego dla systemu źródłowego, a zadania działające w systemie źródłowym.

Te kroki nie mają mieć charakter opisowy. Są one przeznaczone do ustaw platformę, temat, jak firma Microsoft myśl o każdej strategii. Firma Microsoft udostępni przypadków każdej strategii zgodnie z ich implementowanych widzimy.

#### <a name="lift-and-shift"></a>Lift-and-shift

1. Wstrzymaj w systemie źródłowym — źródeł danych przychodzących, zadania i miejsc docelowych ruchu wychodzącego.
2. Skopiuj wszystkie dane z systemu źródłowego do systemu docelowego.
3. Wszystkie źródła danych przychodzących, wskaż polecenie systemu docelowego. Wskaż miejsce docelowe ruchu wychodzącego z systemu docelowego.
4. Przenoszenie, modyfikowanie, uruchamiać wszystkie zadania w systemie docelowym.
5. Wyłącz komputer źródłowy.

#### <a name="copy-once-and-copy-incremental"></a>Kopiowanie — po i kopiowania przyrostowego

1. Skopiuj dane z systemu źródłowego do systemu docelowego.
2. Skopiuj dane przyrostowe z systemu źródłowego do systemu docelowego w regularnych odstępach czasu.
3. Wskaż miejsce docelowe ruchu wychodzącego z systemu docelowego.
4. Przenoszenie, modyfikowanie, uruchamiać wszystkie zadania w systemie docelowym.
5. Wskaż źródeł danych przychodzących przyrostowo systemu docelowego, zgodnie z Twojej wygody.
6. Gdy wszystkie źródła danych przychodzących wskazują systemu docelowego.
    1. Wyłącz przyrostowe kopiowanie.
    2. Wyłącz komputer źródłowy.

#### <a name="parallel-adoption"></a>Wdrożenie równoległe

1. Skonfiguruj system docelowy.
2. Skonfigurować pod kątem zapewnienia dwukierunkowej replikacji między system źródłowy i docelowy.
3. Wskaż źródeł danych przychodzących przyrostowo systemu docelowego.
4. Przenoszenie, modyfikowanie, stopniowo uruchamianie zadań w systemie docelowym.
5. Stopniowo wskaż miejsc docelowych ruchu wychodzącego z systemu docelowego.
6. Po wszystkich oryginalnych źródeł danych przychodzących, zadań i miejsce docelowe ruchu wychodzącego pracuje z systemu docelowego, należy wyłączyć w systemie źródłowym.

### <a name="data-upgrade"></a>Uaktualnianie danych

Ogólna strategia używana do przeprowadzania uaktualnienia (opisana w sekcji [strategia uaktualniania](#upgrade-strategy) w tym przewodniku) umożliwia określenie narzędzi, których można użyć do uaktualnienia danych. Narzędzia wymienione poniżej są oparte na bieżących informacji i są sugestie. 

#### <a name="tools-guidance"></a>Wskazówki dotyczące narzędzi

| Strategia                       | Narzędzia                                                                                                             | Specjaliści                                                                                                                             | Zagadnienia do rozważenia                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Podnieś i Przenieś**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Zarządzana usługa w chmurze                                                                                                                | Obecnie można kopiować zarówno dane, jak i listy ACL.                                                                                                                                                                                                                                                                      |
|                                    | [Pomocą distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Za pomocą tego narzędzia można skopiować dobrze znane narzędzia usługi Hadoop — pod warunkiem uprawnień, czyli list ACL                                                   | Wymaga klastra, które można podłączyć Gen2 i Data Lake Storage Gen1, w tym samym czasie.                                                                                                                                                                                   |
| **Kopiowanie-jednorazowe i kopiowanie przyrostowe** | Azure Data Factory                                                                                                    | Zarządzana usługa w chmurze                                                                                                                | Obecnie można kopiować zarówno dane, jak i listy ACL. Do obsługi przyrostowego kopiowania w usłudze ADF, dane muszą być uporządkowane w sposób szeregów czasowych. Najkrótszy interwał kopiowania przyrostowego to [15 minut](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Wdrożenie równoległe**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Obsługa replikacji spójnej czy przy użyciu czystego środowiska Hadoop podłączone do usługi Azure Data Lake Storage, obsługuje zapewnienia dwukierunkowej replikacji | Jeśli nie używa środowiska czystego Hadoop, prawdopodobnie opóźnienia w replikacji.                                                                                                                                                                                                                                                  |

Należy zauważyć, że istnieją inne firmy, które mogą obsłużyć Data Lake Storage Gen1 do Data Lake Storage Gen2 uaktualniania bez powyższego poziomu narzędzi do kopiowania danych/metadanych (na przykład: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Zapewniają one środowisko "kompleksowym", który wykonuje migracji danych, a także migrację obciążeń. Może być konieczne uaktualnienie out-of-band dla znajdujących się poza ich ekosystemy narzędzi.

#### <a name="considerations"></a>Zagadnienia do rozważenia

* Należy ręcznie utworzyć konta Data Lake Storage Gen2 najpierw przed rozpoczęciem dowolnej części procesu uaktualniania, ponieważ bieżące wskazówki nie obejmuje żadnych Gen2 konto procesu automatycznego na podstawie informacji konta Gen1. Należy porównać procesy tworzenia kont dla [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) i [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2, obsługuje tylko pliki rozmiarze maksymalnym rozmiarze 5 TB. W celu uaktualnienia do Data Lake Storage Gen2 może zajść potrzeba zmiany rozmiaru plików w Data Lake Storage Gen1, tak aby były mniejsze niż 5 TB.

* Jeśli nie chcesz kopiować listy ACL za pomocą narzędzia, która nie obejmuje kopiowania listy ACL, następnie należy do ustawiania listy kontroli dostępu na komputerze docelowym ręcznie na odpowiednie najwyższym poziomie. Możesz tworzyć, za pomocą Eksploratora usługi Storage. Upewnij się, że te listy ACL domyślne listy kontroli dostępu, aby pliki i foldery, kopiujących za pośrednictwem dziedziczy ich.

* W Data Lake Storage Gen1 najwyższego poziomu, można ustawić list ACL jest w katalogu głównym konta. W Data Lake Storage Gen2 jednak najwyższy poziom można ustawić listy ACL w folderze głównym w kontenerze, a nie w całym koncie. Dlatego jeśli chcesz ustawić domyślne listy kontroli dostępu na poziomie konta, należy te we wszystkich systemach plików na koncie usługi Data Lake Storage Gen2 zduplikowane.

* Ograniczenia nazewnictwa plików różnią się między dwoma systemami magazynowania. Te różnice są szczególnie dotyczące podczas kopiowania z Data Lake Storage Gen2 Data Lake Storage Gen1 ponieważ one bardziej ma ograniczone ograniczenia.

### <a name="application-upgrade"></a>Uaktualnienie aplikacji

Jeśli potrzebujesz do tworzenia aplikacji na Data Lake Storage Gen1 lub Data Lake Storage Gen2, musisz najpierw wybrać odpowiedni interfejs programowania. Podczas wywoływania interfejsu API w tym interfejsie musisz zapewnić odpowiedni identyfikator URI i odpowiednie poświadczenia. Reprezentacja te trzy elementy, API, identyfikator URI i jak poświadczenia są udostępniane, czy różnic między Data Lake Storage Gen1 i Data Lake Storage Gen2.So, jako część uaktualnienia aplikacji, konieczne będzie mapowanie tych trzech konstrukcje odpowiednio.

#### <a name="uri-changes"></a>Zmiany identyfikatora URI

Głównym zadaniem jest przetłumaczenie identyfikatora URI, który ma prefiks `adl://` w identyfikatorze URI, który ma prefiks `abfss://`.

Schemat identyfikatora URI dla Data Lake Storage Gen1 został szczegółowo opisany w [tym miejscu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) , ale ogólnie mówiąc, jest *adl://mydatalakestore.azuredatalakestore.net/\<FILE_PATH\>.*

Schemat identyfikatora URI służący do uzyskiwania dostępu do plików Data Lake Storage Gen2 jest wyjaśniony [tutaj](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) , ale ogólnie mówiąc, jest `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`.

Musisz przejść przez istniejące aplikacje i upewnić się, że zmiany zostały wprowadzone identyfikatory URI odpowiednio wskaż Data Lake Storage Gen2 te. Ponadto należy dodać odpowiednie poświadczenia. Na koniec jak wycofywanie oryginalnej aplikacji i Zamień na nową aplikację należy ściśle wyrównywana strategii ogólną uaktualnienia.

#### <a name="custom-applications"></a>Aplikacje niestandardowe

W zależności od interfejsu, używanych przez aplikację za pomocą programu Data Lake Storage Gen1 należy zmodyfikować, aby dostosować ją do Data Lake Storage Gen2.

##### <a name="rest-apis"></a>Interfejsy API REST

Jeśli aplikacja używa interfejsów API REST Data Lake Storage, należy zmodyfikować aplikację w celu użycia interfejsów API Data Lake Storage Gen2 REST. Linki są dostępne w sekcji *interfejsy programowania* .

##### <a name="sdks"></a>Zestawy SDK

Zgodnie z opisem w sekcji *Ocena gotowości do uaktualnienia* zestawy SDK nie są obecnie dostępne. Jeśli chcesz, aby port przez aplikacje był Data Lake Storage Gen2, zalecamy zaczekanie, aż obsługiwane zestawy SDK będą dostępne.

##### <a name="powershell"></a>PowerShell

Jak przywołane w oceny sekcji rozwiązania upgrade readiness, obsługa programu PowerShell nie jest obecnie dostępny dla płaszczyzny danych.

Polecenia cmdlet płaszczyzny zarządzania można zastąpić odpowiednie pozycje w Data Lake Storage Gen2. Linki są dostępne w sekcji *interfejsy programowania* .

##### <a name="cli"></a>Interfejs wiersza polecenia

Zgodnie z opisem w sekcji *Ocena gotowości do uaktualnienia* obsługa interfejsu wiersza polecenia nie jest obecnie dostępna dla płaszczyzny danych.

Można zastąpić poleceń płaszczyzny zarządzania odpowiednie pozycje w Data Lake Storage Gen2. Linki są dostępne w sekcji *interfejsy programowania* .

### <a name="analytics-frameworks-upgrade"></a>Uaktualnianie analizy struktury

Jeśli aplikacja tworzy metadane dotyczące informacji w magazynie, takie jak jawne pliku i ścieżki folderu, należy wykonać dodatkowe akcje po uaktualnieniu magazynu danych/meta-data. Jest to szczególnie istotne dla platform analizy, takich jak HDInsight Azure Databricks itp., które zwykle tworzą wykazu danych magazynu danych.

Analytics Platform pracować z danymi i metadane przechowywane w lokalizacjach zdalnych, takich jak Data Lake Storage Gen1 i Gen2. Tak teoretycznie silniki mogą być efemeryczne i zostać wznowione tylko wtedy, gdy zadania należy uruchamiać przechowywanych danych.

Jednak w celu zoptymalizowania wydajności, struktur analytics może utworzyć jawnego odwołania do plików i folderów przechowywanych w magazynie zdalnym, a następnie utwórz pamięci podręcznej w celu przechowania. Identyfikator URI danych zdalnych zmieni się, na przykład klaster, który został przechowywanie danych w Data Lake Storage Gen1 wcześniej, a teraz chcą przechowywać w Data Lake Storage Gen2, identyfikator URI dla tego samego skopiowanej zawartości może się różnić. Dlatego po danych i metadane uaktualnienia pamięci podręczne dla tych aparatów trzeba również można zaktualizować lub ponownie zainicjowane

W ramach procesu planowania należy do identyfikowania Twojej aplikacji i zorientować się, jak metadane informacje mogą być ponownie zainicjowane, aby wskazać dane, które są obecnie przechowywane w Data Lake Storage Gen2. Poniżej przedstawiono wskazówki dotyczące struktury powszechnie przyjętym analytics do udzielenia odpowiedzi na swoje kroki uaktualnienia.

#### <a name="azure-databricks"></a>Azure Databricks

W zależności od wybranej strategii uaktualniania kroki będą się różnić. Bieżącej sekcji przyjęto założenie, że została wybrana strategia "Life-and-shift". Ponadto istniejącego obszaru roboczego usługi Databricks, które umożliwiają dostęp do danych w ramach konta Data Lake Storage Gen1 powinien pracować z danymi, która jest kopiowana do konta Data Lake Storage Gen2.

Najpierw upewnij się, że po utworzeniu konta Gen2 i następnie skopiowanych danych i metadanych z Gen1 do Gen2 za pomocą odpowiedniego narzędzia. Te narzędzia są wywoływane w sekcji [uaktualnianie danych](#data-upgrade) w tym przewodniku.

Następnie [Uaktualnij](https://docs.azuredatabricks.net/user-guide/clusters/index.html) istniejący klaster datarangs, aby rozpocząć korzystanie z programu datakostks Runtime 5,1 lub nowszego, który powinien obsługiwać Data Lake Storage Gen2.

Kroki po tej dacie są oparte na jak istniejący obszar roboczy usługi Databricks uzyskuje dostęp do danych w ramach konta Data Lake Storage Gen1. Można to zrobić, wywołując identyfikatory URI adl:// [bezpośrednio](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) z notesów lub za pośrednictwem [mountpoints](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Jeśli uzyskujesz dostęp bezpośrednio z notesów, zapewniając pełny adl: / / identyfikatory URI, musisz przejść przez każdy Notes i zmień konfigurację, aby dostęp do odpowiedniego Data Lake Storage Gen2 identyfikatora URI.

Idąc dalej, musisz ponownie skonfigurować to ustawienie, aby konta Data Lake Storage Gen2. Są wymagane żadne zmiany więcej i notebooki powinien móc pracować jak wcześniej.

Jeśli używasz żadnego innymi strategiami uaktualnienia można utworzyć odmianą powyższe kroki zgodnie z wymaganiami.

### <a name="azure-ecosystem-upgrade"></a>Uaktualnienie ekosystem platformy Azure

Każdy z narzędzi i usług, które zostały wywołane w sekcji [ekosystem platformy Azure](#azure-ecosystem) tego przewodnika, będzie musiał zostać skonfigurowany do pracy z Data Lake Storage Gen2.

Najpierw upewnij się, że istnieje integracji udostępniono Data Lake Storage Gen2.

Następnie elementy wymienione powyżej (na przykład: identyfikator URI i poświadczeń), muszą zostać zmienione. Można zmodyfikować istniejące wystąpienie współdziałająca z usługą Data Lake Storage Gen1 lub można utworzyć nowe wystąpienie, które może pracować nad Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Uaktualnienie ekosystemu partnerów

Skontaktuj się z partnerem składników i narzędzi w celu upewnij się, że można pracować Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Przeprowadzania uaktualnienia

### <a name="pre-upgrade"></a>Przed uaktualnieniem

W ramach tego przewodnika nastąpiło przechodzenie przez sekcję *Ocena gotowości do uaktualnienia* i [zaplanowanie uaktualnienia w](#planning-for-an-upgrade) tym podręczniku, otrzymano wszystkie niezbędne informacje i utworzono plan, który spełnia Twoje potrzeby. Prawdopodobnie trzeba będzie testowania zadań w tej fazie.

### <a name="in-upgrade"></a>W uaktualnieniu

W zależności od strategii wybierzesz i komplikacje związane z jego rozwiązania, w tym etapie może być krótki co najmniej jeden rozszerzonej w przypadku, gdy istnieje wiele obciążeń oczekiwanie na przyrostowo przeniesieni do Data Lake Storage Gen2. Są to najbardziej krytyczna część uaktualnienia.

### <a name="post-upgrade"></a>Po zakończeniu uaktualniania.

Po wykonaniu operacji przejścia, ostatnie kroki będą obejmować dokładnej weryfikacji. Może to obejmować, ale nie będzie ograniczony do weryfikowania danych, które zostały wcześniej skopiowane, zweryfikowanie list ACL zostało prawidłowo ustawione. Sprawdzanie, czy kompleksowe potoki działają prawidłowo itd. Po zakończeniu weryfikacji możesz teraz wyłączyć stare potoki, usunąć źródłowe konta Data Lake Storage Gen1 i uzyskać pełną szybkość rozwiązań opartych na Data Lake Storage Gen2.

## <a name="conclusion"></a>Podsumowanie

Wskazówki zawarte w tym dokumencie powinny pomogły możesz uaktualnić swoje rozwiązania do używania Data Lake Storage Gen2. 

Jeśli masz więcej pytań lub masz opinię, podaj poniższe komentarze lub Prześlij opinię na [forum opinii na platformie Azure](https://feedback.azure.com/forums/327234-data-lake).
