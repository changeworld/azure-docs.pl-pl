---
title: Samouczek — zarządzanie maszynami wirtualnymi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure 2.0 | Microsoft Docs
description: Z tego samouczka dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure 2.0 zarządzać maszynami wirtualnymi platformy Azure, stosując kontrolę dostępu opartą na rolach, zasady, blokady i tagi
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: aeb94a430e633299637d4e9b96ef6b2c0309b163
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722919"
---
# <a name="tutorial-learn-about-linux-virtual-machine-governance-with-azure-cli-20"></a>Samouczek: informacje o zarządzaniu maszynami wirtualnymi za pomocą interfejsu wiersza polecenia platformy Azure 2.0

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="understand-scope"></a>Objaśnienie zakresu

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

W tym samouczku zastosujesz wszystkie ustawienia zarządzania do grupy zasobów, aby móc łatwo usunąć te ustawienia po zakończeniu.

Utwórzmy tę grupę zasobów.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Grupa zasobów jest obecnie pusta.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Upewnij się, że użytkownicy w organizacji mają odpowiedni poziom dostępu do tych zasobów. Nie udzielaj użytkownikom nieograniczonego dostępu, ale upewnij się, że mogą oni wykonywać swoją pracę. Dzięki [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) możesz zarządzać, którzy użytkownicy mają uprawnienia do wykonywania określonych akcji w danym zakresie.

Aby móc tworzyć i usuwać przypisania roli, użytkownicy muszą mieć dostęp `Microsoft.Authorization/roleAssignments/*`. Ten dostęp jest udzielany za pośrednictwem ról Właściciel lub Administrator dostępu użytkowników.

W przypadku zarządzania rozwiązaniami maszyn wirtualnych dostępne są 3 role specyficzne dla zasobów, które zapewniają najczęściej potrzebny dostęp:

