---
title: Uaktualnij rozwiązania do analizy danych Big Data z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2
description: Uaktualnij rozwiązanie, aby używać Azure Data Lake Storage Gen2
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 27d752b8ff7eafbb92930b19e17890ace8a90b85
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750448"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Uaktualnij rozwiązania do analizy danych Big Data z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2

Jeśli używasz Azure Data Lake Storage Gen1 w rozwiązaniach do analizy danych Big Data, ten przewodnik pomoże Ci uaktualnić te rozwiązania do korzystania z Azure Data Lake Storage Gen2. Za pomocą tego dokumentu można ocenić zależności, na których Twoje rozwiązanie ma Data Lake Storage Gen1. W tym przewodniku pokazano również, jak zaplanować i przeprowadzić uaktualnienie.

Pomożemy Ci w następujących zadaniach:

: heavy_check_mark: Oceń gotowość do uaktualnienia

: heavy_check_mark: Planowanie uaktualnienia

: heavy_check_mark: Przeprowadź uaktualnienie

## <a name="assess-your-upgrade-readiness"></a>Oceń gotowość do uaktualnienia

Naszym celem jest, że wszystkie funkcje, które są obecne w Data Lake Storage Gen1 zostaną udostępnione w Data Lake Storage Gen2. Jak są ujawniane te możliwości, np. w zestawie SDK, interfejsie wiersza polecenia itp., mogą się różnić między Data Lake Storage Gen1 i Data Lake Storage Gen2. Aplikacje i usługi, które działają z Data Lake Storage Gen1 muszą być w stanie działać podobnie z Data Lake Storage Gen2. Na koniec niektóre funkcje nie będą dostępne w Data Lake Storage Gen2 od razu. Gdy staną się dostępne, opublikujemy je w tym dokumencie.

Te następne sekcje ułatwią podjęcie decyzji o tym, jak najlepiej uaktualnić do Data Lake Storage Gen2 i kiedy może to mieć największe znaczenie.

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 składniki rozwiązania

Najprawdopodobniej w przypadku używania Data Lake Storage Gen1 w rozwiązaniach analitycznych lub potokach istnieje wiele dodatkowych technologii, które są stosowane w celu osiągnięcia ogólnych, kompleksowych funkcji. W tym [artykule](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) opisano różne składniki przepływu danych, które obejmują pozyskiwanie, przetwarzanie i zużywanie danych.

Ponadto istnieją składniki do obsługi wielu obciążeń, które umożliwiają aprowizacji i monitorowanie tych składników oraz zarządzanie nimi. Każdy składnik działa z Data Lake Storage Gen1 przy użyciu interfejsu najlepiej dopasowanego do nich. Jeśli planujesz uaktualnić rozwiązanie do Data Lake Storage Gen2, musisz mieć świadomość interfejsów, które są używane. Należy uaktualnić zarówno interfejsy zarządzania, jak i interfejsy danych, ponieważ każdy interfejs ma odrębne wymagania.

![Data Lake Storage składniki rozwiązania](./media/data-lake-storage-upgrade/solution-components.png)

Powyższy **rysunek 1** pokazuje składniki funkcjonalności, które zobaczysz w większości rozwiązań analitycznych.

Na **rysunku 2** przedstawiono przykład sposobu implementowania tych składników przy użyciu określonych technologii.

Funkcja przechowywania w programie **rysunek 1** jest udostępniana przez Data Lake Storage Gen1 (**rysunek 2**). Zwróć uwagę, jak różne składniki w przepływie danych współdziałają z Data Lake Storage Gen1 przy użyciu interfejsów API REST lub zestawu Java SDK. Zwróć uwagę na to, jak składniki funkcjonalności krzyżowego współdziałają z Data Lake Storage Gen1. Składnik aprowizacji używa szablonów zasobów platformy Azure, natomiast składnik monitorowania, który używa dzienników Azure Monitor, wykorzystuje dane operacyjne pochodzące z Data Lake Storage Gen1.

Aby uaktualnić rozwiązanie z używania Data Lake Storage Gen1 do Data Lake Storage Gen2, należy skopiować dane i metadanych, ponownie podpiąć przepływy danych, a następnie wszystkie składniki muszą mieć możliwość pracy z Data Lake Storage Gen2ami.

Poniższe sekcje zawierają informacje ułatwiające podejmowanie lepszych decyzji:

: heavy_check_mark: możliwości platformy

: heavy_check_mark: Programowanie interfejsów

: heavy_check_mark: ekosystem platformy Azure

: heavy_check_mark: ekosystem partnerski

: heavy_check_mark: informacje operacyjne

