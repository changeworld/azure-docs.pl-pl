---
title: Uaktualnienie rozwiązań do analizy danych big data z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2
description: Uaktualnij swoje rozwiązania do używania usługi Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 84e3aff9c1c8cb3e7fe399c861c2c7d58c278fed
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107877"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Uaktualnienie rozwiązań do analizy danych big data z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2

Jeśli używasz usługi Azure Data Lake Storage Gen1 rozwiązań do analizy danych big data, ten Przewodnik ułatwia uaktualnienia tych rozwiązań, aby używać usługi Azure Data Lake Storage Gen2. W tym dokumencie można użyć do oceny, zależności, które rozwiązanie ma na Data Lake Storage Gen1. Ten przewodnik pokazuje także sposobu planowania i przeprowadzić uaktualnienie.

Pomożemy Ci przez następujące zadania:

:heavy_check_mark: Oceń gotowość do uaktualnienia

:heavy_check_mark: Planowanie uaktualnienia

:heavy_check_mark: Dokonaj uaktualnienia

## <a name="assess-your-upgrade-readiness"></a>Oceń gotowość do uaktualnienia

Naszym celem jest, że wszystkie funkcje, które znajdują się w Data Lake Storage Gen1 zostanie udostępniona w Data Lake Storage Gen2. Jak te funkcje są udostępniane np. w zestawie SDK, interfejsu wiersza polecenia itd., mogą się różnić między Data Lake Storage Gen1 i Data Lake Storage Gen2. Aplikacje i usługi działające z usługą Data Lake Storage Gen1 muszą mieć możliwość działają podobnie, za pomocą Data Lake Storage Gen2. Ponadto niektóre funkcje nie będzie dostępny w Data Lake Storage Gen2 natychmiast. Po ich udostępnieniu ogłosimy ich w tym dokumencie.

Następujące sekcje dalej pomoże wybrać najlepszy sposób uaktualnić do Data Lake Storage Gen2 i kiedy może być najbardziej przydatne, aby to zrobić.

### <a name="data-lake-storage-gen1-solution-components"></a>Składniki rozwiązania Gen1 magazynu jeziora danych

Najprawdopodobniej korzystając z programu Data Lake Storage Gen1 w ramach rozwiązania do analizy lub potoków, istnieje wiele dodatkowych technologii, które zostanie zastosowana do osiągnięcia funkcji ogólne end-to-end. To [artykułu](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) w tym artykule opisano różne składniki przepływu danych, które zawierają wprowadzania, przetwarzania i wykorzystywanie danych.

Ponadto istnieją składniki przekrojowe aprowizację, zarządzanie i monitorowanie tych składników. Poszczególne składniki pracują z Data Lake Storage Gen1 przy użyciu interfejsu najlepiej nadaje się do nich. Gdy planowane jest uaktualnienie rozwiązania do Data Lake Storage Gen2, należy mieć świadomość interfejsów, które są używane. Należy uaktualnić zarówno interfejsy zarządzania jak i interfejsy danych, ponieważ każdy interfejs mają różne wymagania.

![Składniki rozwiązania Lake magazynu danych](./media/data-lake-storage-upgrade/solution-components.png)

**Rysunek 1** powyżej przedstawiono składniki funkcji będzie wyświetlany w większości rozwiązań analitycznych.

**Rysunek 2** przedstawiono przykład implementacji tych składników przy użyciu określonych technologii.

Funkcje przechowywanie w **rysunek 1** są dostarczane przez Data Lake Storage Gen1 (**na rysunku 2**). Należy zwrócić uwagę interakcje różnych składników przepływu danych z Data Lake Storage Gen1 przy użyciu interfejsów API REST lub zestawu SDK języka Java. Należy również zauważyć, jak interakcji składników funkcji przekrojowe Data Lake Storage Gen1. Składnik aprowizacji przy użyciu szablonów zasobów platformy Azure, natomiast składnik monitorowania, który używa dzienników usługi Azure Monitor korzysta z danych operacyjnych, który pochodzi z Data Lake Storage Gen1.

