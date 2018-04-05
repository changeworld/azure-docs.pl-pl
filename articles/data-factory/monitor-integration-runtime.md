---
title: Monitorowanie środowiska uruchomieniowego integracji w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować różne typy środowiska uruchomieniowego integracji w fabryce danych Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: ''
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: douglasl
ms.openlocfilehash: 4c857af02e104940559cb86daa3ccd208d8e35a1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitor środowiska uruchomieniowego integracji w fabryce danych Azure  
**Integracja środowiska uruchomieniowego** jest używany przez fabryki danych Azure zapewnienie różne funkcje integracji danych w różnych środowiskach sieci infrastruktury obliczeniowej. Istnieją trzy typy środowisk uruchomieniowych integracji oferowane przez fabrykę danych:

- Środowisko uruchomieniowe integracji Azure
- Infrastruktura Integration Runtime (Self-hosted)
- Azure SSIS Integration Runtime

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz dostępnej ogólnie wersji 1 usługi Data Factory, zobacz [Data Factory version 1 documentation (Dokumentacja usługi Data Factory w wersji 1)](v1/data-factory-introduction.md).

Aby uzyskać stan wystąpienia środowiska uruchomieniowego integracji (IR), uruchom następujące polecenie programu PowerShell: 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Polecenie cmdlet zwraca informacje różne dla różnych typów środowiska uruchomieniowego integracji. W tym artykule opisano właściwości i stanów dla każdego typu środowiska uruchomieniowego integracji.  

## <a name="azure-integration-runtime"></a>Środowisko uruchomieniowe integracji Azure
Zasób obliczeniowy dla środowiska uruchomieniowego integracji Azure pełni odbywa się elastycznie na platformie Azure. Poniższa tabela zawiera opisy właściwości zwrócony przez **Get-AzureRmDataFactoryV2IntegrationRuntime** polecenia:

### <a name="properties"></a>Właściwości
Poniższa tabela zawiera opisy właściwości zwracany przez polecenie cmdlet do obsługi integracji Azure:

| Właściwość | Opis |
-------- | ------------- | 
| Name (Nazwa) | Nazwa środowiska uruchomieniowego integracji platformy Azure. |  
| Stan | Stan środowiska uruchomieniowego integracji platformy Azure. | 
| Lokalizacja | Lokalizacja środowiska uruchomieniowego integracji platformy Azure. Aby uzyskać więcej informacji o lokalizacji środowiska uruchomieniowego integracji platformy Azure, zobacz [wprowadzenie do integracji środowiska uruchomieniowego](concepts-integration-runtime.md). |
| DataFactoryName | Nazwa środowiska uruchomieniowego integracji Azure należącej do fabryki danych. | 
| ResourceGroupName | Nazwa grupy zasobów, do której należy fabryki danych.  |
| Opis | Opis elementu środowiska uruchomieniowego integracji.  |

### <a name="status"></a>Stan
W poniższej tabeli przedstawiono możliwe stany ze środowiska uruchomieniowego integracji Azure:

| Stan | Komentarz/scenariuszy | 
| ------ | ------------------ |
| Online | Środowiska uruchomieniowego integracji Azure jest dostępna i gotowa do użycia. | 
| Offline | Środowisko uruchomieniowe integracja Azure jest w trybie offline z powodu błędu wewnętrznego. |

## <a name="self-hosted-integration-runtime"></a>Infrastruktura Integration Runtime (Self-hosted)
Ta sekcja zawiera opisy zwracany przez polecenie cmdlet Get-AzureRmDataFactoryV2IntegrationRuntime właściwości. 

> [!NOTE] 
> Zwróconych właściwości i stan zawierają informacje o ogólną siebie integrację środowiska uruchomieniowego i każdego węzła w czasie wykonywania.  

### <a name="properties"></a>Właściwości

Poniższa tabela zawiera opisy monitorowania właściwości **każdego węzła**:

