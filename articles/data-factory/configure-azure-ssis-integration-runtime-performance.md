---
title: Konfigurowanie środowiska uruchomieniowego integracji usług SSIS Azure o wysokiej wydajności | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować właściwości środowiska uruchomieniowego integracji usług SSIS Azure o wysokiej wydajności
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 876f68d232e5f171f0c03b653d56f2351ffb2c7d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617131"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurowanie środowiska uruchomieniowego integracji usług SSIS Azure o wysokiej wydajności

W tym artykule opisano sposób konfigurowania usług SSIS Azure integrację środowiska uruchomieniowego (IR) o wysokiej wydajności. IR Azure SSIS umożliwia wdrażanie i uruchamianie pakietów programu SQL Server Integration Services (SSIS) na platformie Azure. Aby uzyskać więcej informacji na temat IR Azure SSIS, zobacz [integrację środowiska uruchomieniowego](concepts-integration-runtime.md#azure-ssis-integration-runtime) artykułu. Aby uzyskać informacje dotyczące wdrażania i uruchamiania pakietów SSIS na platformie Azure, zobacz [przyrostu i shift obciążeń usług integracji programu SQL Server do chmury](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Ten artykuł zawiera wyniki wydajności i uwagi z wewnętrznych testowania wykonywane przez członków zespołu deweloperów SSIS. Wyniki mogą być różne. Do testowania przed finalize ustawień konfiguracji, które wpływają na wydajność i koszty.

## <a name="properties-to-configure"></a>Właściwości do skonfigurowania

Następujące części skryptu konfiguracji zawiera właściwości, które można skonfigurować podczas tworzenia środowiska uruchomieniowego integracji usług SSIS Azure. Ukończ skrypt programu PowerShell i opis, zobacz [pakiety wdrażania programu SQL Server Integration Services na platformie Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** to lokalizacja, w węźle integracji środowiska wykonawczego procesu roboczego. Z węzłem procesu roboczego przechowuje stałe połączenie z bazą danych usług SSIS katalogu (SSISDB) w bazie danych Azure SQL. Ustaw **AzureSSISLocation** w tej samej lokalizacji co serwer bazy danych SQL, który jest hostem bazy danych SSISDB, który umożliwia integrację środowiska uruchomieniowego działa jak najbardziej wydajny.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
W publicznej wersji zapoznawczej v2 fabryki danych Azure, w tym IR Azure SSIS obsługuje następujące opcje:
-   Standardowe\_A4\_v2
-   Standardowe\_A8\_v2
-   Standardowe\_D1\_v2
-   Standardowe\_D2\_v2
-   Standardowe\_D3\_v2
-   Standardowe\_D4\_v2.

Testami nieoficjalny wewnętrznych przez zespół inżynieryjny SSIS, serii D wydaje się być bardziej odpowiednie dla wykonanie pakietu usług SSIS niż serii A.

-   Współczynnik wydajności ceny serii D jest wyższa niż serii A.
-   Przepływność serii D jest wyższa niż serii A w tej samej cenie.

### <a name="configure-for-execution-speed"></a>Konfigurowanie szybkości wykonywania
Jeśli masz wiele pakietów do uruchomienia, i chcesz pakietów, aby szybko uruchomić, skorzystaj z informacji w poniższej tabeli, aby wybrać odpowiednie dla danego scenariusza typ maszyny wirtualnej.

Te dane reprezentuje wykonanie pojedynczy pakiet, w węźle pojedynczego procesu roboczego. Pakiet ładuje 10 miliony rekordów o nazwie pierwszy i ostatni nazwa kolumny z magazynu obiektów Blob platformy Azure, generuje kolumny Pełna nazwa i zapisuje rekordy, które mają pełną nazwę dłuższa niż 20 znaków do magazynu obiektów Blob Azure.

![Szybkość wykonywania pakietu środowiska uruchomieniowego integracji usług SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Konfigurowanie dla ogólną przepustowość

Jeśli masz wiele pakietów do uruchomienia, a najbardziej Cię interesują ogólną przepustowość, skorzystaj z informacji w poniższej tabeli, aby wybrać odpowiednie dla danego scenariusza typ maszyny wirtualnej.

![Środowisko uruchomieniowe integracji usług SSIS ogólną przepustowość maksymalna](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** dostosowuje skalowalność środowiska uruchomieniowego integracji. Przepływność środowiska uruchomieniowego integracji jest proporcjonalny do **AzureSSISNodeNumber**. Ustaw **AzureSSISNodeNumber** małej wartości na początku, monitorować wydajność środowiska uruchomieniowego integracji, a następnie Dostosuj wartość dla danego scenariusza. Aby zmienić konfigurację liczba węzłów procesu roboczego, zobacz [Zarządzanie środowiska uruchomieniowego integracji usług SSIS Azure](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Jeśli już korzystasz z węzłem procesu roboczego zaawansowane do uruchamiania pakietów, zwiększenie **AzureSSISMaxParallelExecutionsPerNode** może zwiększyć ogólną przepustowość środowiska uruchomieniowego integracji. Dla węzłów Standard_D1_v2 1-4 wykonaniami równoległego na węzeł są obsługiwane. Dla wszystkich innych typów węzłów 1 – 8 wykonaniami równoległego na węzeł są obsługiwane.
Można oszacować odpowiednią wartość, na podstawie jej kosztu pakietu i następujące konfiguracje węzłów procesu roboczego. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych ogólnego przeznaczenia](../virtual-machines/windows/sizes-general.md).

| Rozmiar             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana wydajność sieci (Mb/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standardowe\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2 x 500                         | 2 / 750                                        |
| Standardowe\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4 x 500                         | 2 / 1500                                       |
| Standardowe\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8 x 500                         | 4 / 3000                                       |
| Standardowe\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16 x 500                       | 8 / 6000                                       |
| Standardowe\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8 x 500                         | 4 / 1000                                       |
| Standardowe\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16 x 500                       | 8 / 2000                                       |

Poniżej przedstawiono wskazówki dotyczące ustawiania wartości prawo **AzureSSISMaxParallelExecutionsPerNode** właściwości: 

1. Ustaw ją na małej wartości na początku.
2. Zwiększyć jej niewielkie, aby sprawdzić, czy poprawia ogólną przepustowość.
3. Zatrzymaj, zwiększenie wartości, gdy ogólną przepustowość osiągnie wartość maksymalna.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** jest warstwę cenową dla wykazu SSIS bazy danych (SSISDB) w bazie danych Azure SQL. To ustawienie wpływa na maksymalną liczbę pracowników w wystąpieniu IR, szybkość do kolejki wykonywania pakietu i szybkość załadować dziennika wykonywania.

-   Jeśli nie interesują o szybkości wykonywania pakietu kolejki i załadować dziennik wykonywania, można wybrać najniższej warstwy cenowej bazy danych. Baza danych SQL Azure z podstawowe ceny obsługuje 8 pracowników w wystąpieniu środowiska uruchomieniowego integracji.

-   Wybierz bardziej zaawansowanych bazy danych niż podstawowy, jeśli liczba procesu roboczego jest więcej niż 8 lub liczby rdzeni jest więcej niż 50. W przeciwnym razie wąskie gardło będące przyczyną wystąpienia środowiska uruchomieniowego integracji rozmiaru bazy danych i negatywny wpływ na ogólną wydajność.

Można również dostosować bazy danych na podstawie warstwy cenowej [jednostka transakcji bazy danych](../sql-database/sql-database-what-is-a-dtu.md) (bazy danych DTU) informacje o użyciu dostępne w portalu Azure.

## <a name="design-for-high-performance"></a>Projektowanie pod kątem wysokiej wydajności
Projektowanie pakietu SSIS do uruchamiania na platformie Azure różni się od projektowania pakietu w celu wykonania lokalnego. Zamiast łączenie wielu niezależnych zadań w tym samym pakiecie, podzielone na kilka pakietów efektywniejsze wykonywanie w podczerwieni Azure SSIS. Utwórz wykonanie pakietu dla każdego pakietu, dzięki czemu nie trzeba czekać dla pozostałych do zakończenia. Ta metoda korzysta z skalowalność środowiska uruchomieniowego integracji usług SSIS Azure i zwiększa ogólną przepustowość.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat środowiska uruchomieniowego integracji usług SSIS Azure. Zobacz [środowiska uruchomieniowego integracji usług SSIS Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime).
