---
title: Ograniczanie instalacji rozszerzenia maszyny Wirtualnej za pomocą usługi Azure Policy
description: Użyj usługi Azure Policy, aby ograniczyć wdrożenia rozszerzeń maszyn wirtualnych.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 3c660f7e05af43c2aad6f7283e32cfc1d85571ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066830"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Ograniczanie instalacji rozszerzeń na maszynach wirtualnych z systemem Linux za pomocą usługi Azure Policy

Aby zapobiec używaniu lub instalacji niektórych rozszerzeń na maszynach wirtualnych z systemem Linux, możesz utworzyć zasady platformy Azure przy użyciu interfejsu wiersza polecenia, aby ograniczyć rozszerzenia dla maszyn wirtualnych w ramach grupy zasobów. 

W tym samouczku używa interfejsu wiersza polecenia w usłudze Azure Cloud Shell, który jest stale aktualizowany do najnowszej wersji. Jeśli chcesz uruchomić interfejsu wiersza polecenia platformy Azure lokalnie, musisz zainstalować wersję 2.0.26 lub nowszą. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Tworzenie pliku reguł

Aby ograniczyć, jakie rozszerzenia mogą być instalowane, należy mieć [regułę,](../../governance/policy/concepts/definition-structure.md#policy-rule) aby zapewnić logikę do identyfikowania rozszerzenia.

W tym przykładzie pokazano, jak odmówić instalacji rozszerzeń opublikowanych przez "Microsoft.OSTCExtensions" przez utworzenie pliku reguł w usłudze Azure Cloud Shell, ale jeśli pracujesz w interfejsu wiersza polecenia lokalnie, można również utworzyć plik lokalny i zastąpić ścieżkę (~/clouddrive) ścieżką do pliku lokalnego na komputerze.

W [bash Cloud Shell](https://shell.azure.com/bash), wpisz:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Skopiuj i wklej następujący plik .json do pliku.

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

Po zakończeniu naciśnij klawisz **Esc,** a następnie wpisz **:wq,** aby zapisać i zamknąć plik.


## <a name="create-a-parameters-file"></a>Tworzenie pliku parametrów

Potrzebny jest również plik [parametrów,](../../governance/policy/concepts/definition-structure.md#parameters) który tworzy strukturę, która będzie używana do przekazywania na liście rozszerzeń do zablokowania. 

W tym przykładzie pokazano, jak utworzyć plik parametrów dla maszyn wirtualnych z systemem Linux w aplikacji Cloud Shell, ale jeśli pracujesz lokalnie w interfejsie wiersza polecenia, możesz również utworzyć plik lokalny i zastąpić ścieżkę (~/clouddrive) ścieżką do pliku lokalnego na komputerze.

W [bash Cloud Shell](https://shell.azure.com/bash), wpisz:

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Skopiuj i wklej następujący plik .json do pliku.

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

Po zakończeniu naciśnij klawisz **Esc,** a następnie wpisz **:wq,** aby zapisać i zamknąć plik.

## <a name="create-the-policy"></a>Tworzenie zasad

Definicja zasad jest obiektem używanym do przechowywania konfiguracji, której chcesz użyć. Definicja zasad używa reguł i parametrów plików do definiowania zasad. Utwórz definicję zasad przy użyciu [definicji zasad az create](/cli/azure/role/assignment?view=azure-cli-latest).

W tym przykładzie reguły i parametry są plikami utworzonymi i przechowywanymi jako pliki .json w powłoce chmury.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Przypisywanie zasad

W tym przykładzie przypisuje zasadę do grupy zasobów przy użyciu [przydziału zasad az .](/cli/azure/policy/assignment) Żadna maszyna wirtualna utworzona w grupie zasobów **myResourceGroup** nie będzie mogła zainstalować dostępu do maszyny Wirtualnej systemu Linux ani rozszerzeń skryptów niestandardowych dla systemu Linux. Grupa zasobów musi istnieć, zanim będzie można przypisać zasadę.

Użyj [listy kont AZ,](/cli/azure/account?view=azure-cli-latest) aby uzyskać identyfikator subskrypcji do użycia zamiast tego w przykładzie.


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

Przetestuj zasady, tworząc nową maszynę wirtualną i próbując dodać nowego użytkownika.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Spróbuj utworzyć nowego użytkownika o nazwie **myNewUser** przy użyciu rozszerzenia dostępu do maszyny Wirtualnej.

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
## <a name="remove-the-policy"></a>Usuwanie zasad

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Azure Policy](../../governance/policy/overview.md).