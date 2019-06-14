---
title: Monitorowanie środowiska integration runtime w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować różne typy infrastruktury integration runtime w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/25/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: b62cbe75730da8c5764839d41887deb7e6cd0e90
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122612"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorowanie środowiska integration runtime w usłudze Azure Data Factory  
**Środowisko Integration runtime** jest infrastruktura obliczeniowa używana przez usługę Azure Data Factory, aby zapewnić różne możliwości integracji danych w różnych środowiskach sieciowych. Istnieją trzy typy środowiska integration Runtime oferowane przez usługę Data Factory:

- Środowisko uruchomieniowe integracji Azure
- Infrastruktura Integration Runtime (Self-hosted)
- Azure SSIS Integration Runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby uzyskać stan wystąpienia środowiska integration Runtime (IR), uruchom następujące polecenie programu PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Polecenie cmdlet zwraca różne informacje dla różnych typów środowiska integration runtime. W tym artykule opisano, właściwości i Stany dla każdego typu środowiska integration runtime.  

## <a name="azure-integration-runtime"></a>Środowisko uruchomieniowe integracji Azure
Zasób obliczeniowy do środowiska Azure integration runtime jest w pełni zarządzana elastycznie na platformie Azure. Poniższa tabela zawiera opis właściwości zwrócony przez **Get AzDataFactoryV2IntegrationRuntime** polecenia:

### <a name="properties"></a>Właściwości
Poniższa tabela zawiera opisy właściwości zwracany przez polecenie cmdlet środowiska Azure integration Runtime:

| Właściwość | Opis |
-------- | ------------- | 
| Name (Nazwa) | Nazwa środowiska Azure integration runtime. |  
| Stan | Stan środowiska Azure integration runtime. | 
| Lokalizacja | Lokalizacja IR platformy Azure. Aby uzyskać szczegółowe informacje o lokalizacji środowiska Azure integration runtime, zobacz [wprowadzenie do produktu integration runtime](concepts-integration-runtime.md). |
| DataFactoryName | Nazwa fabryki danych, do której należy produkt Azure integration runtime. | 
| ResourceGroupName | Nazwa grupy zasobów, do której należy z fabryką danych.  |
| Opis | Opis produktu integration runtime.  |

### <a name="status"></a>Stan
W poniższej tabeli przedstawiono możliwe stany środowiska Azure integration runtime:

| Stan | Komentarze/scenariusze | 
| ------ | ------------------ |
| Online | Środowisko uruchomieniowe integracji platformy Azure jest dostępna online i gotowa do użycia. | 
| Offline | Produkt Azure integration runtime jest w trybie offline z powodu błędu wewnętrznego. |

## <a name="self-hosted-integration-runtime"></a>Infrastruktura Integration Runtime (Self-hosted)
Ta sekcja zawiera opis właściwości zwracany przez polecenie cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Zwrócone właściwości i stan zawierają informacje dotyczące ogólnej własnego środowiska integration runtime i każdego węzła w środowisku uruchomieniowym.  

### <a name="properties"></a>Właściwości

Poniższa tabela zawiera opis monitorowania właściwości **każdy węzeł**:

| Właściwość | Opis | 
| -------- | ----------- | 
| Name (Nazwa) | Nazwa własnego środowiska integration runtime i skojarzonych z nim węzłów. Węzeł to maszyna Windows w środowisku lokalnym, który ma własne środowisko integration runtime na nim zainstalowany. |  
| Stan | Stan ogólny własnego środowiska integration runtime i każdego węzła. Przykład: Online/Offline/Limited/itd. Dla informacji o tych stanów zobacz następną sekcję. | 
| Wersja | Wersja własnego środowiska integration runtime i każdego węzła. Wersja własnego środowiska integration runtime jest określana na podstawie wersji Większość węzłów w grupie. W przypadku węzłów z różnymi wersjami w instalacji Self-Hosted integration runtime tylko węzły numerem wersji jako logicznej może być samodzielnie hostowane funkcji środowiska uruchomieniowego integracji prawidłowo. Innym jest tryb ograniczony i należy ręcznie zaktualizować (tylko w przypadku, gdy aktualizacje automatyczne nie powiedzie się). | 
| Dostępna pamięć | Dostępna pamięć na węzeł Self-Hosted integration runtime. Ta wartość jest niemal w czasie rzeczywistym migawki. | 
| Użycie procesora CPU | Użycie procesora CPU węzeł Self-Hosted integration runtime. Ta wartość jest niemal w czasie rzeczywistym migawki. |
| Sieć (We/Wy) | Wykorzystanie sieci węzeł Self-Hosted integration runtime. Ta wartość jest niemal w czasie rzeczywistym migawki. | 
| Równoczesne zadania (uruchomione / Limit) | **Uruchamianie**. Numer zadania lub podzadania uruchomione w każdym węźle. Ta wartość jest niemal w czasie rzeczywistym migawki. <br/><br/>**Limit**. Limit oznacza maksymalna liczba równoczesnych zadań dla każdego węzła. Ta wartość jest określona na podstawie rozmiaru maszyny. Możesz zwiększyć limit, skalowanie w górę wykonywania zadań jednoczesnych w zaawansowanych scenariuszach podczas działania przekraczają limit nawet jeśli Procesor, pamięć lub sieć jest niewykorzystywanych. Ta funkcja jest również dostępne z pojedynczym węzłem własnego środowiska integration runtime. |
| Rola | Istnieją dwa typy ról wielowęzłowego własnego środowiska integration runtime — dyspozytora i proces roboczy. Wszystkie węzły są pracowników, co oznacza, że ich można wszystkie służyć do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora, który jest używany do pobierania zadania/zadań z usług cloud services i wysyłać je do innego procesu roboczego węzłów. Ten węzeł dyspozytora również jest węzłem procesu roboczego. |

Niektóre ustawienia właściwości będą bardziej zrozumiałe, gdy istnieją co najmniej dwa węzły w własnego środowiska integration runtime (czyli w skalowanym scenariusz).

#### <a name="concurrent-jobs-limit"></a>Limit współbieżnych zadań

Wartość domyślna równoczesnych zadań, których limit jest ustawiony na podstawie rozmiaru maszyny. Czynniki używane do obliczania tej wartości zależy od tego, ilość pamięci RAM i liczba rdzeni procesora CPU maszyny. Dlatego więcej rdzeni i więcej pamięci, tym wyższa domyślny limit współbieżnych zadań.

Możesz skalować w poziomie przez odpowiednie zwiększenie liczby węzłów. Wraz ze zwiększeniem liczby węzłów limit współbieżnych zadań jest sumą wartości limit współbieżnych zadania wszystkich dostępnych węzłów.  Na przykład jeśli jeden węzeł można uruchomić maksymalnie 12 równoczesnych zadań, następnie dodając trzy węzły bardziej przypominające umożliwia uruchamianie maksymalnie 48 równoczesnych zadań (czyli 4 x 12). Firma Microsoft zaleca, zwiększ limit współbieżnych zadań, tylko wtedy, gdy zostanie wyświetlony w każdym węźle niskie użycie zasobów z wartościami domyślnymi.

Można zastąpić wartością domyślną obliczeniowe w witrynie Azure portal. Wybierz autor > połączeń > środowiska Integration Runtime > Edytuj > węzły > zmodyfikuj wartość równoczesnych zadań na węzeł. Możesz również użyć programu PowerShell [Azdatafactoryv2integrationruntimenode aktualizacji](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) polecenia.
  
### <a name="status-per-node"></a>Stan (na węzeł)
W poniższej tabeli przedstawiono możliwe stany węzeł Self-Hosted integration runtime:

| Stan | Opis |
| ------ | ------------------ | 
| Online | Węzeł jest połączony z usługą Data Factory. |
| Offline | Węzeł jest w trybie offline. |
| Uaktualnianie | Węzeł jest aktualizowane automatycznie. |
| Ograniczone | Ze względu na problem z łącznością. Może być spowodowane problemem 8050 portu HTTP, problem łączności z usługą Service bus lub problemu z synchronizacją poświadczeń. |
| Nieaktywna | Węzeł znajduje się w konfiguracji różni się od konfiguracji innych większości węzłów. |