| Właściwość | Opis | 
| -------- | ----------- | 
| Name (Nazwa) | Nazwa środowiska uruchomieniowego integracji siebie i skojarzonych z nim węzłów. Węzeł jest na lokalnej maszynie systemu Windows ma środowiska uruchomieniowego integracji siebie na nim zainstalowany. |  
| Stan | Stan środowiska uruchomieniowego ogólną integrację siebie i każdego węzła. Przykład: Online/Offline/Limited/itp. Aby uzyskać informacje o tych stanów zobacz następną sekcję. | 
| Wersja | Wersja środowiska uruchomieniowego integracji siebie i każdego węzła. Wersja środowiska uruchomieniowego integracji hostowania samoobsługowego jest określana na podstawie wersji Większość węzłów w grupie. W przypadku węzłów z różnych wersji w konfiguracji środowiska uruchomieniowego integracji siebie tylko węzły z tym samym numerem wersji jako logiczny hosta samodzielnego funkcji środowiska uruchomieniowego integracji poprawnie. Inne osoby włączony jest tryb ograniczony i należy ręcznie zaktualizować (tylko w przypadku automatycznej aktualizacji nie powiedzie się). | 
| Dostępna pamięć | Dostępna pamięć na węźle środowiska uruchomieniowego integracji siebie. Ta wartość jest blisko migawka w czasie rzeczywistym. | 
| Użycie procesora CPU | Użycie procesora CPU przez siebie integracji środowiska uruchomieniowego węzła. Ta wartość jest blisko migawka w czasie rzeczywistym. |
| Sieć (We/Wy) | Użycie sieci węzła środowiska uruchomieniowego integracji siebie. Ta wartość jest blisko migawka w czasie rzeczywistym. | 
| Równoczesnych zadań (systemem / Limit) | Liczba zadań lub zadania uruchomione w każdym węźle. Ta wartość jest blisko migawka w czasie rzeczywistym. Limit oznacza maksymalny współbieżnych zadań dla każdego węzła. Ta wartość jest określona na podstawie rozmiaru maszyny. Można zwiększyć limit skalowanie w górę wykonywania zadań jednoczesnych w zaawansowanych scenariuszach, w których procesora CPU i pamięci/sieci jest używane w obszarze, ale limit czasu są działania. Ta funkcja jest również dostępna o środowisku uruchomieniowym integracji siebie jednym węzłem. |
| Rola | Istnieją dwa typy ról wielowęzłowego integracji hostowanie Samoobsługowe środowisko uruchomieniowe — dyspozytora i proces roboczy. Wszystkie węzły są pracowników, co oznacza, że ich wszystkich służy do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora służąca do ściągnięcia zadania/zadań z usługi w chmurze i wysyłania ich do węzłów innego procesu roboczego. Węzeł dyspozytora jest również węzłem procesu roboczego. |

Niektóre ustawienia właściwości będą bardziej zrozumiałe, gdy istnieją co najmniej dwa węzły (skalowanie w poziomie scenariusz) w środowisku wykonawczym siebie integracji. 
  
### <a name="status-per-node"></a>Stan (na każdym węźle)
W poniższej tabeli przedstawiono możliwe stany węzła środowiska uruchomieniowego integracji hostowania samoobsługowego:

| Stan | Opis |
| ------ | ------------------ | 
| Online | Węzeł jest podłączony do usługi fabryki danych. |
| Offline | Węzeł jest w trybie offline. |
| Uaktualnianie | Węzeł jest aktualizowane automatycznie. |
| Ograniczone | Spowodowane problemem z łącznością. Może być z powodu problemu z synchronizacją poświadczeń, problem dotyczący łączności magistrali usługi lub problem 8050 portu HTTP. |
| Nieaktywna | Węzeł znajduje się w różnych konfiguracji innych węzłów większość konfiguracji. |

