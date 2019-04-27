---
title: Ograniczanie instalacji rozszerzeń maszyny Wirtualnej za pomocą usługi Azure Policy | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Policy, aby ograniczyć wdrożenia rozszerzenia maszyn wirtualnych.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: 1f71276c25e3ec1e5791d9b35f89aa95190c6afd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543261"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Ograniczanie instalacji rozszerzeń na maszynach wirtualnych z systemem Linux za pomocą usługi Azure Policy

Jeśli chcesz uniemożliwić używanie lub instalacji niektórych rozszerzeń na maszynach wirtualnych systemu Linux, możesz utworzyć usługi Azure policy, aby ograniczyć rozszerzenia dla maszyn wirtualnych w grupie zasobów przy użyciu interfejsu wiersza polecenia. 

W tym samouczku korzysta z interfejsu wiersza polecenia w ramach usługi Azure Cloud Shell, które są stale aktualizowane do najnowszej wersji. Jeśli chcesz uruchomić z wiersza polecenia platformy Azure lokalnie, musisz zainstalować wersję 2.0.26 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Utwórz plik reguł

Aby można było ograniczyć, jakie rozszerzenia można zainstalować, musisz mieć [reguły](../../governance/policy/concepts/definition-structure.md#policy-rule) zapewnienie logiki, aby zidentyfikować rozszerzenia.

W tym przykładzie pokazano, jak odmowa, instalowanie rozszerzenia publikowane przez "Microsoft.OSTCExtensions" przez utworzenie pliku reguły w usłudze Azure Cloud Shell, ale jeśli pracujesz w interfejsie wiersza polecenia lokalnie, możesz również utworzyć lokalnego pliku i Zamień na ścieżkę do ścieżki (~/clouddrive) Plik lokalny na komputerze.

W [bash w usłudze Cloud Shell](https://shell.azure.com/bash), wpisz:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Skopiuj i wklej następujące JSON do pliku.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Gdy wszystko będzie gotowe, trafienia **Esc** klucza, a następnie wpisz **: wq** Zapisz i zamknij plik.


## <a name="create-a-parameters-file"></a>Utwórz plik parametrów

Należy również [parametry](../../governance/policy/concepts/definition-structure.md#parameters) pliku, który tworzy strukturę do użycia do przekazania na liście rozszerzeń, aby zablokować. 

W tym przykładzie pokazano, jak utworzyć plik parametrów dla maszyn wirtualnych systemu Linux w usłudze Cloud Shell, ale jeśli pracujesz w interfejsie wiersza polecenia lokalnie, możesz również utworzyć lokalnego pliku i ścieżka (~/clouddrive) należy zastąpić ścieżkę do pliku lokalnego na komputerze.

W [bash w usłudze Cloud Shell](https://shell.azure.com/bash), wpisz:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Skopiuj i wklej następujące JSON do pliku.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Gdy wszystko będzie gotowe, trafienia **Esc** klucza, a następnie wpisz **: wq** Zapisz i zamknij plik.

## <a name="create-the-policy"></a>Tworzenie zasad

Definicja zasad jest obiekt służący do przechowywania konfiguracji, który chcesz użyć. Definicja zasad używa plików zasady i parametry do zdefiniowania zasad. Utwórz definicję zasad za pomocą [utworzenia definicji zasad az](/cli/azure/role/assignment?view=azure-cli-latest).

W tym przykładzie zasady i parametry są pliki tworzone i przechowywane jako pliki JSON w usługi cloud shell.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Przypisz zasady

W tym przykładzie przypisuje grupę zasobów za pomocą zasad [utworzenia przypisania zasad az](/cli/azure/policy/assignment). Wszystkie maszyny Wirtualnej utworzonej w **myResourceGroup** grupy zasobów nie będzie można zainstalować dostęp do maszyny Wirtualnej systemu Linux lub rozszerzenia niestandardowego skryptu dla systemu Linux. Grupa zasobów musi istnieć przed przypisaniem zasad.

Użyj [listy kont az](/cli/azure/account?view=azure-cli-latest) można pobrać Identyfikatora subskrypcji należy użyć zamiast co w przykładzie.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Testowanie zasad

Testowanie zasad, tworząc nową maszynę Wirtualną, a próby dodania nowego użytkownika.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Spróbuj utworzyć nowego użytkownika o nazwie **myNewUser** za pomocą rozszerzenia VM Access.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Usuwanie przypisania

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Usuń zasady

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [usługi Azure Policy](../../governance/policy/overview.md).