Węzeł może być nieaktywne, gdy nie można nawiązać z innych węzłów.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stan (ogólną własnego środowiska integration runtime)
W poniższej tabeli przedstawiono możliwe stany własnego środowiska integration runtime. Ten stan jest zależna od stany wszystkie węzły, które należą do środowiska uruchomieniowego. 

| Stan | Opis |
| ------ | ----------- | 
| Potrzebujesz rejestracji | Żaden węzeł nie jest jeszcze zarejestrowany ten własnego środowiska integration runtime. |
| Online | Wszystkie węzły są w trybie online. |
| Offline | Żaden węzeł nie jest w trybie online. |
| Ograniczone | Nie wszystkie węzły w tym własne środowisko integration runtime jest w dobrej kondycji. Ten stan jest ostrzeżenie, że niektóre węzły mogą być wyłączone. Ten stan może wynikać z problemu z synchronizacją poświadczeń na węzeł dyspozytora/procesu roboczego. |

Użyj **Get AzDataFactoryV2IntegrationRuntimeMetric** polecenia cmdlet, aby pobrać ładunek JSON zawierającego szczegółowe Self-Hosted integration runtime właściwości i ich migawki wartości w czasie wykonywania polecenia cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Przykładowe dane wyjściowe (przy założeniu, że istnieją dwa węzły skojarzone z tym własnego środowiska integration runtime):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Azure SSIS Integration Runtime
Środowisko Azure-SSIS integration runtime jest w pełni zarządzany klaster Azure maszyny wirtualne (lub węzły) uruchamianie pakietów SSIS w wersji dedykowanej. Nie powoduje uruchomienia wszelkie inne działania usługi Azure Data Factory. Po zainicjowaniu obsługi administracyjnej, można zbadać jego właściwości, a także monitorować stany jej całkowitego/odnoszące się do węzła.

### <a name="properties"></a>Właściwości

| Właściwość/Status | Opis |
| --------------- | ----------- |
| CreateTime | Czas UTC, podczas tworzenia środowiska Azure-SSIS integration runtime. |
| Węzły | Przydzielone dostępne węzły Twojego środowiska Azure-SSIS integration Runtime z możliwością wykonywania akcji błędów i stanów dla węzła (uruchamianie/dostępne/odtwarzanie/niedostępna). |
| OtherErrors | Błędy informacje z możliwością działania bez określonego węzła na środowiska Azure-SSIS integration runtime. |
| LastOperation | Wynik ostatniej operacji uruchomienia/zatrzymania na środowiska Azure-SSIS integration runtime z możliwością wykonywania akcji błędów, jeśli go nie powiodła się. |
| Stan | Ogólny stan (początkowy/uruchamianie/uruchomienia/zatrzymywania/zatrzymane) środowiska Azure-SSIS integration runtime. |
| Lokalizacja | Lokalizacja środowiska Azure-SSIS integration runtime. |
| NodeSize | Rozmiar każdego węzła środowiska Azure-SSIS integration runtime. |
| NodeCount | Liczba węzłów w środowiska Azure-SSIS integration runtime. |
| MaxParallelExecutionsPerNode | Liczba równoległych wykonań na węzeł w środowiska Azure-SSIS integration runtime. |
| CatalogServerEndpoint | Punkt końcowy istniejącego serwera Azure SQL Database/wystąpienia zarządzanego do hostowania bazy SSISDB. |
| CatalogAdminUserName | Nazwa użytkownika administratora istniejącego serwera Azure SQL Database/wystąpienia zarządzanego. Usługa Data Factory używa tych informacji, aby przygotować i zarządzać nią SSISDB w Twoim imieniu. |
| CatalogAdminPassword | Hasło administratora dla istniejącego serwera Azure SQL Database/wystąpienia zarządzanego. |
| CatalogPricingTier | Warstwa cenowa bazą danych SSISDB hostowaną przez istniejącego serwera usługi Azure SQL Database.  Nie ma zastosowania do bazy danych wystąpienia zarządzanego Azure SQL hostowania bazy SSISDB. |
| VNetId | Identyfikator sieci wirtualnej zasobów dla Twojego środowiska Azure-SSIS integration runtime do dołączenia do. |
| Podsieć | Nazwa podsieci dla Twojego środowiska Azure-SSIS integration runtime do dołączenia do. |
| ID | Identyfikator zasobu środowiska Azure-SSIS integration runtime. |
| Type | Typ (Managed/samoobsługowego-Hosted) środowiska Azure-SSIS integration runtime. |
| ResourceGroupName | Nazwa grupy zasobów platformy Azure, w którym zostały utworzone fabryki danych i środowiska Azure-SSIS integration runtime. |
| DataFactoryName | Nazwa fabryki danych platformy Azure. |
| Name (Nazwa) | Nazwa środowiska Azure-SSIS integration runtime. |
| Opis | Opis Twojego środowiska Azure-SSIS integration runtime. |

  
### <a name="status-per-node"></a>Stan (na węzeł)