Węzeł może być nieaktywne, gdy nie można połączyć do innych węzłów.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stan (ogólną integrację siebie środowiska wykonawczego)
W poniższej tabeli przedstawiono możliwe stany runtime siebie integracji. Ten stan jest zależna od statusy wszystkich węzłów, które należą do środowiska wykonawczego. 

| Stan | Opis |
| ------ | ----------- | 
| Wymagają rejestracji | Żaden węzeł nie jest jeszcze zarejestrowana do tego środowiska uruchomieniowego integracji siebie. |
| Online | Wszystkie węzły są online. |
| Offline | Żaden węzeł nie jest w trybie online. |
| Ograniczone | Nie wszystkie węzły w środowisku uruchomieniowym tej integracji siebie są w dobrej kondycji. Ten stan jest ostrzeżenie, że niektóre węzły mogą nie działać. Ten stan może wynikać z problemu z synchronizacją poświadczeń w węźle dyspozytora/proces roboczy. |

Użyj **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** polecenia cmdlet, aby pobrać ładunek JSON zawierający szczegółowe hosta samodzielnego właściwości środowiska uruchomieniowego integracji i ich migawki wartości w czasie wykonywania polecenia cmdlet.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Przykładowe dane wyjściowe (przy założeniu, że istnieją dwa węzły skojarzone z tego środowiska uruchomieniowego integracji siebie):

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
Środowiska uruchomieniowego integracji usług SSIS Azure jest pełni zarządzanego klastra Azure maszyny wirtualnej (lub węzły) dedykowane do uruchamiania pakietów SSIS. Działa on żadnych innych działań fabryki danych Azure. Po zainicjowaniu obsługi, można zbadać jego właściwości i monitorować jego stanów specyficzne dla ogólnych/węzła.

### <a name="properties"></a>Właściwości

| Właściwość/Status | Opis |
| --------------- | ----------- |
| CreateTime | Czas UTC utworzenia Twojego środowiska uruchomieniowego integracji usług SSIS Azure. |
| Węzły | Przydzielone dostępne węzły Twojego środowiska uruchomieniowego integracji usług SSIS Azure z właściwym dla węzła stanów (uruchamianie/dostępne/recykling/niedostępna) i błędy można wykonać. |
| OtherErrors | Błędy można wykonać z systemem innym niż danego węzła na Twojego środowiska uruchomieniowego integracji usług SSIS Azure. |
| LastOperation | Wynik ostatniej operacji uruchamiania i zatrzymywania na Twojego środowiska uruchomieniowego integracji Azure SSIS z możliwością wykonania akcji błędy, jeśli go nie powiodła się. |
| Stan | Ogólny stan (początkowego/uruchamianie/uruchomiona/zatrzymywania/zatrzymany) programu Azure SSIS integracji runtime. |
| Lokalizacja | Lokalizacja Twojego środowiska uruchomieniowego integracji usług SSIS Azure. |
| NodeSize | Rozmiar każdego węzła Twojego środowiska uruchomieniowego integracji usług SSIS Azure. |
| NodeCount | Liczba węzłów w środowisku uruchomieniowym programu Azure SSIS integracji. |
| MaxParallelExecutionsPerNode | Liczba równoległych wykonaniami każdy węzeł w środowisku uruchomieniowym programu Azure SSIS integracji. |
| CatalogServerEndpoint | Punkt końcowy istniejącego serwera wystąpienia zarządzanego/bazy danych SQL Azure (wersja zapoznawcza) na host usług SSIS. |
| CatalogAdminUserName | Nazwa użytkownika z istniejącego serwera Azure SQL bazy danych/zarządzane wystąpienia (wersja zapoznawcza). Usługi fabryka danych używa tych informacji do przygotowania i zarządzanie nimi SSISDB w Twoim imieniu. |
| CatalogAdminPassword | Hasło administratora serwera istniejącego wystąpienia zarządzanego/bazy danych SQL Azure (wersja zapoznawcza). |
| CatalogPricingTier | Warstwa cenowa SSISDB należące do istniejącego serwera bazy danych SQL Azure.  Nie dotyczy Azure zarządzane wystąpienia SQL (wersja zapoznawcza) hosting usług SSIS. |
| VNetId | Sieć wirtualną (VNet) identyfikator zasobu dla Twojego środowiska uruchomieniowego integracji usług SSIS Azure do przyłączenia. |
| Podsieć | Nazwa podsieci dla Twojego środowiska uruchomieniowego integracji Azure SSIS do przyłączenia. |
| ID | Identyfikator zasobu Twojego środowiska uruchomieniowego integracji usług SSIS Azure. |
| Typ | Typ (zarządzanego/niezależne-Hosted) programu Azure SSIS integracji runtime. |
| ResourceGroupName | Nazwa grupy zasobów Azure, w którym zostały utworzone z fabryki danych i środowiska uruchomieniowego integracji usług SSIS Azure. |
| DataFactoryName | Nazwa użytkownika fabryki danych Azure. |
| Name (Nazwa) | Nazwa Twojego środowiska uruchomieniowego integracji usług SSIS Azure. |
| Opis | Opis programu runtime integracji usług SSIS Azure. |

  
### <a name="status-per-node"></a>Stan (na każdym węźle)

