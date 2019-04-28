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
ms.openlocfilehash: 271da0a6ff443fcee28bc870821f4222b3018c91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262240"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurowanie środowiska Azure-SSIS Integration Runtime, dla wysoko wydajnych

W tym artykule opisano sposób konfigurowania środowiska Azure-SSIS Integration Runtime (IR) zapewniające wysoką wydajność. Azure-SSIS IR umożliwia wdrażanie i uruchamianie pakietów usług SQL Server Integration Services (SSIS) na platformie Azure. Aby uzyskać więcej informacji na temat środowiska Azure-SSIS IR, zobacz [środowiska Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) artykułu. Aby uzyskać informacje o wdrażanie i uruchamianie pakietów SSIS na platformie Azure, zobacz [Lift- and -shift obciążeń programu SQL Server Integration Services chmurze](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Ten artykuł zawiera wyniki dotyczące wydajności i uwagi z testów wewnętrznych wykonywane przez członków zespołu rozwoju usług SSIS. Wyniki mogą się różnić. Samodzielnie przeprowadzić testy przed finalize ustawień konfiguracji, które mają wpływ na wydajność i koszt.

## <a name="properties-to-configure"></a>Właściwości, aby skonfigurować

Następujące części skryptu konfiguracji zawiera właściwości, które można skonfigurować podczas tworzenia środowiska Azure-SSIS Integration Runtime. Aby uzyskać kompletny skrypt programu PowerShell i opis, zobacz [pakiety wdrażania programu SQL Server Integration Services na platformę Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Database Managed Instance
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
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
-   Standardowa\_D4\_v2.

W nieoficjalny wewnętrznych testowania przez zespół inżynierów SSIS, seria D wydaje się być bardziej odpowiednie dla wykonanie pakietu SSIS niż seria A.

-   Współczynnik wydajności/cena serii D jest wyższa niż seria A.
-   Przepływność dla serii D jest wyższa niż seria A w tej samej cenie.

### <a name="configure-for-execution-speed"></a>Konfigurowanie dla szybkości wykonywania
Jeśli masz wiele pakietów do uruchomienia i chcesz, aby pakiety do szybkiego uruchamiania, skorzystaj z informacji w poniższej tabeli, aby wybrać odpowiednie dla danego scenariusza typ maszyny wirtualnej.

Wykonanie pojedynczy pakiet, w węźle pojedynczego procesu roboczego reprezentują te dane. Ten pakiet ładuje 10 milionów rekordów o nazwie pierwszej i ostatniej kolumny z magazynu obiektów Blob platformy Azure, generuje kolumny Imię i nazwisko i zapisuje rekordy, które mają pełną nazwę w więcej niż 20 znaków w usłudze Azure Blob Storage.

![Szybkość wykonywania pakietów SSIS Integration Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Konfigurowanie dla ogólną przepływność

Jeśli masz wiele pakietów do uruchomienia, a najbardziej interesujących Cię ogólną przepływność, skorzystaj z informacji w poniższej tabeli, aby wybrać odpowiednie dla danego scenariusza typ maszyny wirtualnej.

![Środowiska SSIS Integration Runtime, ogólną przepustowość](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** dostosowuje skalowalność produktu integration runtime. Przepływność środowiska integration runtime jest proporcjonalna do **AzureSSISNodeNumber**. Ustaw **AzureSSISNodeNumber** małej wartości na początku, monitorowanie przepływności środowiska integration runtime, a następnie dostosować wartości dla danego scenariusza. Aby ponownie skonfigurować liczby węzłów procesu roboczego, zobacz [Zarządzanie środowiska Azure-SSIS integration runtime](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Jeśli już korzystasz z węzłem procesu roboczego wydajne uruchamianie pakietów, zwiększając **AzureSSISMaxParallelExecutionsPerNode** może zwiększyć ogólną przepływność środowiska integration Runtime. Maszyna wirtualna Standard_D1_v2 węzłów 1 – 4 równoległych wykonań na węzeł są obsługiwane. Dla wszystkich innych typów węzłów 1-8 równoległych wykonań na węzeł są obsługiwane.
Można oszacować odpowiednią wartość na podstawie jej kosztu pakietu i konfiguracje dla węzłów procesu roboczego. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych ogólnego przeznaczenia](../virtual-machines/windows/sizes-general.md).

| Rozmiar             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Operacje We/Wy / odczyt MB/s / Zapis MB/s | Maksymalna liczba dysków danych / przepływność: Operacje wejścia/wyjścia | Maksymalna liczba kart sieciowych/oczekiwana wydajność sieci (Mb/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standardowa\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2 x 500                         | 2 / 750                                        |
| Standardowa\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4 x 500                         | 2 / 1500                                       |
| Standardowa\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8 x 500                         | 4 / 3000                                       |
| Standardowa\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16 x 500                       | 8 / 6000                                       |
| Standardowa\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8 x 500                         | 4 / 1000                                       |
| Standardowa\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16 x 500                       | 8 / 2000                                       |

Poniżej przedstawiono wskazówki dotyczące ustawiania odpowiednie wartości dla **AzureSSISMaxParallelExecutionsPerNode** właściwości: 

1. Ustaw ją na małej wartości na początku.
2. Stopniowe zwiększanie on małą ilością do sprawdzenia, czy została udoskonalona ogólną przepływność.
3. Zatrzymaj, zwiększenie wartości, gdy ogólną przepływność osiągnie wartość maksymalna.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** jest warstwę cenową dla bazy danych wykazu usług SSIS (SSISDB) na bazę danych Azure SQL database. To ustawienie wpływa na maksymalną liczbę procesów roboczych w wystąpienie środowiska IR, szybkość wykonywania pakietów w kolejce oraz szybkość załadować dziennika wykonywania.

-   Jeśli nie dba o szybkość wykonywania pakietów kolejki i załaduj dziennik wykonywania, można wybierać najniższej warstwy cenowej bazy danych. Usługa Azure SQL Database dzięki cenom podstawowych obsługuje 8 procesów roboczych w wystąpienie infrastruktury integration runtime.

-   Wybierz bazę danych bardziej wydajne niż wersja podstawowa, czy liczba procesów roboczych jest więcej niż 8, liczba rdzeni wynosi więcej niż 50. W przeciwnym razie bazy danych staje się wąskim gardłem wystąpienia środowiska uruchomieniowego integracji i ogólną wydajność jest obniżona.

Można również dostosować bazy danych na podstawie warstwy cenowej [jednostek transakcji bazy danych](../sql-database/sql-database-what-is-a-dtu.md) (DTU) informacje o użyciu w witrynie Azure portal.

## <a name="design-for-high-performance"></a>Projektowanie pod kątem wysokiej wydajności
Projektowanie pakietu usług SSIS na platformie Azure różni się od projektowania pakietu do wykonania w środowisku lokalnym. Zamiast łączenie wielu niezależnych zadań, w tym samym pakiecie podzielone na kilka pakietów, aby uzyskać bardziej wydajne wykonywanie w programie Azure-SSIS IR. Wykonanie pakietu dla każdego pakietu, należy utworzyć tak, aby nie musieli czekać na siebie, aby zakończyć. Ta metoda korzysta ze skalowania środowiska Azure-SSIS integration runtime i zwiększa ogólną przepustowość.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o usłudze Azure-SSIS Integration Runtime. Zobacz [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