Aby uaktualnić to rozwiązanie z przy użyciu Data Lake Storage Gen1 do Data Lake Storage Gen2, należy skopiować dane i metadane, ponowne utworzenie punktu zaczepienia przepływów danych, a następnie wszystkie składniki będą musiały mieć możliwość pracy z Data Lake Storage Gen2.

Poniższe sekcje zawierają informacje ułatwiające podejmowanie lepszych decyzji:

:heavy_check_mark: Możliwości platformy

:heavy_check_mark: Interfejsy programowania

:heavy_check_mark: Ekosystem platformy Azure

:heavy_check_mark: Ekosystem partnerów

:heavy_check_mark: Informacje operacyjne

W każdej sekcji można określić "musi-identyfikatorów istniejących" dla aktualizacji. Po użytkownik ma pewność, że możliwości są dostępne, lub użytkownik ma pewność, że istnieją obejścia uzasadnione w miejscu, przejdź do [planowania dla uaktualnienie](#planning-for-an-upgrade) części tego przewodnika.

### <a name="platform-capabilities"></a>Możliwości platformy

W tej sekcji opisano, które Data Lake Storage Gen1 możliwości platformy, które są obecnie dostępne w Data Lake Storage Gen2.

| | Usługa Data Lake Storage 1. generacji | Data Lake Storage Gen2 — cel | Data Lake Storage Gen2 — stan dostępności  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Dane organizacji| Obsługuje dane przechowywane jako pliki i foldery. | Obsługuje dane przechowywane w postaci obiektów blob oraz foldery i pliki — [łącza](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Obsługuje dane przechowywane w postaci folderów i plików — *teraz dostępny* <br><br> Obsługuje dane przechowywane w postaci obiektów blob — *nie jest jeszcze dostępna* |
| Przestrzeń nazw| Hierarchiczna | Hierarchiczna |  *Teraz dostępna*  |
| Interfejs API  | Interfejs API REST przy użyciu protokołu HTTPS | Interfejs API REST za pośrednictwem protokołu HTTP/HTTPS| *Teraz dostępna* |
| Interfejs API po stronie serwera| [Interfejs API REST zgodnych z WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Interfejs API REST usługi Azure Blob [interfejsu API REST Gen2 Data Lake Storage](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 interfejsu API REST — *teraz dostępne* <br><br> Usługi Azure Blob Service REST API — *nie jest jeszcze dostępna*       |
| Klient systemu plików usługi Hadoop | Tak ([usługi Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Tak ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Teraz dostępna*  |  
| Operacje na danych — autoryzacji  | Plików i folderów na poziomie POSIX list kontroli dostępu (ACL) oparte na usługi Azure Active Directory tożsamości  | Plików i folderów na poziomie POSIX list kontroli dostępu (ACL) oparte na usługi Azure Active Directory tożsamości [klucz udziału](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) konta poziomu autoryzacji kontroli dostępu opartej na rolach ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) do dostępu do kontenerów | *Teraz dostępna* |
| Operacje na danych — dzienniki  | Yes | Jednorazowe żądania dla dzienników dla określonego czasu trwania, za pomocą biletu pomocy technicznej Azure Monitoring integracji | Jednorazowe dzienniki przy użyciu określonego czasu trwania żądania obsługi biletów — *teraz dostępny*<br><br> Integracja z usługą Azure Monitoring — *nie jest jeszcze dostępna* |
| Szyfrowanie danych magazynowanych | Przezroczyste, po stronie serwera za pomocą kluczy zarządzanych przez usługę i za pomocą kluczy zarządzanych przez klienta w usłudze Azure KeyVault | Przezroczyste, po stronie serwera za pomocą kluczy zarządzanych przez usługę i za pomocą kluczy zarządzanych kluczy klienta w usłudze Azure KeyVault | Klucze zarządzane przez usługę — *teraz dostępny*<br><br> Klucze zarządzane przez klienta — *teraz dostępny*  |
| Operacje zarządzania (np. Tworzenie konta) | [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) udostępnianej przez platformę Azure w celu zarządzania kontami | [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) udostępnianej przez platformę Azure w celu zarządzania kontami | *Teraz dostępna*|
| Zestawy SDK dla deweloperów | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| *Jeszcze niedostępne* |
| |Optymalizacji wydajności dla obciążeń analizy równoległej. Wysoka przepływność i operacje We/Wy. | Optymalizacji wydajności dla obciążeń analizy równoległej. Wysoka przepływność i operacje We/Wy. | *Teraz dostępna* |
| Obsługa usługi Virtual Network (VNet)  | [Korzystając z integracji sieci wirtualnej](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Przy użyciu punktu końcowego usługi dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Teraz dostępna* |
| Limity rozmiaru | Brak limitów rozmiarów kont, rozmiarów plików lub liczby plików | Brak limitów rozmiarów kont lub liczbę plików. Rozmiar pliku jest ograniczony do 5TB. | *Teraz dostępna*|
| Nadmiarowość geograficzna| Lokalnie nadmiarowy (LRS) | Lokalnie nadmiarowy (LRS) Strefowo nadmiarowy (ZRS) globalnie nadmiarowy (GRS) dostęp do odczytu, globalnie nadmiarowy zobacz (RA-GRS) [tutaj](https://docs.microsoft.com/azure/storage/common/storage-redundancy) Aby uzyskać więcej informacji| *Teraz dostępna* |
| Dostępność regionalna | Zobacz [tutaj](https://azure.microsoft.com/regions/) | Wszystkie [regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Teraz dostępna*                                                                                                                           |
| Cena                                       | Zobacz [ceny](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Zobacz [ceny](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Dostępność umowy SLA                            | [Zobacz umowę SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Zobacz umowę SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Teraz dostępna*                                                                                                                           |
| Zarządzanie danymi                             | Wygaśnięcie pliku                                                                                                                                        | Zasady cyklu życia                                                                                                                                                                                                                                                                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                       |

### <a name="programming-interfaces"></a>Interfejsy programowania

W tej tabeli opisano ustawiająca interfejsu API, które są dostępne dla niestandardowych aplikacji. Aby umożliwić ich nieco bardziej zrozumiały, firma Microsoft rozdzielonych te zestawy interfejsów API do 2 typy: zarządzanie interfejsami API i system plików interfejsów API.

Interfejsy API Management ułatwiają zarządzanie kontami podczas systemu plików, interfejsy API ułatwiają działanie plików i folderów.

|  Zestaw API                           |  Usługa Data Lake Storage 1. generacji                                                                                                                                                                                                                                                                                                   | Dostępność Data Lake Storage Gen2 — za pomocą uwierzytelniania klucza wspólnego | Dostępność Data Lake Storage Gen2 — za pomocą uwierzytelniania OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zestaw SDK platformy .NET — Zarządzanie                  | [Link](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Nieobsługiwane*                                                      | *Dostępne teraz -* [łącza](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| Zestaw SDK platformy .NET — system plików                  | [Link](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Jeszcze niedostępne*                                                | *Jeszcze niedostępne*                                                                                                                                             |
| Java SDK — Zarządzanie                  | [Link](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Nieobsługiwane*                                                      | *Dostępne teraz —* [łącza](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK — system plików                  | [Link](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                         | *Jeszcze niedostępne*                                                | *Jeszcze niedostępne*                                                                                                                                             |
| Środowisko node.js — Zarządzanie                   | [Link](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | Nieobsługiwane                                                      | *Dostępne teraz -* [łącza](https://azure.github.io/azure-storage-node/)                                                                                            |
| Środowisko node.js — system plików                   | [Link](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Jeszcze niedostępne*                                                | *Jeszcze niedostępne*                                                                                                                                             |
| Python – zarządzania                    | [Link](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Nieobsługiwane*                                                      | *Dostępne teraz -* [łącza](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python – systemu plików                    | [Link](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Jeszcze niedostępne*                                                | *Jeszcze niedostępne*                                                                                                                                             |
| Interfejs API REST — Zarządzanie                  | [Link](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Nieobsługiwane*                                                      | *Dostępne teraz-*                                                                                                                                               |
| Interfejs API REST — system plików                  | [Link](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Teraz dostępna*                                                    | *Dostępne teraz -* [łącza](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell — zarządzanie i system plików | [Link](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Zarządzanie — nieobsługiwany system plików - *nie jest jeszcze dostępna*        | Zarządzanie — *dostępne teraz -* [łącza](https://docs.microsoft.com/powershell/module/az.storage) <br><br>System — plików *nie jest jeszcze dostępna* |
| Interfejs wiersza polecenia — Zarządzanie                       | [Link](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Nieobsługiwane*                                                      | *Dostępne teraz -* [łącza](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| Interfejs wiersza polecenia — system plików                       | [Link](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Jeszcze niedostępne*                                                | *Jeszcze niedostępne*                                                                                                                                             |
| Szablony usługi Azure Resource Manager — Zarządzanie             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Nieobsługiwane*                                                      | *Dostępne teraz -* [łącza](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Ekosystem platformy Azure

Korzystając z programu Data Lake Storage Gen1, można użyć różnych produktów i usług firmy Microsoft w potoków end-to-end. Te usługi i produkty pracować Data Lake Storage Gen1 bezpośrednio lub pośrednio. Ta tabela zawiera listę usług, firma Microsoft została zmodyfikowana, aby pracować z Data Lake Storage Gen1 i pokazuje, które z nich jest obecnie zgodna z Data Lake Storage Gen2.

| **Obszar**             | **Dostępność usługi Data Lake Storage Gen1**                                                                                                                                    | **Dostępność Data Lake Storage Gen2 — za pomocą uwierzytelniania klucza wspólnego**                                                                                                           | **Dostępność usługi Data Lake Storage Gen2 — przy użyciu protokołu OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Framework analizy  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Teraz dostępna*                                                                                                                                                              | *Teraz dostępna*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 — *dostępne teraz* 4.0 HDInsight — *nie jest jeszcze dostępna*      | ESP HDInsight 3.6 — *teraz dostępne* <br><br>  ESP HDInsight 4.0 - *nie jest jeszcze dostępna*                                                                 |
|                      | Środowisko uruchomieniowe usługi Databricks 3.1 lub nowszej                                                                                                                                               | [Środowisko uruchomieniowe usługi Databricks 5.1 i nowszych](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *— jest teraz dostępny* | [Środowisko uruchomieniowe usługi Databricks 5.1 i nowszych](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) — *teraz dostępne*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Nieobsługiwane*                                                                                                                                                              | *Dostępne teraz* [łącza](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Integracja danych     | [Fabryka danych](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [W wersji 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) — *dostępne teraz* w wersji 1 — *nieobsługiwane*                               | [W wersji 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) — *teraz dostępne* <br><br> Wersja 1 — *nieobsługiwane*  |
|                      | [Narzędzia AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Nieobsługiwane*                                                                                                                                                              | *Nieobsługiwane*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
|                      | [Wykaz danych](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
| IoT                  | [Przechwytywanie usługi Event Hubs —](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
| Zużycie          | [Program Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
| Produktywność         | [Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Nieobsługiwane*                                                                                                                                                              | Zarządzanie kontami *— jest teraz dostępny* <br><br>Operacje na danych *—**nie jest jeszcze dostępna*                                                                   |
|                      | [Narzędzia Data Lake Tools for Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
|                      | [Azure Storage Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Teraz dostępna*                                                                                                                                                              | *Teraz dostępna*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |

### <a name="partner-ecosystem"></a>Ekosystem partnerów

Poniższa tabela pokazuje listę usług innych firm i produktów, które zostały zmodyfikowane, aby pracować z Data Lake Storage Gen1. Pokazuje również, które nie są obecnie zgodna z Data Lake Storage Gen2.

| Obszar            | Partner  | Produkt/usługę  | Dostępność usługi Data Lake Storage Gen1                                                                                                                                               | Dostępność Data Lake Storage Gen2 — za pomocą uwierzytelniania klucza wspólnego                                                   | Dostępność usługi Data Lake Storage Gen2 — przy użyciu protokołu Oauth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Framework analizy | Cloudera     | PROGRAMU CDH                  | [Link](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Jeszcze niedostępne*                                                                                                  | [Link](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Link](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Nieobsługiwane*                                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [Link](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
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
| Dane dotyczące rozliczeń - liczniki, które są wysyłane do handlu zespołu do rozliczeń i następnie udostępniane klientom  | *Teraz dostępna*                                                                                             | *Teraz dostępna*                                                                                                                           |
| Dzienniki aktywności                                                                                          | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Jednorazowe dzienniki przy użyciu określonego czasu trwania żądania obsługi biletów — *dostępne teraz* integracja monitorowania platformy Azure — *nie jest jeszcze dostępna* |
| Dzienniki diagnostyczne                                                                                        | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Jednorazowe dzienniki przy użyciu określonego czasu trwania żądania obsługi biletów — *dostępne teraz* integracja monitorowania platformy Azure — *nie jest jeszcze dostępna* |
| Metryki                                                                                                | *Nieobsługiwane*                                                                                               | *Dostępne teraz -* [łącza](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planowanie uaktualnienia

W tej sekcji założono, że [ocenić gotowość do uaktualnienia](#assess-your-upgrade-readiness) części tego przewodnika oraz że są spełnione wszystkie zależności. W przypadku funkcji, które nadal nie są dostępne w Data Lake Storage Gen2 kontynuować tylko wtedy, gdy znasz odpowiednie rozwiązania. Poniższe sekcje zawierają wskazówki na jak można zaplanować dla uaktualnienie potoków. Rzeczywiste uaktualniania będzie opisana [przeprowadzania uaktualnienia](#performing-the-upgrade) części tego przewodnika.

### <a name="upgrade-strategy"></a>Strategii uaktualniania

Najbardziej krytycznych części procesu uaktualniania, wybierają strategii. Ta decyzja określi opcje dostępne dla Ciebie.

Poniższa tabela zawiera listę niektórych dobrze znanych strategie, które zostały użyte w celu migracji bazy danych, klastrów Hadoop, itp. Firma Microsoft będzie przyjąć podobne strategie w nasze wskazówki, a następnie dostosować je do nasz kontekst.

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

Ogólnej strategii, którego używasz do wykonywania uaktualnienia (opisanego w [uaktualnienia strategii](#upgrade-strategy) części tego przewodnika), określają, narzędzia, które służy do uaktualniania danych. Narzędzia wymienione poniżej są oparte na bieżących informacji i są sugestie. 

#### <a name="tools-guidance"></a>Wskazówki dotyczące narzędzi

| Strategia                       | Narzędzia                                                                                                             | Specjaliści                                                                                                                             | Zagadnienia do rozważenia                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Lift-and-shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Zarządzana usługa w chmurze                                                                                                                | Tylko kopie danych. Nie można skopiować list kontroli dostępu za pośrednictwem obecnie.                                                                                                                                                                                                                                                                      |
|                                    | [Narzędzia Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Za pomocą tego narzędzia można skopiować dobrze znane narzędzia usługi Hadoop — pod warunkiem uprawnień, czyli list ACL                                                   | Wymaga klastra, które można podłączyć Gen2 i Data Lake Storage Gen1, w tym samym czasie.                                                                                                                                                                                   |
| **Kopiowania raz i kopii przyrostowej** | Azure Data Factory                                                                                                    | Zarządzana usługa w chmurze                                                                                                                | Do obsługi przyrostowego kopiowania w usłudze ADF, dane muszą być uporządkowane w sposób szeregów czasowych. Najkrótszy interwał między przyrostowe kopie [15 minut](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). Dla odstępach krótszych ADF nie będą działać. Nie można skopiować list kontroli dostępu za pośrednictwem obecnie. |
| **Wdrożenie równoległe**              | [Platforma WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Obsługa replikacji spójnej czy przy użyciu czystego środowiska Hadoop podłączone do usługi Azure Data Lake Storage, obsługuje zapewnienia dwukierunkowej replikacji | Jeśli nie używa środowiska czystego Hadoop, prawdopodobnie opóźnienia w replikacji.                                                                                                                                                                                                                                                  |

Należy pamiętać, że stron trzecich, które może obsłużyć Gen1 Data Lake Storage Data Lake Storage Gen2 uaktualniania, nie angażując powyżej danych/meta-data narzędzia do kopiowania (na przykład: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Zapewniają one środowisko "kompleksowym", który wykonuje migracji danych, a także migrację obciążeń. Może być konieczne uaktualnienie out-of-band dla znajdujących się poza ich ekosystemy narzędzi.

#### <a name="considerations"></a>Zagadnienia do rozważenia

* Należy ręcznie utworzyć konta Data Lake Storage Gen2 najpierw przed rozpoczęciem dowolnej części procesu uaktualniania, ponieważ bieżące wskazówki nie obejmuje żadnych Gen2 konto procesu automatycznego na podstawie informacji konta Gen1. Upewnij się, porównanie procesów tworzenia kont [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) i [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2, obsługuje tylko pliki rozmiarze maksymalnym rozmiarze 5 TB. Aby uaktualnić do Data Lake Storage Gen2, może być konieczne zmiany rozmiaru plików Data Lake Storage Gen1, tak aby były mniejsze niż 5 TB, rozmiar.

* Jeśli nie chcesz kopiować listy ACL za pomocą narzędzia, która nie obejmuje kopiowania listy ACL, następnie należy do ustawiania listy kontroli dostępu na komputerze docelowym ręcznie na odpowiednie najwyższym poziomie. Możesz tworzyć, za pomocą Eksploratora usługi Storage. Upewnij się, że te listy ACL domyślne listy kontroli dostępu, aby pliki i foldery, kopiujących za pośrednictwem dziedziczy ich.

* W Data Lake Storage Gen1 najwyższego poziomu, można ustawić list ACL jest w katalogu głównym konta. Data Lake Storage Gen2, jednak w najwyższego poziomu, można ustawić list ACL jest w folderze głównym w systemie plików, a nie całego konta. Dlatego jeśli chcesz ustawić domyślne listy kontroli dostępu na poziomie konta, należy te we wszystkich systemach plików na koncie usługi Data Lake Storage Gen2 zduplikowane.

* Ograniczenia nazewnictwa plików różnią się między dwoma systemami magazynowania. Te różnice są szczególnie dotyczące podczas kopiowania z Data Lake Storage Gen2 Data Lake Storage Gen1 ponieważ one bardziej ma ograniczone ograniczenia.

### <a name="application-upgrade"></a>Uaktualnienie aplikacji

Jeśli potrzebujesz do tworzenia aplikacji na Data Lake Storage Gen1 lub Data Lake Storage Gen2, musisz najpierw wybrać odpowiedni interfejs programowania. Podczas wywoływania interfejsu API w tym interfejsie musisz zapewnić odpowiedni identyfikator URI i odpowiednie poświadczenia. Reprezentacja te trzy elementy, API, identyfikator URI i jak poświadczenia są udostępniane, czy różnic między Data Lake Storage Gen1 i Data Lake Storage Gen2.So, jako część uaktualnienia aplikacji, konieczne będzie mapowanie tych trzech konstrukcje odpowiednio.

#### <a name="uri-changes"></a>Zmiany identyfikatora URI

Głównym zadaniem jest sformułowanie identyfikatory URI, które mają prefiks `adl://` na identyfikatory URI mających `abfss://` prefiks.

Schemat identyfikatora URI Data Lake Storage Gen1 mowa [tutaj](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) w szczegóły, ale ogólnie rzecz biorąc, zaleca się *adl://mydatalakestore.azuredatalakestore.net/\<file_path\>.*

Schemat identyfikatora URI do uzyskiwania dostępu do plików Data Lake Storage Gen2 zostało wyjaśnione [tutaj](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) w szczegóły, ale ogólnie rzecz biorąc, zaleca się `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>`.

Musisz przejść przez istniejące aplikacje i upewnić się, że zmiany zostały wprowadzone identyfikatory URI odpowiednio wskaż Data Lake Storage Gen2 te. Ponadto należy dodać odpowiednie poświadczenia. Na koniec jak wycofywanie oryginalnej aplikacji i Zamień na nową aplikację należy ściśle wyrównywana strategii ogólną uaktualnienia.

#### <a name="custom-applications"></a>Aplikacje niestandardowe

W zależności od interfejsu, używanych przez aplikację za pomocą programu Data Lake Storage Gen1 należy zmodyfikować, aby dostosować ją do Data Lake Storage Gen2.

##### <a name="rest-apis"></a>Interfejsy API REST

Jeśli aplikacja używa interfejsów API REST Data Lake Storage, należy zmodyfikować aplikację w celu użycia interfejsów API Data Lake Storage Gen2 REST. Linki zostały podane w *interfejsy programowania* sekcji.

##### <a name="sdks"></a>Zestawy SDK

Jako o nazwie w *ocenić gotowość do uaktualnienia* sekcji, zestawy SDK nie są obecnie dostępne. Chcąc portu nad swoimi aplikacjami, aby Data Lake Storage Gen2, firma Microsoft zaleca, poczekaj obsługiwanych zestawów SDK mają być dostępne.

##### <a name="powershell"></a>PowerShell

Jak przywołane w oceny sekcji rozwiązania upgrade readiness, obsługa programu PowerShell nie jest obecnie dostępny dla płaszczyzny danych.

Polecenia cmdlet płaszczyzny zarządzania można zastąpić odpowiednie pozycje w Data Lake Storage Gen2. Linki zostały podane w *interfejsy programowania* sekcji.

##### <a name="cli"></a>Interfejs wiersza polecenia

Jako o nazwie w *ocenić gotowość do uaktualnienia* sekcji obsługę interfejsu wiersza polecenia nie jest obecnie dostępny dla płaszczyzny danych.

Można zastąpić poleceń płaszczyzny zarządzania odpowiednie pozycje w Data Lake Storage Gen2. Linki zostały podane w *interfejsy programowania* sekcji.

### <a name="analytics-frameworks-upgrade"></a>Uaktualnianie analizy struktury

Jeśli aplikacja tworzy metadane dotyczące informacji w magazynie, takie jak jawne pliku i ścieżki folderu, należy wykonać dodatkowe akcje po uaktualnieniu magazynu danych/meta-data. Jest to szczególnie istotne dla platform analizy, takich jak HDInsight Azure Databricks itp., które zwykle tworzą wykazu danych magazynu danych.

Analytics Platform pracować z danymi i metadane przechowywane w lokalizacjach zdalnych, takich jak Data Lake Storage Gen1 i Gen2. Tak teoretycznie silniki mogą być efemeryczne i zostać wznowione tylko wtedy, gdy zadania należy uruchamiać przechowywanych danych.

Jednak w celu zoptymalizowania wydajności, struktur analytics może utworzyć jawnego odwołania do plików i folderów przechowywanych w magazynie zdalnym, a następnie utwórz pamięci podręcznej w celu przechowania. Identyfikator URI danych zdalnych zmieni się, na przykład klaster, który został przechowywanie danych w Data Lake Storage Gen1 wcześniej, a teraz chcą przechowywać w Data Lake Storage Gen2, identyfikator URI dla tego samego skopiowanej zawartości może się różnić. Dlatego po danych i metadane uaktualnienia pamięci podręczne dla tych aparatów trzeba również można zaktualizować lub ponownie zainicjowane

W ramach procesu planowania należy do identyfikowania Twojej aplikacji i zorientować się, jak metadane informacje mogą być ponownie zainicjowane, aby wskazać dane, które są obecnie przechowywane w Data Lake Storage Gen2. Poniżej przedstawiono wskazówki dotyczące struktury powszechnie przyjętym analytics do udzielenia odpowiedzi na swoje kroki uaktualnienia.

#### <a name="azure-databricks"></a>Azure Databricks

W zależności od wybranej strategii uaktualniania kroki będą się różnić. Bieżącej sekcji przyjęto założenie, że została wybrana strategia "Life-and-shift". Ponadto istniejącego obszaru roboczego usługi Databricks, które umożliwiają dostęp do danych w ramach konta Data Lake Storage Gen1 powinien pracować z danymi, która jest kopiowana do konta Data Lake Storage Gen2.

Najpierw upewnij się, że po utworzeniu konta Gen2 i następnie skopiowanych danych i metadanych z Gen1 do Gen2 za pomocą odpowiedniego narzędzia. Te narzędzia są wywoływane w [uaktualniania danych](#data-upgrade) części tego przewodnika.

Następnie [uaktualnienia](https://docs.azuredatabricks.net/user-guide/clusters/index.html) istniejące usługi Databricks uruchomienia klastra przy użyciu środowiska uruchomieniowego usługi Databricks, 5.1 lub nowszej, który powinien obsługiwać Data Lake Storage Gen2.

Kroki po tej dacie są oparte na jak istniejący obszar roboczy usługi Databricks uzyskuje dostęp do danych w ramach konta Data Lake Storage Gen1. Można to zrobić przez wywołującego adl: / / identyfikatorów URI [bezpośrednio](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) z notesów lub za pomocą [punkty instalacji](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Jeśli uzyskujesz dostęp bezpośrednio z notesów, zapewniając pełny adl: / / identyfikatory URI, musisz przejść przez każdy Notes i zmień konfigurację, aby dostęp do odpowiedniego Data Lake Storage Gen2 identyfikatora URI.

Idąc dalej, musisz ponownie skonfigurować to ustawienie, aby konta Data Lake Storage Gen2. Są wymagane żadne zmiany więcej i notebooki powinien móc pracować jak wcześniej.

Jeśli używasz żadnego innymi strategiami uaktualnienia można utworzyć odmianą powyższe kroki zgodnie z wymaganiami.

### <a name="azure-ecosystem-upgrade"></a>Uaktualnienie ekosystem platformy Azure

Każdego z narzędzi i usług w [ekosystem platformy Azure](#azure-ecosystem) części tego przewodnika musi być skonfigurowana do pracy z Data Lake Storage Gen2.

Najpierw upewnij się, że istnieje integracji udostępniono Data Lake Storage Gen2.

Następnie elementy wymienione powyżej (na przykład: Identyfikator URI i poświadczeń), będą musieli zmienić. Można zmodyfikować istniejące wystąpienie współdziałająca z usługą Data Lake Storage Gen1 lub można utworzyć nowe wystąpienie, które może pracować nad Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Uaktualnienie ekosystemu partnerów

Skontaktuj się z partnerem składników i narzędzi w celu upewnij się, że można pracować Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Przeprowadzania uaktualnienia

### <a name="pre-upgrade"></a>Przed uaktualnieniem

W ramach tego, czy wykonano już instrukcje *ocenić gotowość do uaktualnienia* sekcji i [planowania dla uaktualnienie](#planning-for-an-upgrade) sekcji tego przewodnika, otrzymał wszystkie niezbędne informacje, a masz utworzony plan, spełniające Twoje potrzeby. Prawdopodobnie trzeba będzie testowania zadań w tej fazie.

### <a name="in-upgrade"></a>W uaktualnieniu

W zależności od strategii wybierzesz i komplikacje związane z jego rozwiązania, w tym etapie może być krótki co najmniej jeden rozszerzonej w przypadku, gdy istnieje wiele obciążeń oczekiwanie na przyrostowo przeniesieni do Data Lake Storage Gen2. Są to najbardziej krytyczna część uaktualnienia.

### <a name="post-upgrade"></a>Po zakończeniu uaktualniania.

Po wykonaniu operacji przejścia, ostatnie kroki będą obejmować dokładnej weryfikacji. Spowoduje to obejmują, ale nie ogranicza się do weryfikowania danych został skopiowany przez niezawodne, weryfikowanie listy kontroli dostępu została poprawnie ustawiona, weryfikowanie potoki end-to-end działają prawidłowo itp. Po ukończeniu weryfikacji można teraz Wyłącz stary potoków, usuwanie konta Data Lake Storage Gen1 źródła i przejdź pełną prędkością rozwiązań usługi Data Lake Storage Gen2.

## <a name="conclusion"></a>Podsumowanie

Wskazówki zawarte w tym dokumencie powinny pomogły możesz uaktualnić swoje rozwiązania do używania Data Lake Storage Gen2. 

Jeśli masz więcej pytań lub przesłać opinię, podaj komentarze poniżej lub wprowadź swoją opinię w [Forum opinii platformy Azure](https://feedback.azure.com/forums/327234-data-lake).
