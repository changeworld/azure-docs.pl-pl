---
title: Konfigurowanie wydajności środowiska Azure-SSIS Integration Runtime | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować właściwości środowiska Azure-SSIS Integration Runtime, dla wysoko wydajnych
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 42c69653a002446552da998320a43730dfdaadf5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232527"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurowanie środowiska Azure-SSIS Integration Runtime, dla wysoko wydajnych

W tym artykule opisano sposób konfigurowania środowiska Azure-SSIS Integration Runtime (IR) zapewniające wysoką wydajność. Azure-SSIS IR umożliwia wdrażanie i uruchamianie pakietów usług SQL Server Integration Services (SSIS) na platformie Azure. Aby uzyskać więcej informacji na temat środowiska Azure-SSIS IR, zobacz [środowiska Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) artykułu. Aby uzyskać informacje o wdrażanie i uruchamianie pakietów SSIS na platformie Azure, zobacz [Lift- and -shift obciążeń programu SQL Server Integration Services chmurze](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Ten artykuł zawiera wyniki dotyczące wydajności i uwagi z testów wewnętrznych wykonywane przez członków zespołu rozwoju usług SSIS. Wyniki mogą się różnić. Samodzielnie przeprowadzić testy przed finalize ustawień konfiguracji, które mają wpływ na wydajność i koszt.

## <a name="properties-to-configure"></a>Właściwości, aby skonfigurować

Następujące części skryptu konfiguracji zawiera właściwości, które można skonfigurować podczas tworzenia środowiska Azure-SSIS Integration Runtime. Aby uzyskać kompletny skrypt programu PowerShell i opis, zobacz [pakiety wdrażania programu SQL Server Integration Services na platformę Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** znajdują się węzeł procesu roboczego środowiska integration runtime. Węzeł procesu roboczego obsługuje stałe połączenie z bazą danych wykazu usług SSIS (SSISDB) na bazę danych Azure SQL database. Ustaw **AzureSSISLocation** w tej samej lokalizacji co serwer bazy danych SQL, który jest hostem bazy danych SSISDB, co pozwoli środowiska integration runtime do pracy w jak najbardziej wydajny.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Usługa Data Factory, Azure-SSIS IR, w tym obsługuje następujące opcje:
-   Standardowa\_A4\_v2
-   Standardowa\_A8\_v2
-   Standardowa\_D1\_v2
-   Standardowa\_D2\_v2
-   Standardowa\_D3\_v2
-   Standardowa\_D4\_v2
-   Standardowa\_D2\_v3
-   Standardowa\_D4\_v3
-   Standardowa\_D8\_v3
-   Standardowa\_D16\_v3
-   Standardowa\_D32\_v3
-   Standardowa\_D64\_v3
-   Standardowa\_E2\_v3
-   Standardowa\_E4\_v3
-   Standardowa\_E8\_v3
-   Standardowa\_E16\_v3
-   Standardowa\_E32\_v3
-   Standardowa\_E64\_v3

W nieoficjalny wewnętrznych testowania przez zespół inżynierów SSIS, seria D wydaje się być bardziej odpowiednie dla wykonanie pakietu SSIS niż seria A.

-   Współczynnik wydajności/cena serii D jest wyższa niż seria A i współczynnik wydajności/cena serii v3 jest wyższa niż seria v2.
-   Przepływność dla serii D jest wyższa niż seria A w tej samej cenie i Przepływność dla serii v3 jest wyższy niż seria v2 w tej samej cenie.
-   Węzły serii v2 Azure-SSIS IR nie są odpowiednie dla ustawień niestandardowych, aby zamiast tego użyj wersji 3 węzły serii. Jeśli już używasz wersji 2 węzły serii, Przełącz do użycia w wersji 3 węzły serii tak szybko, jak to możliwe.
-   Seria E jest zoptymalizowane pod kątem pamięci rozmiary maszyn wirtualnych, które zapewnia wyższy stosunek pamięci do procesorów niż w przypadku innych maszyn. Jeśli pakiet wymaga dużej ilości pamięci, należy rozważyć, wybierając maszyny Wirtualnej serii E.

### <a name="configure-for-execution-speed"></a>Konfigurowanie dla szybkości wykonywania
Jeśli masz wiele pakietów do uruchomienia i chcesz, aby pakiety do szybkiego uruchamiania, skorzystaj z informacji w poniższej tabeli, aby wybrać odpowiednie dla danego scenariusza typ maszyny wirtualnej.

Wykonanie pojedynczy pakiet, w węźle pojedynczego procesu roboczego reprezentują te dane. Ten pakiet ładuje 3 miliony rekordów o nazwie pierwszej i ostatniej kolumny z magazynu obiektów Blob platformy Azure, generuje kolumny Imię i nazwisko i zapisuje rekordy, które mają pełną nazwę w więcej niż 20 znaków w usłudze Azure Blob Storage.

![Szybkość wykonywania pakietów SSIS Integration Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurowanie dla ogólną przepływność

Jeśli masz wiele pakietów do uruchomienia, a najbardziej interesujących Cię ogólną przepływność, skorzystaj z informacji w poniższej tabeli, aby wybrać odpowiednie dla danego scenariusza typ maszyny wirtualnej.

![Środowiska SSIS Integration Runtime, ogólną przepustowość](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** dostosowuje skalowalność produktu integration runtime. Przepływność środowiska integration runtime jest proporcjonalna do **AzureSSISNodeNumber**. Ustaw **AzureSSISNodeNumber** małej wartości na początku, monitorowanie przepływności środowiska integration runtime, a następnie dostosować wartości dla danego scenariusza. Aby ponownie skonfigurować liczby węzłów procesu roboczego, zobacz [Zarządzanie środowiska Azure-SSIS integration runtime](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Jeśli już korzystasz z węzłem procesu roboczego wydajne uruchamianie pakietów, zwiększając **AzureSSISMaxParallelExecutionsPerNode** może zwiększyć ogólną przepływność środowiska integration Runtime. Maszyna wirtualna Standard_D1_v2 węzłów 1 – 4 równoległych wykonań na węzeł są obsługiwane. Dla wszystkich innych typów węzłów 1 max(2 x number of cores, 8) równoległych wykonań na węzeł są obsługiwane. Jeśli chcesz **AzureSSISMaxParallelExecutionsPerNode** przekracza maksymalną wartość obsługiwane, możesz otworzyć bilet pomocy technicznej i firma Microsoft może zwiększyć maksymalną wartość dla Ciebie i po nim, należy zaktualizować przy użyciu programu Azure Powershell  **AzureSSISMaxParallelExecutionsPerNode**.
Można oszacować odpowiednią wartość na podstawie jej kosztu pakietu i konfiguracje dla węzłów procesu roboczego. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych ogólnego przeznaczenia](../virtual-machines/windows/sizes-general.md).

| Rozmiar             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Operacje We/Wy / odczyt MB/s / Zapis MB/s | Maksymalna liczba dysków danych / przepływność: Operacje wejścia/wyjścia | Maksymalna liczba kart sieciowych/oczekiwana wydajność sieci (Mb/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standardowa\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2 x 500                         | 2 / 750                                        |
| Standardowa\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4 x 500                         | 2 / 1500                                       |
| Standardowa\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8 x 500                         | 4 / 3000                                       |
| Standardowa\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16 x 500                       | 8 / 6000                                       |
| Standardowa\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8 x 500                         | 4 / 1000                                       |
| Standardowa\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16 x 500                       | 8 / 2000                                       |
| Standardowa\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6 x 500                         | 2 / 1000                                       |
| Standardowa\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12 x 500                        | 2 / 2000                                       |
| Standardowa\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24 x 500                       | 4 / 4000                                       |
| Standardowa\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32 / 48 x 500                        | 8 / 8000                                       |
| Standardowa\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96 x 500                       | 8 / 16 000                                      |
| Standardowa\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192 x 500                      | 8 / 30000                                      |
| Standardowa\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6 x 500                         | 2 / 1000                                       |
| Standardowa\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12 x 500                        | 2 / 2000                                       |
| Standardowa\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24 x 500                       | 4 / 4000                                       |
| Standardowa\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48 x 500                       | 8 / 8000                                       |
| Standardowa\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96 x 500                       | 8 / 16 000                                      |
| Standardowa\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192 x 500                      | 8 / 30000                                      |

Poniżej przedstawiono wskazówki dotyczące ustawiania odpowiednie wartości dla **AzureSSISMaxParallelExecutionsPerNode** właściwości: 

1. Ustaw ją na małej wartości na początku.
2. Stopniowe zwiększanie on małą ilością do sprawdzenia, czy została udoskonalona ogólną przepływność.
3. Zatrzymaj, zwiększenie wartości, gdy ogólną przepływność osiągnie wartość maksymalna.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** jest warstwę cenową dla bazy danych wykazu usług SSIS (SSISDB) na bazę danych Azure SQL database. To ustawienie wpływa na maksymalną liczbę procesów roboczych w wystąpienie środowiska IR, szybkość wykonywania pakietów w kolejce oraz szybkość załadować dziennika wykonywania.

-   Jeśli nie dba o szybkość wykonywania pakietów kolejki i załaduj dziennik wykonywania, można wybierać najniższej warstwy cenowej bazy danych. Usługa Azure SQL Database dzięki cenom podstawowych obsługuje 8 procesów roboczych w wystąpienie infrastruktury integration runtime.

-   Wybierz bazę danych bardziej wydajne niż wersja podstawowa, czy liczba procesów roboczych jest więcej niż 8, liczba rdzeni wynosi więcej niż 50. W przeciwnym razie bazy danych staje się wąskim gardłem wystąpienia środowiska uruchomieniowego integracji i ogólną wydajność jest obniżona.

-   Wybierz bardziej wydajne bazy danych, np. s3, jeśli poziom rejestrowania jest ustawiony na pełne. Zgodnie z naszym nieoficjalny testów wewnętrznych, warstwy cenowej s3 może obsługiwać wykonanie pakietu SSIS przy użyciu 2 węzłów, 128 liczby równolegle i na poziomie pełne rejestrowanie.

Można również dostosować bazy danych na podstawie warstwy cenowej [jednostek transakcji bazy danych](../sql-database/sql-database-what-is-a-dtu.md) (DTU) informacje o użyciu w witrynie Azure portal.

## <a name="design-for-high-performance"></a>Projektowanie pod kątem wysokiej wydajności
Projektowanie pakietu usług SSIS na platformie Azure różni się od projektowania pakietu do wykonania w środowisku lokalnym. Zamiast łączenie wielu niezależnych zadań, w tym samym pakiecie podzielone na kilka pakietów, aby uzyskać bardziej wydajne wykonywanie w programie Azure-SSIS IR. Wykonanie pakietu dla każdego pakietu, należy utworzyć tak, aby nie musieli czekać na siebie, aby zakończyć. Ta metoda korzysta ze skalowania środowiska Azure-SSIS integration runtime i zwiększa ogólną przepustowość.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o usłudze Azure-SSIS Integration Runtime. Zobacz [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