| Stan | Opis |
| ------ | ----------- | 
| Uruchamianie | Ten węzeł jest w trakcie przygotowywania. |
| Dostępna | Ten węzeł jest gotowe do wdrożenia/wykonywania pakietów SSIS. |
| Odtwarzanie | Ten węzeł jest on naprawiony/ponowne uruchomienie. |
| Niedostępny | Ten węzeł nie jest gotowe do wdrożenia/wykonywania pakietów SSIS i można wykonać błędy/problemy, które można rozwiązać. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Stan (ogólną Azure SSIS integracji runtime)

| Ogólne informacje o stanie | Opis | 
| -------------- | ----------- | 
| Początkowa | Węzły Twojego środowiska uruchomieniowego integracji Azure SSIS nie zostały przydzielone przygotowane. | 
| Uruchamianie | Węzły sieci środowiska uruchomieniowego integracji usług SSIS Azure są przydzielone przygotowane i rozliczeń została uruchomiona. |
| Uruchomiono | Węzły sieci środowiska uruchomieniowego integracji usług SSIS Azure zostały przydzielone przygotowane i są gotowe do wdrożenia/wykonywania pakiety usług SSIS. |
| Zatrzymywanie  | Zwalnianie węzły Twojego środowiska uruchomieniowego integracji usług SSIS Azure. |
| Zatrzymano | Węzły Twojego środowiska uruchomieniowego integracji usług SSIS Azure zostały zwolnione i rozliczeń została zatrzymana. |

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat środowiska uruchomieniowego integracji usług SSIS Azure:

- [Środowisko uruchomieniowe integracji usług SSIS Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje o pojęciach dotyczących środowisk uruchomieniowych integracji ogólnie tym podczerwieni Azure SSIS. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL do hostowania wykazu usług SSIS. 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). W tym artykule rozszerzenie samouczka i zawiera instrukcje dotyczące przy użyciu wystąpienia zarządzane Azure SQL (wersja zapoznawcza) i dołączenie IR do sieci wirtualnej. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Zawiera on również instrukcje skalowania środowiska Azure-SSIS IR do wewnątrz za pomocą dodawania do niego węzłów. 
- [Join an Azure-SSIS IR to a VNet (Dołączanie środowiska Azure-SSIS IR do sieci wirtualnej)](join-azure-ssis-integration-runtime-virtual-network.md). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska Azure-SSIS IR do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska Azure-SSIS IR do sieci wirtualnej. 

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły monitorowania potoki na różne sposoby: 

- [Szybki Start: tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md).
- [Użyj monitora Azure w celu monitorowania potoki fabryki danych](monitor-using-azure-monitor.md)