---
title: Użyj zasad Azure, aby ograniczyć instalacja rozszerzenia maszyny Wirtualnej | Dokumentacja firmy Microsoft
description: Użyj zasad Azure, aby ograniczyć wdrażania rozszerzenia maszyny Wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: danis;cynthn
ms.openlocfilehash: 8e65b82730884947633688db9ed50080b96e0b8e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30248156"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Użyj zasad Azure, aby ograniczyć instalacji rozszerzeń na maszynach wirtualnych systemu Linux

Jeśli chcesz uniemożliwić używanie lub instalacji niektórych rozszerzeń dla maszyn wirtualnych systemu Linux, możesz utworzyć zasady Azure przy użyciu interfejsu wiersza polecenia, można ograniczyć rozszerzenia dla maszyn wirtualnych w grupie zasobów. 

W tym samouczku korzysta z interfejsu wiersza polecenia powłoki chmury Azure, który jest stale aktualizowany do najnowszej wersji. Jeśli chcesz uruchomić lokalnie wiersza polecenia platformy Azure, musisz zainstalować wersję 2.0.26 lub nowszym. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Tworzenie pliku reguł

Aby ograniczyć, jakie rozszerzenia można zainstalować, musisz mieć [reguły](/azure/azure-policy/policy-definition#policy-rule) zapewnienie logiki, aby zidentyfikować rozszerzenia.

W tym przykładzie przedstawiono sposób Odmów instalowanie rozszerzeń opublikowanych przez "Microsoft.OSTCExtensions", tworząc plik reguł w powłoce chmury Azure, ale jeśli pracujesz w CLI lokalnie, można również utworzyć lokalnego pliku i Zamień na ścieżkę do ścieżki (~/clouddrive) Plik lokalny na komputerze.

W [chmury powłoki bash](https://shell.azure.com/bash), wpisz:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Skopiuj i wklej następujący JSON do pliku.

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

Gdy wszystko będzie gotowe, kliknij przycisk **Esc** klucza, a następnie wpisz **: wq** Zapisz i zamknij plik.


## <a name="create-a-parameters-file"></a>Utwórz plik parametrów

Należy również [parametry](/azure/azure-policy/policy-definition#parameters) pliku strukturę do użycia dla przekazywania na liście rozszerzeń do blokowania. 

W tym przykładzie przedstawiono sposób tworzenia pliku parametrów dla maszyn wirtualnych systemu Linux w powłoce chmury, ale jeśli pracujesz w CLI lokalnie, można również utworzyć lokalnego pliku i Zamień ścieżki (~/clouddrive) ścieżka do pliku lokalnego na komputerze.

W [chmury powłoki bash](https://shell.azure.com/bash), wpisz:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Skopiuj i wklej następujący JSON do pliku.

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

Gdy wszystko będzie gotowe, kliknij przycisk **Esc** klucza, a następnie wpisz **: wq** Zapisz i zamknij plik.

## <a name="create-the-policy"></a>Tworzenie zasad

Definicja zasad jest obiekt służący do przechowywania konfiguracji, który chcesz użyć. Definicja zasad używa plików reguł i parametry do zdefiniowania zasad. Tworzenie przy użyciu definicji zasad [utworzenia definicji zasad az](/cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create).

W tym przykładzie reguły i parametry są pliki tworzone i przechowywane jako pliki JSON w chmurze powłoki.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Przypisania zasad

W tym przykładzie przypisuje zasady grupy zasobów przy użyciu [utworzenia przypisania zasad az](/cli/azure/policy/assignment#az_policy_assignment_create). Wszystkie maszyny Wirtualnej utworzonej w **myResourceGroup** grupy zasobów nie będzie mógł instalować dostęp maszyny Wirtualnej systemu Linux lub rozszerzenia niestandardowego skryptu dla systemu Linux. Nazwa grupy zasobów musi istnieć przed przypisaniem zasady.

Użyj [listy kont az](/cli/azure/account?view=azure-cli-latest#az_account_list) uzyskać identyfikator subskrypcji do użycia zamiast co w przykładzie.


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

Testowanie zasad poprzez utworzenie nowej maszyny Wirtualnej, a następnie próby dodania nowego użytkownika.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Próba utworzenia nowego użytkownika o nazwie **myNewUser** przy użyciu rozszerzenia dostępu do maszyny Wirtualnej.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Usuń przypisanie

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Usuń zasady

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [zasadami Azure](../../azure-policy/azure-policy-introduction.md).