* [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Współautor sieci](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Zamiast przypisywać role poszczególnym użytkownikom, często łatwiej jest [utworzyć grupę usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) dla użytkowników, którzy muszą wykonywać podobne działania. Następnie należy przypisać tę grupę do odpowiedniej roli. Aby uprościć ten artykuł, utwórz grupę usługi Azure Active Directory bez członków. Nadal możesz przypisać tę grupę do roli w zakresie. 

W poniższym przykładzie zostanie utworzona grupa usługi Azure Active Directory o nazwie *VMDemoContributors* i pseudonimie pocztowym *vmDemoGroup*. Pseudonim pocztowy służy jako alias dla grupy.

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

Propagacja grupy w usłudze Azure Active Directory może potrwać kilka chwil od momentu zwrócenia wyników przez wiersz polecenia. Po upływie 20 lub 30 sekund przypisz nową grupę usługi Azure Active Directory do roli współautora maszyny wirtualnej dla grupy zasobów za pomocą polecenia [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create).  Jeśli uruchomisz następujące polecenie przed zakończeniem propagacji, zostanie wyświetlony komunikat o błędzie **Podmiot zabezpieczeń <guid> nie istnieje w katalogu**. Spróbuj ponownie uruchomić polecenie.

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Zazwyczaj należy powtórzyć ten proces dla roli *Współautor sieci* i *Współautor konta magazynu*, aby upewnić się, że użytkownicy mogą zarządzać wdrożonymi zasobami. W tym artykule można pominąć te kroki.

## <a name="azure-policies"></a>Zasady platformy Azure

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Stosowanie zasad

Subskrypcja ma już kilka definicji zasad. Aby wyświetlić dostępne definicje zasad, użyj polecenia [az policy definition list](/cli/azure/policy/definition#az_policy_definition_list):

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Zostaną wyświetlone istniejące definicje zasad. Typ zasad to **Wbudowane** albo **Niestandardowe**. Przejrzyj definicje i znajdź te opisujące warunek, który chcesz przypisać. W tym artykule przypiszesz zasady określające następujące działania:

* Ograniczanie lokalizacji dla wszystkich zasobów.
* Ograniczanie jednostek SKU dla maszyn wirtualnych.
* Przeprowadzanie inspekcji maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

W poniższym przykładzie pobierane są 3 definicje zasad na podstawie nazwy wyświetlanej. Aby przypisać te definicje do grupy zasobów, użyj polecenia [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create). W przypadku niektórych zasad należy podać wartości parametrów, aby określić dozwolone wartości.

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

W poprzednim przykładzie przyjęto, że znasz już parametry dla zasad. Aby wyświetlić parametry, użyj polecenia:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Wdrażanie maszyny wirtualnej

Role i zasady są przypisane, a zatem pora na wdrożenie rozwiązania. Rozmiar domyślny to Standard_DS1_v2, który jest jedną z dozwolonych jednostek SKU. To polecenie tworzy klucze SSH, jeśli nie istnieją one w domyślnej lokalizacji.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Po zakończeniu wdrażania możesz zastosować do rozwiązania więcej ustawień zarządzania.

## <a name="lock-resources"></a>Blokowanie zasobów

[Blokady zasobów](../../azure-resource-manager/resource-group-lock-resources.md) uniemożliwiają użytkownikom w organizacji przypadkowe usunięcie lub zmodyfikowanie krytycznych zasobów. W przeciwieństwie do kontroli dostępu opartej na rolach blokady zasobów stosują ograniczenie do wszystkich użytkowników i ról. Poziom blokady można ustawić na wartość *CanNotDelete* lub *ReadOnly*.

Aby móc tworzyć lub usuwać blokady zarządzania, musisz mieć dostęp do akcji `Microsoft.Authorization/locks/*`. Spośród wbudowanych ról tylko **Właściciel** i **Administrator dostępu użytkowników** mają dostęp do tych akcji.

Aby zablokować maszynę wirtualną i sieciową grupę zabezpieczeń, użyj polecenia [az lock create](/cli/azure/lock#az_lock_create):

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

Aby przetestować blokadę, spróbuj uruchomić następujące polecenie:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Zostanie wyświetlony komunikat o błędzie informujący, że nie można wykonać operacji usuwania z powodu blokady. Grupę zasobów można usunąć tylko po zdjęciu blokad. Ten krok przedstawiono w sekcji [Oczyszczanie zasobów](#clean-up-resources).

## <a name="tag-resources"></a>Tagowanie zasobów

Stosowanie [tagów](../../azure-resource-manager/resource-group-using-tags.md) do zasobów platformy Azure umożliwia ich logiczne zorganizowanie według kategorii. Każdy tag składa się z nazwy i wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Aby zastosować tagi do maszyny wirtualnej, użyj polecenia [az resource tag](/cli/azure/resource#az_resource_tag). Nie są zachowywane żadne istniejące tagi w zasobie.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Znajdowanie zasobów według tagów

Aby znaleźć zasoby z wartością i nazwą tagu, użyj polecenia [az resource list](/cli/azure/resource#az_resource_list):

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Zwróconych wartości można użyć na potrzeby zadań zarządzania, takich jak zatrzymanie wszystkich maszyn wirtualnych z daną wartością tagu.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Wyświetlanie kosztów według wartości tagów

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zablokowanej sieciowej grupy zabezpieczeń nie można usunąć, dopóki blokada nie zostanie zdjęta. Aby zdjąć blokadę, pobierz identyfikatory blokad i podaj je w poleceniu [az lock delete](/cli/azure/lock#az_lock_delete):

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


## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Przypisywanie użytkowników do roli
> * Stosowanie zasad wymuszających standardy
> * Ochrona krytycznych zasobów za pomocą blokad
> * Tagowanie zasobów na potrzeby rozliczeń i zarządzania

Przejdź do następnego samouczka, aby dowiedzieć się więcej o maszynach wirtualnych o wysokiej dostępności.

> [!div class="nextstepaction"]
> [Monitorowanie maszyn wirtualnych](tutorial-monitoring.md)

