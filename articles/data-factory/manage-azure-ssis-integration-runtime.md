---
title: Skonfiguruj ponownie środowiska uruchomieniowego integracji usług SSIS Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić konfigurację środowiska uruchomieniowego integracji usług SSIS Azure w fabryce danych Azure po już uprzednim udostępnieniu.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 248967f736fcd10cf398917d3cd1e2760537df7c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051988"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Skonfiguruj ponownie środowiska uruchomieniowego integracji usług SSIS Azure
W tym artykule opisano, jak zmienić konfigurację istniejącego środowiska uruchomieniowego integracji usług SSIS Azure. Aby utworzyć Azure SSIS integrację środowiska uruchomieniowego (IR) w fabryce danych Azure, zobacz [utworzyć środowiska uruchomieniowego integracji usług SSIS Azure](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory 
Interfejs fabryki danych służy do zatrzymania, edycji/ponownej konfiguracji i usuwania IR. Azure SSIS 

1. W **interfejsu użytkownika z fabryki danych**, przełącz się do **Edytuj** kartę. Aby uruchomić interfejs użytkownika z fabryki danych, kliknij przycisk **autora & Monitor** na stronie głównej w fabryce danych.
2. W okienku po lewej stronie kliknij **połączenia**.
3. W okienku po prawej stronie, przełącz się do **środowisk uruchomieniowych integracji**. 
4. Można użyć przycisków w kolumnie Akcje **zatrzymać**, **Edytuj**, lub **usunąć** środowiska uruchomieniowego integracji. **Kod** przycisk **akcje** kolumny umożliwia wyświetlanie definicji JSON skojarzony ze środowiskiem uruchomieniowym integracji.  
    
    ![Akcje dotyczące IR Azure SSIS](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Aby zmienić konfigurację IR Azure SSIS
1. Zatrzymać środowiska uruchomieniowego integracji, klikając **zatrzymać** w **akcje** kolumny. Aby odświeżyć widok listy, kliknij przycisk **Odśwież** na pasku narzędzi. Po wyłączeniu IR, zobaczysz, że pierwszą akcją umożliwia uruchamianie IR. 

    ![Akcje dotyczące Azure SSIS IR — po wyłączeniu](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Edytuj/reconfigure IR, klikając **Edytuj** przycisk **akcje** kolumny. W **ustawień środowiska uruchomieniowego integracji** okna, Zmień ustawienia (na przykład rozmiaru węzła, liczba węzłów lub maksymalna wykonaniami równoległego na węzeł). 
3. Aby ponownie uruchomić IR, kliknij przycisk **Start** przycisk **akcje** kolumny.     

## <a name="azure-powershell"></a>Azure PowerShell
Po obsługi administracyjnej i uruchomić wystąpienia środowiska uruchomieniowego integracji usług SSIS Azure, można ponownie skonfigurować go przez uruchomienie sekwencji `Stop`  -  `Set`  -  `Start` poleceń cmdlet programu PowerShell po kolei. Na przykład poniższy skrypt programu PowerShell zmienia numer przydzielone dla wystąpienia środowiska uruchomieniowego integracji usług SSIS Azure do pięciu węzłów.

### <a name="reconfigure-an-azure-ssis-ir"></a>Skonfiguruj ponownie IR Azure SSIS

1. Najpierw należy zatrzymać środowiska uruchomieniowego integracji usług SSIS Azure za pomocą [Stop AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) polecenia cmdlet. To polecenie zwalnia wszystkich jego węzłów i zatrzymuje rozliczeń.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Następnie należy ponownie skonfigurować IR Azure SSIS przy użyciu [AzureRmDataFactoryV2IntegrationRuntime zestaw](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) polecenia cmdlet. Następującego przykładowego polecenia skaluje się środowiska uruchomieniowego integracji usług SSIS Azure do pięciu węzłów.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Następnie uruchom środowiska uruchomieniowego integracji usług SSIS Azure za pomocą [Start AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) polecenia cmdlet. To polecenie przydziela wszystkich jego węzłów do uruchamiania pakietów SSIS.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Usuń IR Azure SSIS
1. Po pierwsze Wyświetl listę wszystkich istniejących amerykańskim SSIS Azure w fabryce danych.

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Następnie należy zatrzymać wszystkie istniejące amerykańskim SSIS Azure w fabryce danych.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Następnie usuń wszystkie istniejące amerykańskim SSIS Azure w fabryce danych jeden po drugim.

    ```powershell
    Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Na koniec usunąć fabrykę danych.

    ```powershell
    Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Jeśli została utworzona nowa grupa zasobów, Usuń grupę zasobów.

    ```powershell
    Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje o środowisku uruchomieniowym usług SSIS Azure zobacz następujące tematy: 

- [Środowisko uruchomieniowe integracji usług SSIS Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje o pojęciach dotyczących środowisk uruchomieniowych integracji ogólnie tym podczerwieni Azure SSIS. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL do hostowania wykazu usług SSIS. 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). Ten artykuł stanowi rozszerzenie samouczka i zawiera instrukcje dotyczące używania wystąpienia zarządzanego Azure SQL (wersja zapoznawcza) oraz dołączania środowiska IR do sieci wirtualnej. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska IR Azure-SSIS do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska IR Azure-SSIS do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
 
