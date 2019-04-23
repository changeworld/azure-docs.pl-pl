---
title: Jak zarządzać przypisaniami przy użyciu programu PowerShell
description: Dowiedz się, jak zarządzać przypisaniami planu za pomocą oficjalnego schematy moduł Azure PowerShell, Az.Blueprint.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d8eacffe4b792eda5d81051f6aa65caa3292c896
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785694"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Jak zarządzać przypisaniami przy użyciu programu PowerShell

Przypisanie planu mogą być zarządzane przy użyciu **Az.Blueprint** modułu Azure PowerShell. Ten moduł umożliwia pobieranie, tworzenie, aktualizowanie i usuwanie przypisań. Moduł można również pobrać szczegółów dla istniejącej definicji planu. W tym artykule opisano sposób instalowania modułu i rozpocząć korzystanie z niej.

## <a name="add-the-azblueprint-module"></a>Dodaj moduł Az.Blueprint

Aby włączyć programu Azure PowerShell do zarządzania przypisaniami planu, można dodać modułu. Ten moduł może być używany z lokalnie zainstalowanym programem PowerShell, [programem Azure Cloud Shell](https://shell.azure.com) lub [obrazem Docker programu Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Wymagania podstawowe

Moduł Azure schematy wymaga następującego oprogramowania:

- Program Azure PowerShell 1.5.0 lub nowszej. Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/powershell/azure/install-az-ps).
- Modułu PowerShellGet w wersji 2.0.1 lub nowszej. Jeśli jeszcze nie został on zainstalowany lub zaktualizowany, postępuj zgodnie z [tymi instrukcjami](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Instalowanie modułu

Schematy modułu programu PowerShell jest **Az.Blueprint**.

1. Za pomocą monitu **administracyjnego** programu PowerShell uruchom następujące polecenie:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Jeśli **Az.Accounts** jest już zainstalowany, może być konieczne użycie `-AllowClobber` spowoduje wymuszenie instalacji.

1. Sprawdź, czy moduł został zaimportowany i jest poprawna wersja (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Pobieranie definicji planu

Pierwszym krokiem do pracy z przypisania często to pobieranie odwołania do definicji planu.
`Get-AzBlueprint` Polecenie cmdlet pobiera co najmniej jedna definicja planu. Polecenia cmdlet można pobrać definicji planu z grupą zarządzania za pomocą `-ManagementGroupId {mgId}` lub subskrypcji przy użyciu `-SubscriptionId {subId}`. **Nazwa** parametru pobiera definicji planu, ale może być używany z **ManagementGroupId** lub **SubscriptionId**. **Wersja** mogą być używane z **nazwa** się o definicji planu, który jest zwracany. Zamiast **wersji**, przełącznik `-LatestPublished` chwyty ostatnio opublikowanej wersji.

W poniższym przykładzie użyto `Get-AzBlueprint` można pobrać wszystkich wersji definicji planu, o nazwie "101 schematy definition subskrypcji" z konkretną subskrypcję, reprezentowane jako `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Przykładowe dane wyjściowe dla definicji planu z wieloma wersjami wygląda następująco:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

[Planu parametry](../concepts/parameters.md#blueprint-parameters) na planu definicji można rozszerzyć, aby dostarczyć dodatkowych informacji.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Pobieranie przypisań planu

Jeśli przypisanie planu już istnieje, można uzyskać odwołanie do niej przy użyciu `Get-AzBlueprintAssignment` polecenia cmdlet. Polecenie cmdlet pobiera **SubscriptionId** i **nazwa** jako parametry opcjonalne. Jeśli **SubscriptionId** nie zostanie określony, używany jest bieżący kontekst subskrypcji.

W poniższym przykładzie użyto `Get-AzBlueprintAssignment` można pobrać przypisania pojedynczego planu, o nazwie "Przypisania lock-resource-groups" z konkretną subskrypcję, reprezentowane jako `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Przykład danych wyjściowych przypisanie planu wygląda następująco:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Tworzenie przypisań planu

Jeśli jeszcze nie istnieje przypisanie planu, możesz utworzyć za pomocą `New-AzBlueprintAssignment` polecenia cmdlet. To polecenie cmdlet używa następujących parametrów:

- **Nazwa** [wymagane]
  - Określa nazwę przypisanie planu
  - Muszą być unikatowe i nie istnieje w **SubscriptionId**
- **Plan** [wymagane]
  - Określa definicję planu do przypisania
  - Użyj `Get-AzBlueprint` można pobrać obiektu odwołania
- **Lokalizacja** [wymagane]
  - Określa region przypisany systemowo tożsamości i subskrypcji wdrożenia obiektu zarządzanego w
- **Subskrypcja** (opcjonalnie)
  - Określa subskrypcję, w której wdrożono przypisania
  - Jeśli nie zostanie podana, wartość domyślna to bieżącego kontekstu subskrypcji
- **Zablokuj** (opcjonalnie)
  - Definiuje [planu blokowania zasobów](../concepts/resource-locking.md) na potrzeby wdrożone zasoby
  - Obsługiwane opcje: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Jeśli nie zostanie podana, wartość domyślna to _None_
- **SystemAssignedIdentity** (opcjonalnie)
  - Wybierz do tworzenia tożsamości zarządzanej przypisany systemowo przypisania i wdrażania zasobów
  - Domyślne dla zestawu parametrów "tożsamość"
  - Nie można używać z **UserAssignedIdentity**
- **UserAssignedIdentity** (opcjonalnie)
  - Określa przypisanych przez użytkownika tożsamości zarządzanej do używanych w celu przypisania i wdrażania zasobów
  - Część "tożsamość" zestaw parametrów
  - Nie można używać z **SystemAssignedIdentity**
- **Parametr** (opcjonalnie)
  - A [tabeli mieszania](/powershell/module/microsoft.powershell.core/about/about_hash_tables) par klucz/wartość dla ustawienia [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters) na przypisanie planu
  - Wartością domyślną jest parametr dynamiczny **defaultValue** w definicji
  - Jeśli nie podano parametru, a nie ma przypisanego **defaultValue**, parametr jest opcjonalny

    > [!NOTE]
    > **Parametr** nie obsługuje secureStrings.

- **ResourceGroupParameter** (opcjonalnie)
  - A [tabeli mieszania](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktów grupy zasobów
  - Każdy symbol zastępczy artefaktu grupy zasobów będzie miał pary klucz/wartość do ustawiania dynamicznie **nazwa** i/lub **lokalizacji** tego artefaktu grupy zasobów
  - Jeśli nie podano parametru grupy zasobów, a nie ma przypisanego **defaultValue**, parametru grupy zasobów nie jest opcjonalna

Poniższy przykład tworzy nowe przypisanie definicji planu "Mój plan" pobrana z wersji "1.1" `Get-AzBlueprint`, ustawia zarządzaną lokalizację obiektu tożsamości i przypisanie do "westus2", blokad zasobów przy użyciu  _AllResourcesReadOnly_i ustawia tabel skrótów dla obu **parametru** i **ResourceGroupParameter** na konkretną subskrypcję, reprezentowane jako `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Przykładowe dane wyjściowe do tworzenia przypisanie planu wygląda następująco:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>Aktualizowanie przypisań planu

Czasami jest niezbędne do aktualizacji przypisanie planu, który został już utworzony. `Set-AzBlueprintAssignment` Polecenie cmdlet obsługuje tę akcję. Polecenie cmdlet pobiera najbardziej te same parametry, `New-AzBlueprintAssignment` jest polecenie cmdlet, dzięki czemu wszystkie elementy, które została ustawiona na przypisanie do zaktualizowania. Wyjątki od tego czy _nazwa_, _planu_, i _SubscriptionId_. Tylko wartości, które zostały podane są aktualizowane.

Aby dowiedzieć się, co się dzieje w przypadku aktualizowania przypisania planu, zobacz [zasady aktualizowanie przypisań](./update-existing-assignments.md#rules-for-updating-assignments).

- **Nazwa** [wymagane]
  - Określa nazwę przypisanie planu do zaktualizowania
  - Używana do lokalizowania przypisania, aby zaktualizować, nie należy zmieniać przypisania
- **Plan** [wymagane]
  - Określa definicję planu przypisanie planu
  - Użyj `Get-AzBlueprint` można pobrać obiektu odwołania
  - Używana do lokalizowania przypisania, aby zaktualizować, nie należy zmieniać przypisania
- **Lokalizacja** (opcjonalnie)
  - Określa region przypisany systemowo tożsamości i subskrypcji wdrożenia obiektu zarządzanego w
- **Subskrypcja** (opcjonalnie)
  - Określa subskrypcję, w której wdrożono przypisania
  - Jeśli nie zostanie podana, wartość domyślna to bieżącego kontekstu subskrypcji
  - Używana do lokalizowania przypisania, aby zaktualizować, nie należy zmieniać przypisania
- **Zablokuj** (opcjonalnie)
  - Definiuje [planu blokowania zasobów](../concepts/resource-locking.md) na potrzeby wdrożone zasoby
  - Obsługiwane opcje: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (opcjonalnie)
  - Wybierz do tworzenia tożsamości zarządzanej przypisany systemowo przypisania i wdrażania zasobów
  - Domyślne dla zestawu parametrów "tożsamość"
  - Nie można używać z **UserAssignedIdentity**
- **UserAssignedIdentity** (opcjonalnie)
  - Określa przypisanych przez użytkownika tożsamości zarządzanej do używanych w celu przypisania i wdrażania zasobów
  - Część "tożsamość" zestaw parametrów
  - Nie można używać z **SystemAssignedIdentity**
- **Parametr** (opcjonalnie)
  - A [tabeli mieszania](/powershell/module/microsoft.powershell.core/about/about_hash_tables) par klucz/wartość dla ustawienia [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters) na przypisanie planu
  - Wartością domyślną jest parametr dynamiczny **defaultValue** w definicji
  - Jeśli nie podano parametru, a nie ma przypisanego **defaultValue**, parametr jest opcjonalny

    > [!NOTE]
    > **Parametr** nie obsługuje secureStrings.

- **ResourceGroupParameter** (opcjonalnie)
  - A [tabeli mieszania](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktów grupy zasobów
  - Każdy symbol zastępczy artefaktu grupy zasobów będzie miał pary klucz/wartość do ustawiania dynamicznie **nazwa** i/lub **lokalizacji** tego artefaktu grupy zasobów
  - Jeśli nie podano parametru grupy zasobów, a nie ma przypisanego **defaultValue**, parametru grupy zasobów nie jest opcjonalna

Poniższy przykład aktualizuje przypisywanie definicji planu "Mój plan" pobrana z wersji "1.1" `Get-AzBlueprint` , zmieniając tryb blokowania:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Przykładowe dane wyjściowe do tworzenia przypisanie planu wygląda następująco:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Usuwanie przypisania planu

Gdy nadejdzie czas przydziału planu do usunięcia, `Remove-AzBlueprintAssignment` polecenie cmdlet obsługuje tę akcję. Polecenie cmdlet pobiera albo **nazwa** lub **InputObject** Aby określić, które planu przypisania do usunięcia. **SubscriptionId** jest _wymagane_ i musi być podana we wszystkich przypadkach.

Poniższy przykład pobiera istniejącego przypisania planu z `Get-AzBlueprintAssignment` i usuwa go z konkretną subskrypcję, reprezentowane jako `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Przykładowy kod end-to-end

Łączące wszystkie kroki, poniższy przykład pobiera definicji planu, następnie tworzy, aktualizuje i usuwa przypisanie planu w określonej subskrypcji reprezentowane jako `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Kolejne kroki

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).