| Stan | Opis |
| ------ | ----------- | 
| Uruchamianie | Ten węzeł jest przygotowywane. |
| Dostępne | Ten węzeł jest gotowe do wdrożenia/wykonywanie pakietów usług SSIS. |
| Odtwarzanie | Ten węzeł jest są naprawiane/ponowne uruchomienie. |
| Niedostępny | Ten węzeł nie jest gotowe do wdrożenia/wykonywanie pakietów usług SSIS i zawiera informacje z możliwością działania błędy/problemy, które można rozpoznać. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Stan (ogólnego środowiska Azure-SSIS IR)

| Stan ogólny | Opis | 
| -------------- | ----------- | 
| Początkowa | Węzły środowiska Azure-SSIS integration runtime nie zostały przydzielone przygotowane. | 
| Uruchamianie | Węzły środowiska Azure-SSIS integration runtime są przydzielane przygotowany i rozliczeń została uruchomiona. |
| Rozpoczęto | Węzły środowiska Azure-SSIS integration runtime zostały przydzielone przygotowane i są gotowe do wdrożenia/wykonywanie pakietów usług SSIS. |
| Zatrzymywanie  | Węzły środowiska Azure-SSIS integration runtime są udostępniane. |
| Zatrzymano | Węzły środowiska Azure-SSIS integration runtime zostały zwolnione i rozliczeń została zatrzymana. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitorowanie środowiska Azure-SSIS integration runtime w witrynie Azure portal

Poniższych zrzutach ekranu przedstawiono sposób wybierania Azure-SSIS IR do monitorowania i podaj przykład wyświetlane informacje.

![Wybierz środowisko Azure-SSIS integration runtime do monitorowania](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Wyświetlanie informacji dotyczących środowiska Azure-SSIS integration runtime](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorowanie środowiska Azure-SSIS integration runtime przy użyciu programu PowerShell

Sprawdź stan Azure-SSIS IR. za pomocą skryptu, jak w poniższym przykładzie

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Więcej informacji na temat środowiska Azure-SSIS integration runtime

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat środowiska Azure-SSIS integration runtime:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje o pojęciach dotyczących środowiska integration Runtime, ogólnie rzecz biorąc w tym Azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL Database do hostowania wykazu usług SSIS. 
- [Instrukcje: Tworzenie środowiska Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md). Ten artykuł stanowi rozszerzenie samouczka i zawiera instrukcje na temat używania wystąpienia zarządzanego Azure SQL Database i dołączania środowiska IR do sieci wirtualnej. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Zawiera on również instrukcje skalowania środowiska Azure-SSIS IR do wewnątrz za pomocą dodawania do niego węzłów. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska IR Azure-SSIS do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej, tak, aby dołączyć Azure-SSIS IR do sieci wirtualnej przy użyciu witryny Azure portal. 

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły dotyczące monitorowania potoków w różny sposób: 

- [Szybki Start: tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md).
- [Monitorowanie potoków usługi Data Factory przy użyciu usługi Azure Monitor](monitor-using-azure-monitor.md)
