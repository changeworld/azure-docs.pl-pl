---
title: Jak zarządzać przydziałami za pomocą programu PowerShell
description: Dowiedz się, jak zarządzać przydziałami planu za pomocą oficjalnego modułu programu Azure Blueprints PowerShell, Az.Blueprint.
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: b16cf887ba8bfc51616839db5f4af87944ec686d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247405"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Jak zarządzać przydziałami za pomocą programu PowerShell

Przypisanie planu można zarządzać za pomocą modułu **Az.Blueprint** Azure PowerShell. Moduł obsługuje pobieranie, tworzenie, aktualizowanie i usuwanie przypisań. Moduł można również pobrać szczegółowe informacje na temat istniejących definicji planu. W tym artykule opisano, jak zainstalować moduł i rozpocząć korzystanie z niego.

## <a name="add-the-azblueprint-module"></a>Dodaj moduł Az.Blueprint

Aby włączyć program Azure PowerShell do zarządzania przydziałami planu, należy dodać moduł. Ten moduł może być używany z lokalnie zainstalowanym programem PowerShell, [programem Azure Cloud Shell](https://shell.azure.com) lub [obrazem Docker programu Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Wymagania podstawowe

Moduł Azure Blueprints wymaga następującego oprogramowania:

- Program Azure PowerShell 1.5.0 lub nowszy. Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/powershell/azure/install-az-ps).
- Modułu PowerShellGet w wersji 2.0.1 lub nowszej. Jeśli jeszcze nie został on zainstalowany lub zaktualizowany, postępuj zgodnie z [tymi instrukcjami](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Instalowanie modułu

Moduł Blueprints dla programu PowerShell to **Az.Blueprint**.

1. Za pomocą monitu **administracyjnego** programu PowerShell uruchom następujące polecenie:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Jeśli **Az.Accounts** jest już zainstalowany, może `-AllowClobber` być konieczne użycie, aby wymusić instalację.

1. Sprawdź, czy moduł został zaimportowany i jest poprawną wersją (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Pobierz definicje planu

Pierwszym krokiem do pracy z przypisaniem jest często uzyskanie odwołania do definicji planu.
Polecenie `Get-AzBlueprint` cmdlet pobiera jedną lub więcej definicji planu. Polecenie cmdlet może uzyskać definicje planu `-ManagementGroupId {mgId}` z grupy `-SubscriptionId {subId}`zarządzania z lub subskrypcji z . **Parametr Name** pobiera definicję planu, ale musi być używany z **identyfikatorem ManagementGroupId** lub **SubscriptionId**. **Wersja** może służyć z **name** być bardziej wyraźne, o których definicji planu jest zwracany. Zamiast **wersji**przełącznik `-LatestPublished` chwyta ostatnio opublikowaną wersję.

Poniższy przykład `Get-AzBlueprint` używa, aby uzyskać wszystkie wersje definicji planu o nazwie "101-plany-definicja subskrypcji" z określonej subskrypcji reprezentowane jako: `{subId}`

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

[Parametry planu](../concepts/parameters.md#blueprint-parameters) w definicji planu można rozszerzyć, aby zapewnić więcej informacji.

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

## <a name="get-blueprint-assignments"></a>Pobierz przydziały planu

Jeśli przypisanie planu już istnieje, można uzyskać odwołanie `Get-AzBlueprintAssignment` do niego za pomocą polecenia cmdlet. Polecenie cmdlet przyjmuje **Identyfikator subskrypcji** i **nazwę** jako parametry opcjonalne. Jeśli **identyfikator subskrypcji** nie jest określony, używany jest bieżący kontekst subskrypcji.

W poniższym `Get-AzBlueprintAssignment` przykładzie użyto do uzyskania pojedynczego przydziału planu o nazwie "Przydział-lock-resource-groups" z określonej subskrypcji reprezentowane jako: `{subId}`

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Przykładowe dane wyjściowe dla przypisania planu wygląda następująco:

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

## <a name="create-blueprint-assignments"></a>Tworzenie przydziałów planu

Jeśli przypisanie planu jeszcze nie istnieje, można `New-AzBlueprintAssignment` je utworzyć za pomocą polecenia cmdlet. To polecenie cmdlet wykorzystuje następujące parametry:

- **Nazwa** [wymagane]
  - Określa nazwę przypisania planu
  - Musi być unikatowa i jeszcze nie istnieje w **SubscriptionId**
- **Plan** [wymagany]
  - Określa definicję planu do przypisania
  - Użyj, `Get-AzBlueprint` aby uzyskać obiekt odniesienia
- **Lokalizacja** [wymagane]
  - Określa region obiektu tożsamości zarządzanej przypisanego przez system i wdrożenia subskrypcji, który ma zostać utworzony w
- **Subskrypcja** (opcjonalnie)
  - Określa subskrypcję, do która jest wdrażana przypisanie
  - Jeśli nie podano, domyślnie bieżący kontekst subskrypcji
- **Blokada** (opcjonalnie)
  - Definiuje [blokowanie zasobów planu](../concepts/resource-locking.md) do użycia dla wdrożonych zasobów
  - Obsługiwane opcje: _Brak_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Jeśli nie podano, domyślnie _brak_
- **Przypisana do systemuTożsamość** (opcjonalnie)
  - Wybierz, aby utworzyć przypisaną do systemu tożsamość zarządzaną dla przydziału i wdrożyć zasoby
  - Domyślnie dla zestawu parametrów "tożsamość"
  - Nie można używać z **przypisaną do użytkownikatożernością**
- **Przypisana do użytkownikaTożsamość** (opcjonalnie)
  - Określa tożsamość zarządzaną przypisaną przez użytkownika do użycia w celu przypisania i wdrożenia zasobów
  - Część zestawu parametrów "identity"
  - Nie można używać z **przypisaną do systemutożsamością**
- **Parametr** (opcjonalnie)
  - [Tabela mieszania](/powershell/module/microsoft.powershell.core/about/about_hash_tables) par klucz/wartość do ustawiania [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters) w przypisaniu planu
  - Domyślnym parametrem dynamicznym jest **domyślnawartość** w definicji
  - Jeśli parametr nie jest podany i nie ma **defaultValue,** parametr nie jest opcjonalny

    > [!NOTE]
    > **Parametr** nie obsługuje secureStrings.

- **ResourceGroupParameter** (opcjonalnie)
  - [Tabela mieszania](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktów grupy zasobów
  - Każdy symbol zastępczy artefaktu grupy zasobów ma pary klucz/wartość do dynamicznego ustawiania **nazwy** i **lokalizacji** w tym artefaktu grupy zasobów
  - Jeśli parametr grupy zasobów nie jest podany i nie ma **wartości domyślnej,** parametr grupy zasobów nie jest opcjonalny
- **Plik przydziału** (opcjonalnie)
  - Ścieżka do reprezentacji przypisania planu przez plik JSON
  - Ten parametr jest częścią zestawu parametrów programu PowerShell, który zawiera tylko **Name**, **Blueprint**i **SubscriptionId**, plus wspólne parametry.

### <a name="example-1-provide-parameters"></a>Przykład 1: Podaj parametry

Poniższy przykład tworzy nowe przypisanie wersji "1.1" definicji planu "mój `Get-AzBlueprint`plan" pobranej z , ustawia zarządzaną lokalizację obiektu tożsamości i przypisania na "westus2", blokuje zasoby za pomocą _AllResourcesReadOnly_i ustawia tabele mieszania dla **parametru** i **resourcegroupparameter** w określonej subskrypcji reprezentowane jako: `{subId}`

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Przykładowe dane wyjściowe dotyczące tworzenia przydziału planu wygląda następująco:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>Przykład 2: Używanie pliku definicji przypisania JSON

Poniższy przykład tworzy prawie takie samo przypisanie jak [przykład 1](#example-1-provide-parameters).
Zamiast przekazywania parametrów do polecenia cmdlet, w przykładzie pokazano użycie pliku definicji przypisania JSON i parametru **AssignmentFile.** Ponadto **właściwość excludedPrincipals** jest skonfigurowana jako część **blokad**. Nie ma parametru Programu PowerShell dla **excludedPrincipals** i właściwość można skonfigurować tylko przez ustawienie go za pomocą pliku definicji przypisania JSON.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

Na przykład pliku definicji przypisania JSON dla tożsamości zarządzanej przypisanej przez użytkownika zobacz treść żądania w [przykład: Przypisanie z przypisaną przez użytkownika tożsamością zarządzaną](/rest/api/blueprints/assignments/createorupdate#examples) dla interfejsu API REST.

## <a name="update-blueprint-assignments"></a>Aktualizowanie przydziałów planu

Czasami konieczne jest zaktualizowanie przypisania planu, które zostało już utworzone. Polecenie `Set-AzBlueprintAssignment` cmdlet obsługuje tę akcję. Polecenie cmdlet przyjmuje większość tych `New-AzBlueprintAssignment` samych parametrów, które wykonuje polecenie cmdlet, umożliwiając aktualizowanie wszystkiego, co zostało ustawione na przypisaniu. Wyjątkami są _Nazwa_, _Plan_i _SubscriptionId_. Aktualizowane są tylko podane wartości.

Aby dowiedzieć się, co się dzieje podczas aktualizowania przypisania planu, zobacz [reguły aktualizowania przydziałów](./update-existing-assignments.md#rules-for-updating-assignments).

- **Nazwa** [wymagane]
  - Określa nazwę przypisania planu do aktualizacji
  - Służy do lokalizowania przydziału do aktualizacji, a nie do zmiany przypisania
- **Plan** [wymagany]
  - Określa definicję planu przypisania planu
  - Użyj, `Get-AzBlueprint` aby uzyskać obiekt odniesienia
  - Służy do lokalizowania przydziału do aktualizacji, a nie do zmiany przypisania
- **Lokalizacja** (opcjonalnie)
  - Określa region obiektu tożsamości zarządzanej przypisanego przez system i wdrożenia subskrypcji, który ma zostać utworzony w
- **Subskrypcja** (opcjonalnie)
  - Określa subskrypcję, do która jest wdrażana przypisanie
  - Jeśli nie podano, domyślnie bieżący kontekst subskrypcji
  - Służy do lokalizowania przydziału do aktualizacji, a nie do zmiany przypisania
- **Blokada** (opcjonalnie)
  - Definiuje [blokowanie zasobów planu](../concepts/resource-locking.md) do użycia dla wdrożonych zasobów
  - Obsługiwane opcje: _Brak_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **Przypisana do systemuTożsamość** (opcjonalnie)
  - Wybierz, aby utworzyć przypisaną do systemu tożsamość zarządzaną dla przydziału i wdrożyć zasoby
  - Domyślnie dla zestawu parametrów "tożsamość"
  - Nie można używać z **przypisaną do użytkownikatożernością**
- **Przypisana do użytkownikaTożsamość** (opcjonalnie)
  - Określa tożsamość zarządzaną przypisaną przez użytkownika do użycia w celu przypisania i wdrożenia zasobów
  - Część zestawu parametrów "identity"
  - Nie można używać z **przypisaną do systemutożsamością**
- **Parametr** (opcjonalnie)
  - [Tabela mieszania](/powershell/module/microsoft.powershell.core/about/about_hash_tables) par klucz/wartość do ustawiania [parametrów dynamicznych](../concepts/parameters.md#dynamic-parameters) w przypisaniu planu
  - Domyślnym parametrem dynamicznym jest **domyślnawartość** w definicji
  - Jeśli parametr nie jest podany i nie ma **defaultValue,** parametr nie jest opcjonalny

    > [!NOTE]
    > **Parametr** nie obsługuje secureStrings.

- **ResourceGroupParameter** (opcjonalnie)
  - [Tabela mieszania](/powershell/module/microsoft.powershell.core/about/about_hash_tables) artefaktów grupy zasobów
  - Każdy symbol zastępczy artefaktu grupy zasobów ma pary klucz/wartość do dynamicznego ustawiania **nazwy** i **lokalizacji** w tym artefaktu grupy zasobów
  - Jeśli parametr grupy zasobów nie jest podany i nie ma **wartości domyślnej,** parametr grupy zasobów nie jest opcjonalny

Poniższy przykład aktualizuje przypisanie wersji "1.1" definicji planu "mój `Get-AzBlueprint` plan" pobranej przez zmianę trybu blokady:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Przykładowe dane wyjściowe dotyczące tworzenia przydziału planu wygląda następująco:

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

## <a name="remove-blueprint-assignments"></a>Usuwanie przydziałów planu

Gdy nadszedł czas na przypisanie planu do `Remove-AzBlueprintAssignment` usunięcia, polecenie cmdlet obsługuje tę akcję. Polecenie cmdlet przyjmuje **nazwę** lub **inputobject,** aby określić, które przypisanie planu do usunięcia. **Identyfikator subskrypcji** jest _wymagany_ i musi być podany we wszystkich przypadkach.

Poniższy przykład pobiera istniejące przypisanie `Get-AzBlueprintAssignment` planu, a następnie usuwa go `{subId}`z określonej subskrypcji reprezentowanej jako:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Przykład kodu end-to-end

Łącząc wszystkie kroki, poniższy przykład pobiera definicję planu, a następnie tworzy, aktualizuje i `{subId}`usuwa przypisanie planu w określonej subskrypcji reprezentowane jako:

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
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

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [cyklu życia planu](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).