W każdej sekcji będzie można określić "musi to być" dla uaktualnienia. Jeśli masz pewność, że możliwości są dostępne, lub masz pewność, że istnieją odpowiednie obejścia, przejdź do sekcji [Planowanie uaktualnienia](#planning-for-an-upgrade) tego przewodnika.

### <a name="platform-capabilities"></a>Możliwości platformy

Ta sekcja zawiera opis możliwości Data Lake Storage Gen1 platformy, które są obecnie dostępne w programie Data Lake Storage Gen2.

| | Data Lake Storage 1. generacji | Data Lake Storage Gen2 — cel | Data Lake Storage Gen2 — stan dostępności  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Organizacja danych| Obsługuje dane przechowywane jako foldery i pliki | Program obsługuje dane przechowywane jako obiekty/elementy BLOB, a także foldery i pliki — [link](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Obsługuje dane przechowywane jako foldery i pliki — *teraz dostępne* <br><br> Obsługuje dane przechowywane jako obiekty/obiektów blob, które *nie są jeszcze dostępne* |
| Przestrzeń nazw| Hierarchicznej | Hierarchicznej |  *Dostępne teraz*  |
| API  | Interfejs API REST za pośrednictwem protokołu HTTPS | Interfejs API REST za pośrednictwem protokołu HTTP/HTTPS| *Dostępne teraz* |
| Interfejs API po stronie serwera| [Interfejs API REST zgodny z WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Interfejs API REST usługi Azure Blob Service [Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Interfejs API REST Data Lake Storage Gen2 — *teraz jest dostępny* <br><br> Interfejs API REST usługi Azure Blob Service — *nie jest jeszcze dostępny*       |
| Klient systemu plików Hadoop | Tak ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Tak ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Dostępne teraz*  |  
| Operacje na danych — autoryzacja  | Na poziomie plików i folderów w systemie POSIX Access Control listy (ACL) na podstawie tożsamości Azure Active Directory  | Na poziomie plików i folderów w systemie POSIX Access Control List (ACL) na podstawie tożsamości Azure Active Directory [udostępnienia klucza](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) dla ról autoryzacji na poziomie konta Access Control ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) w celu uzyskania dostępu do kontenerów | *Dostępne teraz* |
| Operacje na danych — dzienniki  | Tak | Pojedyncze żądania dla dzienników o określonym czasie za pomocą usługi Azure Monitoring | Jednokrotne żądania dzienników dla określonego czasu trwania przy użyciu biletu pomocy technicznej — *dostępne teraz*<br><br> Integracja monitorowania platformy Azure — *nie jest jeszcze dostępna* |
| Szyfrowanie danych w spoczynku | Przezroczysty, po stronie serwera z kluczami zarządzanymi przez usługę i z kluczami zarządzanymi przez klienta w usłudze Azure Keys | Przezroczysty, po stronie serwera z kluczami zarządzanymi przez usługę i z kluczami zarządzanymi przez klienta w usłudze Azure Keys | Klucze zarządzane przez usługę — *teraz dostępne*<br><br> Klucze zarządzane przez klienta — *teraz dostępne*  |
| Operacje zarządzania (np. Tworzenie konta) | [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) udostępniana przez platformę Azure do zarządzania kontami | [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) udostępniana przez platformę Azure do zarządzania kontami | *Dostępne teraz*|
| Zestawy SDK dla deweloperów | .NET, Java, Python, Node. js  | .NET, Java, Python, Node. js, C++, Ruby, php, go, Android, iOS| *Jeszcze niedostępne* |
| |Zoptymalizowana wydajność dla obciążeń równoległych analizy. Wysoka przepływność i operacje we/wy. | Zoptymalizowana wydajność dla obciążeń równoległych analizy. Wysoka przepływność i operacje we/wy. | *Dostępne teraz* |
| Obsługa Virtual Network (VNet)  | [Korzystanie z integracji Virtual Network](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Korzystanie z punktu końcowego usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Dostępne teraz* |
| Limity rozmiaru | Brak limitów rozmiarów kont, rozmiarów plików ani liczby plików | Brak limitów rozmiaru konta lub liczby plików. Rozmiar pliku jest ograniczony do 5TB. | *Dostępne teraz*|
| Nadmiarowość geograficzna| Lokalnie nadmiarowy (LRS) | Lokalnie nadmiarowy (LRS) strefa nadmiarowa (ZRS) Geograficznie nadmiarowy (GRS) z dostępem do odczytu (RA-GRS) — zobacz [tutaj](https://docs.microsoft.com/azure/storage/common/storage-redundancy) , aby uzyskać więcej informacji| *Dostępne teraz* |
| Dostępność regionalna | Zobacz [tutaj](https://azure.microsoft.com/regions/) | Wszystkie [regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Dostępne teraz*                                                                                                                           |
| Cena                                       | Zobacz [Cennik](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Zobacz [Cennik](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Dostępność umowy SLA                            | [Zobacz umowa SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Zobacz umowa SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Dostępne teraz*                                                                                                                           |
| Zarządzanie danymi                             | Wygaśnięcie pliku                                                                                                                                        | Zasady cyklu życia                                                                                                                                                                                                                                                                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                       |

### <a name="programming-interfaces"></a>Interfejsy programowania

W tej tabeli opisano zestawy interfejsów API, które są dostępne dla aplikacji niestandardowych. Aby zapewnić sobie nieznaczną wartość, należy rozdzielić te zestawy interfejsów API na dwa typy: interfejsy API zarządzania i interfejsy API systemu plików.

Interfejsy API zarządzania ułatwiają zarządzanie kontami, natomiast interfejsy API systemu plików ułatwiają wykonywanie operacji na plikach i folderach.

|  Zestaw interfejsów API                           |  Data Lake Storage 1. generacji                                                                                                                                                                                                                                                                                                   | Dostępność Data Lake Storage Gen2 — z uwierzytelnianiem za pomocą klucza współużytkowanego | Dostępność Data Lake Storage Gen2 — z uwierzytelnianiem OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK — zarządzanie                  | [Link](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Nieobsługiwane*                                                      | *Dostępne teraz —* [link](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| Zestaw SDK platformy .NET — system plików                  | [Link](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Jeszcze niedostępne*                                                | *Jeszcze niedostępne*                                                                                                                                             |
| Zestaw Java SDK — zarządzanie                  | [Link](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Nieobsługiwane*                                                      | *Dostępne teraz —* [link](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Zestaw Java SDK — system plików                  |   [łącza](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                        | *Jeszcze niedostępne*                                                | *Jeszcze niedostępne*                                                                                                                                             |
| Node. js — zarządzanie                   |   [łącza](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                               | Brak obsługi                                                      | *Dostępne teraz —* [link](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node. js — system plików                   |   [łącza](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                               | *Jeszcze niedostępne*                                                | *Jeszcze niedostępne*                                                                                                                                             |
| Python — zarządzanie                    |   [łącza](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                | *Nieobsługiwane*                                                      | *Dostępne teraz —* [link](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python — system plików                    | [Link](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Jeszcze niedostępne*                                                | *Jeszcze niedostępne*                                                                                                                                             |
| Interfejs API REST — zarządzanie                  | [Link](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Nieobsługiwane*                                                      | *Dostępne teraz —*                                                                                                                                               |
| Interfejs API REST — system plików                  | [Link](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Dostępne teraz*                                                    | *Dostępne teraz —* [link](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell — zarządzanie i system plików | [Link](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Zarządzanie — nieobsługiwany system plików — *nie jest jeszcze dostępny*        | Zarządzanie — [link](https://docs.microsoft.com/powershell/module/az.storage) *dostępny teraz* <br><br>System plików — *jeszcze nie jest dostępny* |
| Interfejs wiersza polecenia — Zarządzanie                       | [Link](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Nieobsługiwane*                                                      | *Dostępne teraz —* [link](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| Interfejs wiersza polecenia — system plików                       | [Link](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Jeszcze niedostępne*                                                | *Jeszcze niedostępne*                                                                                                                                             |
| Szablony Azure Resource Manager — zarządzanie             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Nieobsługiwane*                                                      | *Dostępne teraz —* [link](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Ekosystem platformy Azure

Korzystając z Data Lake Storage Gen1, można użyć różnych usług i produktów firmy Microsoft w ramach kompleksowych potoków. Te usługi i produkty współpracują z Data Lake Storage Gen1ami bezpośrednio lub pośrednio. W tej tabeli przedstawiono listę usług, które zostały zmodyfikowane do pracy z Data Lake Storage Gen1 i pokazują, które z nich są obecnie zgodne z Data Lake Storage Gen2.

| **Obszar**             | **Dostępność Data Lake Storage Gen1**                                                                                                                                    | **Dostępność dla Data Lake Storage Gen2 — z uwierzytelnianiem za pomocą klucza współużytkowanego**                                                                                                           | **Dostępność dla Data Lake Storage Gen2 — z uwierzytelnianiem OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Struktura analizy  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Dostępne teraz*                                                                                                                                                              | *Dostępne teraz*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [Hdinsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3,6 — *dostępna teraz* Usługa HDInsight 4,0 — *nie jest jeszcze dostępna*      | Usługa HDInsight 3,6 ESP — *dostępna teraz* <br><br>  Usługa HDInsight 4,0 ESP — *nie jest jeszcze dostępna*                                                                 |
|                      | Databricks Runtime 3,1 i nowsze                                                                                                                                               | [Databricks Runtime 5,1 i nowsze](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *— dostępne teraz* | [Databricks Runtime 5,1 i nowsze](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) — *teraz dostępne*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Nieobsługiwane*                                                                                                                                                              | [Link](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) *dostępne teraz* |
| Integracja danych     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Wersja 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) — wersja *dostępna teraz* 1 — *nieobsługiwana*                               | [Wersja 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) — *teraz dostępna* <br><br> Wersja 1 — *nieobsługiwane*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Nieobsługiwane*                                                                                                                                                              | *Nieobsługiwane*                                                                                                                                 |
|                      | [Usługi SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Dostępne teraz*                                                                                                                                                          | *Dostępne teraz*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Jeszcze niedostępne*                                                                                                                                                          | *Jeszcze niedostępne*                                                                                                                             |
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

W tej tabeli przedstawiono listę usług i produktów innych firm, które zostały zmodyfikowane w celu pracy z Data Lake Storage Gen1. Wskazuje również, które z nich są obecnie zgodne z Data Lake Storage Gen2.

| Obszar            | Partner  | Produkt/usługa  | Dostępność Data Lake Storage Gen1                                                                                                                                               | Dostępność dla Data Lake Storage Gen2 — z uwierzytelnianiem za pomocą klucza współużytkowanego                                                   | Dostępność dla Data Lake Storage Gen2 — z uwierzytelnianiem OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Struktura analizy | Cloudera     | PROGRAMU CDH                  | [Link](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Jeszcze niedostępne*                                                                                                  | [Link](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Link](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Nieobsługiwane*                                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | HortonWorks  | HDP 3,0              |   [łącza](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                      | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | Qubole       |                      | [Link](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
| ETL                 | StreamSets   |                      | [Link](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | Informatica  |                      | [Link](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | Attunity     |                      | [Link](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | Alteryx      |                      | [Link](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | ImanisData   |                      | [Link](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Jeszcze niedostępne*                                                                                                  | *Jeszcze niedostępne*                                                                                                  |
|                     | WANdisco     |                      | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Informacje operacyjne

Data Lake Storage Gen1 wypychanie określonych informacji i danych do innych usług, które pomagają operacjonalizować potoki. W tej tabeli przedstawiono dostępność odpowiedniej pomocy technicznej w Data Lake Storage Gen2.

| Typ danych                                                                                       | Dostępność Data Lake Storage Gen1                                                                 | Dostępność Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Dane dotyczące rozliczeń — liczniki, które są wysyłane do zespołu handlowego na potrzeby rozliczeń, a następnie udostępniane klientom  | *Dostępne teraz*                                                                                             | *Dostępne teraz*                                                                                                                           |
| Dzienniki aktywności                                                                                          | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Jednokrotne żądania dzienników dla określonego czasu trwania przy użyciu biletu pomocy technicznej — *teraz dostępna* integracja z usługą Azure Monitoring — *nie jest jeszcze dostępna* |
| Dzienniki diagnostyczne                                                                                        | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Jednokrotne żądania dzienników dla określonego czasu trwania przy użyciu biletu pomocy technicznej — *teraz dostępna* integracja z usługą Azure Monitoring — *nie jest jeszcze dostępna* |
| Metryki                                                                                                | *Nieobsługiwane*                                                                                               | *Dostępne teraz —* [link](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planowanie uaktualnienia

W tej sekcji założono, że sekcja [Ocena gotowości do uaktualnienia](#assess-your-upgrade-readiness) w tym przewodniku została sprawdzona i zostały spełnione wszystkie zależności. Jeśli istnieją funkcje, które nadal są niedostępne w programie Data Lake Storage Gen2, należy to zrobić tylko w przypadku znajomości odpowiednich obejść. Poniższe sekcje zawierają wskazówki dotyczące planowania uaktualniania potoków. Wykonanie rzeczywistego uaktualnienia zostanie opisane w sekcji [wykonywanie uaktualnienia](#performing-the-upgrade) w tym przewodniku.

### <a name="upgrade-strategy"></a>Strategia uaktualniania

Najbardziej krytycznym elementem uaktualnienia jest podjęcie decyzji dotyczącej strategii. Ta decyzja określi dostępne opcje.

Ta tabela zawiera listę dobrze znanych strategii, które zostały użyte do migracji baz danych, klastrów usługi Hadoop itd. Firma Microsoft przyjmuje podobne strategie w naszych wskazówkach i dostosowuje je do naszego kontekstu.

| Strategia                   | Formaty                                                                                  | Wady                                                           | Kiedy używać?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podnieś i Przenieś                 | Łatwiejszą.                                                                                 | Wymaga przestoju w przypadku kopiowania danych, przemieszczania zadań i przechodzenia do ruchu przychodzącego i wychodzącego                                             | W przypadku łatwiejszych rozwiązań, w których nie ma wielu rozwiązań uzyskujących dostęp do tego samego konta usługi Gen1, dlatego można je przenieść razem w sposób szybki.                                                  |
| Kopiowanie-jednorazowe i kopiowanie przyrostowe | Zmniejsz czas przestoju, wykonując kopię w tle, gdy system źródłowy jest nadal aktywny. | Wymaga przestoju w przypadku przeniesienia ruchu przychodzącego i wychodzącego.                   | Ilość danych do skopiowania jest duża, a przestoje związane z działaniem i przesunięciami nie są akceptowalne. Przed przejściem można wykonać testy z istotnymi danymi produkcyjnymi w systemie docelowym. |
| Wdrożenie równoległe              | Najmniejszy przestój umożliwia przeprowadzenie migracji aplikacji według własnego uznania.           | Najbardziej rozwinięte, ponieważ synchronizacja dwukierunkowa jest wymagana między tymi dwoma systemami. | W przypadku złożonych scenariuszy, w których aplikacje skompilowane Data Lake Storage Gen1 nie mogą być uruchomienie produkcyjne wszystkie jednocześnie i muszą być przenoszone w sposób przyrostowy.                                                      |

Poniżej znajdują się dodatkowe szczegółowe informacje na temat kroków związanych z każdą strategią. Na liście kroków można wykonać czynności związane ze składnikami należącymi do uaktualnienia. Obejmuje to ogólny system źródłowy, ogólny system docelowy, źródła ruchu przychodzącego dla systemu źródłowego, miejsca docelowe danych wyjściowych dla systemu źródłowego i zadania uruchomione w systemie źródłowym.

Te kroki nie są zalecane. Są one przeznaczone do ustawiania struktury dotyczącej sposobu działania każdej strategii. Udostępnimy analizy przypadków dla każdej strategii, gdy zobaczymy, że są one implementowane.

#### <a name="lift-and-shift"></a>Podnieś i Przenieś

1. Wstrzymaj system źródłowy — źródła ruchu przychodzącego, zadania i miejsca docelowe danych wyjściowych.
2. Skopiuj wszystkie dane z systemu źródłowego do systemu docelowego.
3. Wskaż wszystkie źródła transferu danych przychodzących do systemu docelowego. Wskaż miejsce docelowe danych wyjściowych z systemu docelowego.
4. Przenieś, zmodyfikuj, uruchom wszystkie zadania w systemie docelowym.
5. Wyłącz system źródłowy.

#### <a name="copy-once-and-copy-incremental"></a>Kopiowanie jednokrotne i kopiowanie przyrostowe

1. Skopiuj dane z systemu źródłowego do systemu docelowego.
2. Kopiuje dane przyrostowe z systemu źródłowego do systemu docelowego w regularnych odstępach czasu.
3. Wskaż miejsce docelowe danych wyjściowych z systemu docelowego.
4. Przenieś, zmodyfikuj, uruchom wszystkie zadania w systemie docelowym.
5. Przekieruj źródła ruchu zbiorczego do systemu docelowego jako wygoda.
6. Gdy wszystkie źródła danych przychodzących wskazują system docelowy.
    1. Wyłącz kopiowanie przyrostowe.
    2. Wyłącz system źródłowy.

#### <a name="parallel-adoption"></a>Wdrożenie równoległe

1. Skonfiguruj system docelowy.
2. Skonfiguruj dwukierunkową replikację między systemem źródłowym a systemem docelowym.
3. Kierowanie przyrostowo źródeł do systemu docelowego.
4. Przenoszenie, modyfikowanie i uruchamianie zadań przyrostowo do systemu docelowego.
5. Wskaż przechodzące lokalizacje docelowe przyrostowo z systemu docelowego.
6. Gdy wszystkie oryginalne źródła ruchu przychodzącego, zadania i miejsce docelowe danych wyjściowych pracują z systemem docelowym, Wyłącz system źródłowy.

### <a name="data-upgrade"></a>Uaktualnianie danych

Ogólna strategia używana do przeprowadzania uaktualnienia (opisana w sekcji [strategia uaktualniania](#upgrade-strategy) w tym przewodniku) umożliwia określenie narzędzi, których można użyć do uaktualnienia danych. Narzędzia wymienione poniżej są oparte na bieżących informacjach i są sugestiami. 

#### <a name="tools-guidance"></a>Wskazówki dotyczące narzędzi

| Strategia                       | Narzędzia                                                                                                             | Formaty                                                                                                                             | Zagadnienia do rozważenia                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Podnieś i Przenieś**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Zarządzana usługa w chmurze                                                                                                                | Obecnie można kopiować zarówno dane, jak i listy ACL.                                                                                                                                                                                                                                                                      |
|                                    | [Pomocą distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Dobrze znane uprawnienia narzędzia usługi Hadoop, np. listy ACL można skopiować za pomocą tego narzędzia                                                   | Wymaga klastra, który może jednocześnie nawiązać połączenie z jednocześnie Data Lake Storage Gen1 i Gen2.                                                                                                                                                                                   |
| **Kopiowanie-jednorazowe i kopiowanie przyrostowe** | Azure Data Factory                                                                                                    | Zarządzana usługa w chmurze                                                                                                                | Obecnie można kopiować zarówno dane, jak i listy ACL. Aby obsłużyć kopiowanie przyrostowe w podajniku APD, dane muszą być zorganizowane w szeregach czasowych. Najkrótszy interwał kopiowania przyrostowego to [15 minut](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Wdrożenie równoległe**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Obsługa spójnej replikacji w przypadku korzystania z czystego środowiska Hadoop połączonego z Azure Data Lake Storage, która obsługuje replikację dwukierunkową | Jeśli nie korzystasz ze środowiska czystego usługi Hadoop, może wystąpić opóźnienie replikacji.                                                                                                                                                                                                                                                  |

Należy zauważyć, że istnieją inne firmy, które mogą obsłużyć Data Lake Storage Gen1 do Data Lake Storage Gen2 uaktualniania bez powyższego poziomu narzędzi do kopiowania danych/metadanych (na przykład: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Zapewniają one "jedno-stopnie", które przeprowadza migrację danych, a także migrację obciążeń. Może być konieczne przeprowadzenie uaktualnienia poza pasmem dla wszystkich narzędzi, które nie znajdują się poza ekosystemem.

#### <a name="considerations"></a>Zagadnienia do rozważenia

* Najpierw musisz ręcznie utworzyć konto Data Lake Storage Gen2, przed rozpoczęciem jakiejkolwiek części uaktualnienia, ponieważ bieżące wskazówki nie obejmują żadnych automatycznych procesów Gen2 na podstawie informacji o koncie Gen1. Należy porównać procesy tworzenia kont dla [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) i [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2 obsługuje tylko pliki o rozmiarze do 5 TB. W celu uaktualnienia do Data Lake Storage Gen2 może zajść potrzeba zmiany rozmiaru plików w Data Lake Storage Gen1, tak aby były mniejsze niż 5 TB.

* W przypadku korzystania z narzędzia, które nie kopiuje list ACL lub nie chcesz kopiować list ACL, należy ręcznie ustawić listy ACL w miejscu docelowym na odpowiednim najwyższego poziomu. Można to zrobić za pomocą Eksplorator usługi Storage. Upewnij się, że te listy ACL są domyślnymi listami ACL, tak aby pliki i foldery, które zostały skopiowane, dziedziczyły je.

* W Data Lake Storage Gen1 najwyższego poziomu można ustawić listy ACL w katalogu głównym konta. W Data Lake Storage Gen2 jednak najwyższy poziom można ustawić listy ACL w folderze głównym w kontenerze, a nie w całym koncie. Tak więc, jeśli chcesz ustawić domyślne listy ACL na poziomie konta, musisz zduplikować te wszystkie systemy plików w ramach konta Data Lake Storage Gen2.

* Ograniczenia dotyczące nazewnictwa plików są różne w obu systemach magazynowania. Te różnice są szczególnie przydatne w przypadku kopiowania z Data Lake Storage Gen2 do Data Lake Storage Gen1, ponieważ drugie z nich ma bardziej ograniczone ograniczenia.

### <a name="application-upgrade"></a>Uaktualnienie aplikacji

Gdy musisz tworzyć aplikacje na Data Lake Storage Gen1 lub Data Lake Storage Gen2, musisz najpierw wybrać odpowiedni interfejs programowania. Podczas wywoływania interfejsu API w tym interfejsie należy podać odpowiedni identyfikator URI i odpowiednie poświadczenia. Reprezentacja tych trzech elementów, interfejsu API, identyfikatora URI i sposobu podania poświadczeń, różni się między Data Lake Storage Gen1 i Data Lake Storage Gen2.So w ramach uaktualnienia aplikacji, należy odpowiednio zmapować te trzy konstrukcje.

#### <a name="uri-changes"></a>Zmiany identyfikatora URI

Głównym zadaniem jest przetłumaczenie identyfikatora URI, który ma prefiks `adl://` do identyfikatora URI, który ma prefiks `abfss://`.

Schemat identyfikatora URI dla Data Lake Storage Gen1 został szczegółowo opisany w [tym miejscu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) , ale ogólnie mówiąc, jest to *adl://mydatalakestore.azuredatalakestore.net/\<FILE_PATH\>.*

Schemat identyfikatora URI służący do uzyskiwania dostępu do plików Data Lake Storage Gen2 jest wyjaśniony [tutaj](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) , ale ogólnie mówiąc, jest `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`.

Musisz przejść przez istniejące aplikacje i upewnić się, że identyfikatory URI zostały odpowiednio zmienione, aby wskazywały Data Lake Storage Gen2. Należy również dodać odpowiednie poświadczenia. Na koniec wycofasz oryginalne aplikacje i Zastąp ją nową aplikacją, która będzie musiała zostać dokładnie wyrównana do ogólnej strategii uaktualniania.

#### <a name="custom-applications"></a>Aplikacje niestandardowe

W zależności od interfejsu używanej przez aplikację w programie Data Lake Storage Gen1 należy zmodyfikować go, aby dostosować go do Data Lake Storage Gen2.

##### <a name="rest-apis"></a>Interfejsy API REST

Jeśli aplikacja używa interfejsów API REST Data Lake Storage, należy zmodyfikować aplikację tak, aby korzystała Data Lake Storage Gen2 interfejsów API REST. Linki są dostępne w sekcji *interfejsy programowania* .

##### <a name="sdks"></a>Zestawy SDK

Zgodnie z opisem w sekcji *Ocena gotowości do uaktualnienia* zestawy SDK nie są obecnie dostępne. Jeśli chcesz, aby port przez aplikacje był Data Lake Storage Gen2, zalecamy zaczekanie, aż obsługiwane zestawy SDK będą dostępne.

##### <a name="powershell"></a>PowerShell

Jak zostało to opisane w sekcji Ocena gotowości do uaktualnienia, obsługa programu PowerShell nie jest obecnie dostępna dla płaszczyzny danych.

Można zastąpić polecenia cmdlet płaszczyzny zarządzania odpowiednimi przepisami w Data Lake Storage Gen2. Linki są dostępne w sekcji *interfejsy programowania* .

##### <a name="cli"></a>Interfejs CLI

Zgodnie z opisem w sekcji *Ocena gotowości do uaktualnienia* obsługa interfejsu wiersza polecenia nie jest obecnie dostępna dla płaszczyzny danych.

Polecenia płaszczyzny zarządzania można zastąpić odpowiednimi z nich w Data Lake Storage Gen2. Linki są dostępne w sekcji *interfejsy programowania* .

### <a name="analytics-frameworks-upgrade"></a>Uaktualnienie struktur analizy

Jeśli aplikacja tworzy metadane dotyczące informacji w sklepie, takich jak ścieżki do plików i folderów, należy wykonać dodatkowe akcje po uaktualnieniu danych/metadanych magazynu. Jest to szczególnie prawdziwe w przypadku struktur analitycznych, takich jak Azure HDInsight, datakosteks itp., które zazwyczaj tworzą dane katalogu w danych magazynu.

Struktury analityczne pracują z danymi i metami danych przechowywanymi w magazynach zdalnych, takich jak Data Lake Storage Gen1 i Gen2. Dlatego w teorii aparaty mogą być ulotne i działać tylko wtedy, gdy zadania wymagają uruchomienia względem przechowywanych danych.

Aby zoptymalizować wydajność, struktury analizy mogą jednak utworzyć jawne odwołania do plików i folderów przechowywanych w magazynie zdalnym, a następnie utworzyć pamięć podręczną do przechowywania. Jeśli identyfikator URI danych zdalnych zostanie zmieniony na przykład w klastrze, w którym przechowywane są dane w Data Lake Storage Gen1 wcześniejszej wersji, a teraz chcesz przechowywać w Data Lake Storage Gen2, identyfikator URI dla tej samej kopiowanej zawartości będzie różny. Dlatego po uaktualnieniu danych i metadanych pamięci podręcznej dla tych aparatów należy również zaktualizować lub ponownie zainicjować

W ramach procesu planowania należy zidentyfikować aplikację i ustalić, jak można ponownie zainicjować informacje meta-data, aby wskazywały dane, które są teraz przechowywane w Data Lake Storage Gen2. Poniżej przedstawiono wskazówki dotyczące powszechnie przyjętych platform analitycznych, które ułatwiają podejmowanie kroków uaktualniania.

#### <a name="azure-databricks"></a>Azure Databricks

Kroki będą się różnić w zależności od wybranej strategii uaktualniania. W bieżącej sekcji założono, że wybrano strategię "czas_życia-and-Shift". Ponadto istniejący obszar roboczy datakosteks, który służy do uzyskiwania dostępu do danych w ramach konta Data Lake Storage Gen1, powinien współpracować z danymi, które są kopiowane do Data Lake Storage Gen2 konta.

Najpierw upewnij się, że utworzono konto Gen2, a następnie skopiowano dane i meta z Gen1 do Gen2 przy użyciu odpowiedniego narzędzia. Te narzędzia są wywoływane w sekcji [uaktualnianie danych](#data-upgrade) w tym przewodniku.

Następnie [Uaktualnij](https://docs.azuredatabricks.net/user-guide/clusters/index.html) istniejący klaster datarangs, aby rozpocząć korzystanie z programu datakostks Runtime 5,1 lub nowszego, który powinien obsługiwać Data Lake Storage Gen2.

Następnie kroki są zależne od tego, jak istniejący obszar roboczy datakostki uzyskuje dostęp do danych na koncie Data Lake Storage Gen1. Można to zrobić, wywołując identyfikatory URI adl:// [bezpośrednio](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) z notesów lub za pośrednictwem [mountpoints](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Jeśli uzyskujesz dostęp bezpośrednio z notesów, dostarczając pełne identyfikatory URI adl://, musisz przejść przez każdy Notes i zmienić konfigurację w celu uzyskania dostępu do odpowiedniego identyfikatora URI Data Lake Storage Gen2.

W przód należy ponownie skonfigurować tę funkcję, aby wskazywała konto Data Lake Storage Gen2. Nie są konieczne żadne zmiany, a notesy powinny być w stanie działać tak jak wcześniej.

W przypadku korzystania z innych strategii uaktualniania można utworzyć odmianę powyższych kroków w celu spełnienia wymagań.

### <a name="azure-ecosystem-upgrade"></a>Uaktualnienie ekosystemu platformy Azure

Każdy z narzędzi i usług, które zostały wywołane w sekcji [ekosystem platformy Azure](#azure-ecosystem) tego przewodnika, będzie musiał zostać skonfigurowany do pracy z Data Lake Storage Gen2.

Najpierw upewnij się, że dostępna jest integracja z usługą Data Lake Storage Gen2.

Następnie elementy o nazwie powyżej (na przykład: identyfikator URI i poświadczenia) będą musiały zostać zmienione. Można modyfikować istniejące wystąpienie działające z Data Lake Storage Gen1 lub utworzyć nowe wystąpienie, które będzie działać z Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Uaktualnienie ekosystemu partnera

Skontaktuj się z partnerem dostarczającym składnik i narzędzia, aby upewnić się, że mogą oni współpracować z Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Przeprowadzanie uaktualnienia

### <a name="pre-upgrade"></a>Przed uaktualnieniem

W ramach tego przewodnika nastąpiło przechodzenie przez sekcję *Ocena gotowości do uaktualnienia* i [zaplanowanie uaktualnienia w](#planning-for-an-upgrade) tym podręczniku, otrzymano wszystkie niezbędne informacje i utworzono plan, który spełnia Twoje potrzeby. Prawdopodobnie będziesz mieć zadanie testowania w tej fazie.

### <a name="in-upgrade"></a>W ramach uaktualnienia

W zależności od wybranej strategii i złożoności rozwiązania ta faza może być krótką lub rozszerzoną, w której istnieje wiele obciążeń oczekujących na przyrostowe przenoszenie do Data Lake Storage Gen2. Będzie to najbardziej krytyczna część uaktualnienia.

### <a name="post-upgrade"></a>Po uaktualnieniu

Po wykonaniu operacji przejścia końcowe kroki będą obejmowały gruntowną weryfikację. Może to obejmować, ale nie będzie ograniczony do weryfikowania danych, które zostały wcześniej skopiowane, zweryfikowanie list ACL zostało prawidłowo ustawione. Sprawdzanie, czy kompleksowe potoki działają prawidłowo itd. Po zakończeniu weryfikacji możesz teraz wyłączyć stare potoki, usunąć źródłowe konta Data Lake Storage Gen1 i uzyskać pełną szybkość rozwiązań opartych na Data Lake Storage Gen2.

## <a name="conclusion"></a>Podsumowanie

Wskazówki przedstawione w tym dokumencie powinny pomóc w uaktualnieniu rozwiązania do korzystania z Data Lake Storage Gen2. 

Jeśli masz więcej pytań lub masz opinię, podaj poniższe komentarze lub Prześlij opinię na [forum opinii na platformie Azure](https://feedback.azure.com/forums/327234-data-lake).
