---
title: Ponownie skonfigurować środowisko Azure-SSIS integration runtime | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić konfigurację środowiska Azure-SSIS integration runtime w usłudze Azure Data Factory już zostały przeprowadzone.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d0022ee46049181ed15e6b3968b9b952483c7fba
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016036"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Ponownie skonfigurować środowisko Azure-SSIS integration runtime
W tym artykule opisano, jak można ponownie skonfigurować istniejące środowisko Azure-SSIS integration runtime. Aby utworzyć środowiska Azure-SSIS integration runtime (IR) w usłudze Azure Data Factory, zobacz [tworzenia środowiska Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory 
Interfejs użytkownika usługi Data Factory można użyć, aby zatrzymać, Edycja/ponownie lub usuń Azure-SSIS IR. 

1. W **interfejs użytkownika usługi Data Factory**, przełącz się do **Edytuj** kartę. Aby uruchomić interfejs użytkownika usługi Data Factory, kliknij **tworzenie i monitorowanie** na stronie głównej fabryki danych.
2. W okienku po lewej stronie kliknij **połączeń**.
3. W okienku po prawej stronie, przełącz się do **środowiska Integration Runtime**. 
4. Można użyć przycisków w kolumnie akcji, aby **zatrzymać**, **Edytuj**, lub **Usuń** środowiska integration runtime. **Kodu** znajdujący się w **akcje** kolumny pozwala na wyświetlanie definicji JSON skojarzony z produktem integration runtime.  
    
    ![Akcje w przypadku środowiska Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Aby ponownie skonfigurować środowisko Azure-SSIS IR
1. Zatrzymaj środowisko integration runtime, klikając **zatrzymać** w **akcje** kolumny. Aby odświeżyć widok listy, kliknij **Odśwież** na pasku narzędzi. Po zatrzymaniu środowiska IR zobaczysz, że pierwszą akcją, można uruchamiać wewnątrz 

    ![Akcje w przypadku środowiska Azure SSIS IR - po zatrzymaniu](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Edytuj/ponowna konfiguracja środowiska IR, klikając **Edytuj** znajdujący się w **akcje** kolumny. W **Instalatora środowiska Integration Runtime** okna, Zmień ustawienia (na przykład rozmiar węzła, liczba węzłów lub maksymalna równoległych wykonań na węzeł). 
3. Aby ponownie uruchomić środowisko IR, kliknij przycisk **Start** znajdujący się w **akcje** kolumny.     

## <a name="azure-powershell"></a>Azure PowerShell
Po aprowizacji i uruchomić wystąpienie środowiska Azure-SSIS integration runtime, można ponownie skonfigurować ją przez uruchomienie sekwencji `Stop`  -  `Set`  -  `Start` poleceń cmdlet programu PowerShell po kolei. Na przykład poniższy skrypt programu PowerShell zmienia liczbę przydzielonych dla wystąpienia środowiska Azure-SSIS integration środowiska uruchomieniowego do pięciu węzłów.

### <a name="reconfigure-an-azure-ssis-ir"></a>Ponownie skonfigurować środowisko Azure-SSIS IR

1. Najpierw należy zatrzymać środowisko Azure-SSIS integration runtime za pomocą [Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) polecenia cmdlet. To polecenie zwalnia wszystkie jego węzły i zatrzymuje rozliczeń.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Następnie ponownie skonfiguruj środowisko IR Azure-SSIS przy użyciu [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) polecenia cmdlet. Następujące przykładowe polecenie skalowania środowiska Azure-SSIS integration runtime do pięciu węzłów.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Następnie uruchom środowisko Azure-SSIS integration runtime przy użyciu [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) polecenia cmdlet. To polecenie przydziela wszystkie jego węzły uruchamianie pakietów SSIS.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Usuń środowisko Azure-SSIS IR
1. Najpierw Wyświetl listę wszystkich istniejących środowisk IR SSIS platformy Azure w ramach fabryką danych.

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Następnie należy zatrzymać wszystkie istniejące IRs Azure SSIS w fabryce danych.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Następnie usuń wszystkie istniejące Azure SSIS urząd skarbowy w fabryce danych jedno po drugim.

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
Aby uzyskać więcej informacji na temat środowiska Azure-SSIS runtime zobacz następujące tematy: 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje o pojęciach dotyczących środowiska integration Runtime, ogólnie rzecz biorąc w tym Azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL do hostowania wykazu usług SSIS. 
- [Jak: Tworzenie środowiska Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md). Ten artykuł stanowi rozszerzenie samouczka i zawiera instrukcje na temat używania wystąpienia zarządzanego Azure SQL Database i dołączania środowiska IR do sieci wirtualnej. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska IR Azure-SSIS do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska IR Azure-SSIS do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
 
