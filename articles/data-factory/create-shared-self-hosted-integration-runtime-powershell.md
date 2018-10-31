---
title: Tworzenie udostępnionego własnego środowiska integration runtime w usłudze Azure Data Factory przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć udostępnione własnego środowiska integration runtime w usłudze Azure Data Factory, która umożliwia dostęp do fabryki danych wielu środowiska integration runtime.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: d7f3fbcb3235c8c620876e68a62f3e491770779d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252138"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory-with-powershell"></a>Tworzenie udostępnionego własnego środowiska integration runtime w usłudze Azure Data Factory przy użyciu programu PowerShell

Ten przewodnik krok po kroku dowiesz się, jak utworzyć udostępnione własnego środowiska integration runtime (IR) w usłudze Azure Data Factory przy użyciu programu Azure PowerShell. Następnie przy użyciu udostępnionego własnego środowiska integration runtime w innej usłudze data factory. W tym samouczku wykonasz następujące czynności: 

1. Tworzenie fabryki danych. 
1. Utwórz własne środowisko Integration Runtime.
1. Własne środowisko integration runtime można udostępniać innym fabryki danych.
1. Tworzenie połączonych integration runtime.
1. Odwołaj udostępniania.

## <a name="prerequisites"></a>Wymagania wstępne 

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/). 

- Zainstalowanie programu **Azure PowerShell**. Postępuj zgodnie z instrukcjami w [Instalowanie programu Azure PowerShell na Windows](/powershell/azure/install-azurerm-ps). Uruchom skrypt, aby utworzyć Self-Hosted integration runtime, które mogą być współużytkowane z innymi fabryki danych za pomocą programu PowerShell. 

> [!NOTE]
> Aby uzyskać listę regionów świadczenia usługi Azure, w których Data Factory jest dostępna, wybierz regiony, które Cię interesują na następującej stronie: [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom program Windows PowerShell ISE.

1. Utwórz zmienne.

    Skopiuj i wklej poniższy skrypt i Zastąp zmienne (SubscriptionName, ResourceGroupName itp.) przy użyciu rzeczywistych wartości. 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Zaloguj się i wybierz subskrypcję.

    Dodaj następujący kod do skryptu, które mogą się zalogować, a następnie wybierz swoją subskrypcję platformy Azure:

    ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

1. Utwórz grupę zasobów i fabryki danych.

    *(Ten krok jest opcjonalny. Jeśli masz już fabryki danych, Pomiń ten krok).* 

    Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w lokalizacji WestEurope. 

    ```powershell
    New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Uruchom następujące polecenie, aby utworzyć fabrykę danych: 

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

## <a name="create-a-self-hosted-integration-runtime"></a>Tworzenie własnego środowiska Integration Runtime

*(Ten krok jest opcjonalny. Jeśli masz już własne środowisko integration runtime, którą chcesz udostępnić innym fabryki danych, Pomiń ten krok).*

Uruchom następujące polecenie, aby utworzyć własne środowisko integration runtime:

```powershell
$SharedIR = Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Pobierz klucz uwierzytelniania integration runtime i zarejestruj węzeł

Uruchom następujące polecenie, aby pobrać klucz uwierzytelniania dla własnego środowiska integration runtime:

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Odpowiedź zawiera klucz uwierzytelniania dla tego własnego środowiska integration runtime. Użyj tego klucza, gdy Zarejestruj węzeł integration runtime.

### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instalowanie i rejestrowanie własnego środowiska integration runtime

1. Pobierz instalatora Self-Hosted integration runtime z [Integration Runtime usługi Azure Data Factory](https://aka.ms/dmg).

2. Uruchom Instalatora Aby zainstalować Self-Hosted integration na komputerze lokalnym.

3. Zarejestruj nową opcję integracji samodzielnie hostowany przy użyciu klucza uwierzytelniania, do którego został pobrany w poprzednim kroku.

## <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Udostępniać własne środowisko integration runtime za pomocą innej usługi data factory

### <a name="create-another-data-factory"></a>Tworzenie innej fabryki danych

*(Ten krok jest opcjonalny. Jeśli masz już fabryki danych, z którym chcesz się podzielić, Pomiń ten krok).*

```powershell
$factory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
### <a name="grant-permission"></a>Udziel uprawnień

Udziel uprawnienia do usługi Data Factory, który ma dostęp do własnego środowiska integration runtime został utworzony i zarejestrowany.

> [!IMPORTANT]
> Pomiń ten krok nie jest!

```powershell
New-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

## <a name="create-a-linked-self-hosted-integration-runtime"></a>Tworzenie połączonych własnego środowiska integration runtime

Uruchom następujące polecenie, aby utworzyć połączony własnego środowiska integration runtime:

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Teraz można użyć tego połączone środowisko IR w dowolnym połączonej usługi. Połączone środowisko IR używa udostępnionego integration runtime do uruchamiania działań.

## <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Odwołaj środowiska integration runtime, udostępnianie danych z usługi data factory

Aby odwołać dostęp usługi data factory dostęp do udostępnionego integration runtime, można uruchomić następujące polecenie:

```powershell
Remove-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Aby usunąć istniejące połączone środowisko integration runtime, można uruchom następujące polecenie przed udostępnionego integration runtime:

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

## <a name="next-steps"></a>Kolejne kroki

- Przegląd pojęć środowiska uruchomieniowego integracji w [Integration runtime w usłudze Azure Data Factory](concepts-integration-runtime.md).

- Dowiedz się, jak utworzyć własne środowisko integration runtime w witrynie Azure portal w [tworzenie i konfigurowanie własnego środowiska integration runtime](create-self-hosted-integration-runtime.md).
