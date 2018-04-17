---
title: Maszyny wirtualne platformy Azure z wiersza polecenia platformy Azure regulują | Dokumentacja firmy Microsoft
description: Samouczek — Zarządzanie maszyn wirtualnych platformy Azure, stosując RBAC, zasady blokad i tagi z wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: a7d44e421162cf5784dde58f757e235d12b63cba
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="virtual-machine-governance-with-azure-cli"></a>Zarządzanie maszyny wirtualnej z wiersza polecenia platformy Azure

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Aby zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, zobacz [zainstalować Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="understand-scope"></a>Zrozumieć zakres

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

W tym samouczku zastosowanie wszystkich ustawień zarządzania do grupy zasobów, można łatwo usunąć te ustawienia po zakończeniu.

Umożliwia tworzenie tej grupy zasobów.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Grupa zasobów jest obecnie pusta.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Chcesz upewnić się, że użytkownicy w organizacji mają odpowiedni poziom dostępu do tych zasobów. Nie chcesz udzielić nieograniczonego dostępu do użytkowników, ale należy również upewnij się, że mogą je z siecią firmową. [Kontrola dostępu oparta na rolach](../../role-based-access-control/overview.md) pozwala na zarządzanie, którzy użytkownicy mają uprawnienia do wykonania określonych czynności w zakresie.

Aby utworzyć i usunąć przypisania roli, użytkownicy muszą mieć `Microsoft.Authorization/roleAssignments/*` dostępu. Ten dostęp za pomocą ról właściciel lub Administrator dostępu użytkowników.

Do zarządzania rozwiązań maszyny wirtualnej, istnieją trzy role określonych zasobów, które zapewniają dostęp do potrzebnych:

* [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Współautor sieci](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Zamiast przypisywać role poszczególnym użytkownikom, łatwiej często [utworzyć grupy usługi Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) dla użytkowników, którzy potrzebują podjęcia działań podobne. Następnie przypisać do odpowiedniej roli. Aby uprościć w tym artykule, należy utworzyć grupy usługi Azure Active Directory bez członków. Nadal można przypisać tej grupy do roli dla zakresu. 

Poniższy przykład tworzy grupę usługi Azure Active Directory o nazwie *VMDemoContributors* z pseudonim poczty *vmDemoGroup*. Pseudonim poczty służy jako alias dla grupy.

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

Może potrwać kilka minut po powrocie z wiersza polecenia dla grupy w celu propagowania w usłudze Azure Active Directory. Po upływie 20 lub 30 sekund, użyj [utworzenia przypisania roli az](/cli/azure/role/assignment#az_role_assignment_create) polecenie, aby przypisać nową grupę w usłudze Azure Active Directory do roli współautora maszyny wirtualnej dla grupy zasobów.  Uruchamiając następujące polecenie przed zakończeniem propagacji go zostanie wyświetlony błąd informujący o **główna <guid> nie istnieje w katalogu**. Spróbuj ponownie uruchomić polecenie.

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Zwykle, powtórz proces *współautora sieci* i *współautora konta magazynu* się upewnić, że użytkownicy są dodawani do zarządzania zasobami wdrożone. W tym artykule można pominąć te kroki.

## <a name="azure-policies"></a>Zasady usługi Azure

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Stosowanie zasad

Subskrypcja obejmuje już kilka definicje zasad. Aby wyświetlić definicje dostępnych zasad, użyj [listy definicji zasad az](/cli/azure/policy/definition#az_policy_definition_list) polecenia:

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Możesz sprawdzić istniejące definicje zasad. Typ zasad jest **wbudowane** lub **niestandardowy**. Przejrzyj definicje dla tych, które opisują warunek, który chcesz przypisać. W tym artykule należy przypisać zasady który:

* Ograniczenie lokalizacji dla wszystkich zasobów.
* Ogranicz jednostki SKU dla maszyn wirtualnych.
* Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

W poniższym przykładzie możesz pobrać trzy definicji zasad opartych na nazwę wyświetlaną. Możesz użyć [utworzenia przypisania zasad az](/cli/azure/policy/assignment#az_policy_assignment_create) polecenie, aby przypisać tych definicji do grupy zasobów. W przypadku niektórych zasad musisz podać wartości parametrów, aby określić dozwolone wartości.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

Poprzednim przykładzie przyjęto założenie, że znasz już parametry dla zasad. Aby wyświetlić parametry, należy użyć:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Wdróż maszynę wirtualną

Przypisano ról i zasad, więc wszystko jest gotowe do wdrożenia rozwiązania. Rozmiar domyślny to Standard_DS1_v2, które jest jednym z dozwolonych jednostki SKU. Polecenie tworzy kluczy SSH, jeśli nie istnieją w domyślnej lokalizacji.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Po zakończeniu wdrożenia, można zastosować więcej ustawienia zarządzania do rozwiązania.

## <a name="lock-resources"></a>Blokowanie zasobów

[Blokowania zasobów](../../azure-resource-manager/resource-group-lock-resources.md) uniemożliwić użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie kluczowych zasobów. W przeciwieństwie do kontroli dostępu opartej na rolach blokowania zasobów mają zastosowanie ograniczenia we wszystkich użytkowników i ról. Można ustawić poziom blokady *CanNotDelete* lub *tylko do odczytu*.

Aby utworzyć lub usunąć blokady zarządzania, musi mieć dostęp do `Microsoft.Authorization/locks/*` akcje. Wbudowanych ról, tylko **właściciela** i **Administrator dostępu użytkowników** otrzymują te akcje.

Aby zablokować maszyny wirtualnej i grupy zabezpieczeń sieci, należy użyć [utworzyć blokady az](/cli/azure/lock#az_lock_create) polecenia:

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Aby przetestować blokad, spróbuj uruchomić następujące polecenie:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Zostanie wyświetlony komunikat o błędzie informujący, że nie można wykonać operacji usuwania z powodu blokady. Tylko można usunąć grupy zasobów, w szczególności usunięcia blokady. Ten krok jest wyświetlany w obszarze [wyczyścić zasoby](#clean-up-resources).

## <a name="tag-resources"></a>Tag zasobów

Należy zastosować [tagi](../../azure-resource-manager/resource-group-using-tags.md) do zasobów platformy Azure na organizację je według kategorii. Każdy tag składa się z nazwy i wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Aby zastosować tagi do maszyny wirtualnej, użyj [tag zasobu az](/cli/azure/resource#az_resource_tag) polecenia. Wszystkie istniejące znaczniki w zasobie nie są zachowywane.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Znajdź zasoby według znaczników

Aby znaleźć zasoby z tagu nazwy i wartości, należy użyć [listę zasobów az](/cli/azure/resource#az_resource_list) polecenia:

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Można użyć wartości zwracane dla zadań zarządzania, takich jak zatrzymanie wszystkich maszyn wirtualnych z wartością tagu.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Wyświetlanie kosztów przez wartości tagów

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Nie można usunąć grupy zabezpieczeń sieci zablokowane, aż do usunięcia blokady. Aby usunąć blokadę, pobrać identyfikatorów blokad i je, aby zapewnić [usunąć blokady az](/cli/azure/lock#az_lock_delete) polecenia:

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete). Zakończ sesję SSH i wróć do maszyny wirtualnej, a następnie usuń zasoby w następujący sposób:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Przypisywanie użytkowników do roli
> * Stosowanie zasad, które wymuszają standardów
> * Chroń krytyczne zasoby z blokad
> * Tag zasoby dotyczące rozliczeń i zarządzania

Przejdź do następnego samouczkiem, aby poznać sposób wysokiej dostępności maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Monitorowanie maszyn wirtualnych](tutorial-monitoring.md)

