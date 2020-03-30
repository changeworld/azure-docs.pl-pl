---
title: Co-jeśli (Wersja zapoznawcza) wdrożenia szablonu
description: Określ, jakie zmiany nastąpią w zasobach przed wdrożeniem szablonu usługi Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: bc42585204e5cc2c3ece5293a3934fd22fe8507b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156450"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Co do jeśli— wdrożenie szablonu ARM (wersja zapoznawcza)

Przed wdrożeniem szablonu usługi Azure Resource Manager (ARM) można wyświetlić podgląd zmian, które nastąpią. Usługa Azure Resource Manager zapewnia operację co jeśli, aby zobaczyć, jak zasoby zmienią się po wdrożeniu szablonu. Operacja co jeśli nie wprowadzać żadnych zmian w istniejących zasobach. Zamiast tego przewiduje zmiany, jeśli określony szablon jest wdrażany.

> [!NOTE]
> Operacja co jeśli jest obecnie w wersji zapoznawczej. Aby go użyć, musisz [zarejestrować się w wersji zapoznawczej](https://aka.ms/armtemplatepreviews). W wersji zapoznawczej wyniki mogą czasami pokazać, że zasób zmieni się, gdy faktycznie nie nastąpi żadna zmiana. Pracujemy nad ograniczeniem tych problemów, ale potrzebujemy Twojej pomocy. Prosimy o zgłoszenie [https://aka.ms/whatifissues](https://aka.ms/whatifissues)tych problemów na poziomie .

Można użyć operacji co jeśli z poleceniami programu PowerShell lub operacjami interfejsu API REST.

W programie PowerShell dane wyjściowe zawierają wyniki oznaczone kolorami, które ułatwią wyświetlanie różnych typów zmian.

![Wdrożenie szablonu Menedżera zasobów co do operacji fullresourcepayload i typy zmian](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Tekst ouptput jest:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>Polecenia co do jeśli

Można użyć interfejsu Azure PowerShell lub interfejsu API REST platformy Azure dla operacji co jeśli.

### <a name="azure-powershell"></a>Azure PowerShell

Aby wyświetlić podgląd zmian przed wdrożeniem szablonu, dodaj parametr przełącznika `-Whatif` do polecenia wdrażania.

* `New-AzResourceGroupDeployment -Whatif`dla wdrożeń grup zasobów
* `New-AzSubscriptionDeployment -Whatif`i `New-AzDeployment -Whatif` dla wdrożeń na poziomie subskrypcji

Można też użyć `-Confirm` parametru switch, aby wyświetlić podgląd zmian i uzyskać monit o kontynuowanie wdrażania.

* `New-AzResourceGroupDeployment -Confirm`dla wdrożeń grup zasobów
* `New-AzSubscriptionDeployment -Confirm`i `New-AzDeployment -Confirm` dla wdrożeń na poziomie subskrypcji

Poprzednie polecenia zwracają podsumowanie tekstu, które można ręcznie sprawdzić. Aby uzyskać obiekt, który można programowo sprawdzić pod kątem zmian, należy użyć:

* `$results = Get-AzResourceGroupDeploymentWhatIf`dla wdrożeń grup zasobów
* `$results = Get-AzSubscriptionDeploymentWhatIf`lub `$results = Get-AzDeploymentWhatIf` dla wdrożeń na poziomie subskrypcji

> [!NOTE]
> Przed wydaniem wersji 2.0.1-alpha5 użyto `New-AzDeploymentWhatIf` tego polecenia. To polecenie zostało zastąpione `Get-AzResourceGroupDeploymentWhatIf`przez `Get-AzSubscriptionDeploymentWhatIf` polecenie `Get-AzDeploymentWhatIf`, i polecenia. Jeśli użyto wcześniejszej wersji, należy zaktualizować tę składnię. Parametr `-ScopeType` został usunięty.

### <a name="azure-rest-api"></a>Interfejs API REST platformy Azure

W przypadku interfejsu API REST należy użyć:

* [Wdrożenia — co zrobić w przypadku](/rest/api/resources/deployments/whatif) wdrożeń grup zasobów
* [Wdrożenia — co zrobić, jeśli w zakresie subskrypcji](/rest/api/resources/deployments/whatifatsubscriptionscope) dla wdrożeń na poziomie subskrypcji

## <a name="change-types"></a>Zmienianie typów

Operacja co do-jeśli zawiera listę sześciu różnych typów zmian:

- **Utwórz:** Zasób obecnie nie istnieje, ale jest zdefiniowany w szablonie. Zasób zostanie utworzony.

- **Usuń:** Ten typ zmiany ma zastosowanie tylko w przypadku korzystania [z trybu pełnego](deployment-modes.md) wdrożenia. Zasób istnieje, ale nie jest zdefiniowany w szablonie. W trybie kompletnym zasób zostanie usunięty. Tylko zasoby, które [obsługują usuwanie trybu pełnego](complete-mode-deletion.md) są uwzględnione w tym typie zmiany.

- **Ignoruj:** Zasób istnieje, ale nie jest zdefiniowany w szablonie. Zasób nie zostanie wdrożony ani zmodyfikowany.

- **NoChange:** Zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie ponownie rozmieszczony, ale właściwości zasobu nie ulegnie zmianie. Ten typ zmiany jest zwracany, `FullResourcePayloads`gdy [format wyników](#result-format) jest ustawiony na , który jest wartością domyślną.

- **Modyfikuj**: Zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie ponownie rozmieszczony, a właściwości zasobu ulegnie zmianie. Ten typ zmiany jest zwracany, `FullResourcePayloads`gdy [format wyników](#result-format) jest ustawiony na , który jest wartością domyślną.

- **Wdrażanie:** Zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie ponownie rozmieszczony. Właściwości zasobu mogą lub nie mogą ulec zmianie. Operacja zwraca ten typ zmiany, gdy nie ma wystarczającej ilości informacji, aby określić, czy wszystkie właściwości ulegnie zmianie. Ten warunek jest widoczny tylko wtedy, `ResourceIdOnly`gdy [format wynikowy](#result-format) jest ustawiony na .

## <a name="result-format"></a>Format wyniku

Można kontrolować poziom szczegółów, który jest zwracany o przewidywanych zmian. W poleceniach wdrażania`New-Az*Deployment`( ), użyj **parametru -WhatIfResultFormat.** W poleceniach obiektu programowego (`Get-Az*DeploymentWhatIf`) należy użyć parametru **ResultFormat.**

Ustaw parametr formatu na **FullResourcePayloads,** aby uzyskać listę zasobów, które zostaną zmienić i szczegóły dotyczące właściwości, które zostaną zmienić. Ustaw parametr **formatu resourceidonly,** aby uzyskać listę zasobów, które zostaną zmienić. Wartością domyślną jest **FullResourcePayloads**.  

Następujące wyniki pokazują dwa różne formaty wyjściowe:

- Pełne ładunki zasobów

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Tylko identyfikator zasobu

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Uruchamianie operacji co do jeśli

### <a name="set-up-environment"></a>Konfigurowanie środowiska

Aby zobaczyć, jak działa co jeśli, uruchamiajmy kilka testów. Najpierw wdrożyć [szablon, który tworzy sieć wirtualną](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Użyjesz tej sieci wirtualnej, aby przetestować sposób zgłaszania zmian przez co jeśli.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Modyfikacja testu

Po zakończeniu wdrażania można przystąpić do testowania operacji co jeśli. Tym razem wdrożyć [szablon, który zmienia sieć wirtualną](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Brakuje jednego oryginalnego znacznika, podsieć została usunięta, a prefiks adresu został zmieniony.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Wyjście co-jeśli wydaje się podobne do:

![Dane wyjściowe operacji usługi Resource Manager, które można wykonać, jeśli](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Dane wyjściowe tekstu to:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Uwaga w górnej części danych wyjściowych, że kolory są zdefiniowane w celu wskazania typu zmian.

U dołu danych wyjściowych zostanie usunięty tag Owner. Prefiks adresu został zmieniony z 10.0.0.0/16 na 10.0.0.0/15. Usunięto podsieć o nazwie podsieć001. Pamiętaj, że te zmiany nie zostały faktycznie wdrożone. Zostanie wyświetlony podgląd zmian, które nastąpią po wdrożeniu szablonu.

Niektóre właściwości, które są wymienione jako usunięte faktycznie nie zmieni. Właściwości mogą być niepoprawnie zgłaszane jako usunięte, gdy nie są one w szablonie, ale są automatycznie ustawiane podczas wdrażania jako wartości domyślne. Wynik ten jest uważany za "hałas" w odpowiedzi co jeśli. Ostateczny wdrożony zasób będzie miał ustawione wartości właściwości. W miarę dojrzewania operacji co jeśli te właściwości zostaną odfiltrowane z wyniku.

## <a name="programmatically-evaluate-what-if-results"></a>Programowo oceniaj wyniki, które

Teraz programowo oceńmy wyniki warunkowe, ustawiając polecenie na zmienną.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Możesz zobaczyć podsumowanie każdej zmiany.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Potwierdzenie usunięcia

Operacja co-jeśli obsługuje przy użyciu [trybu wdrażania](deployment-modes.md). Po ustawieniu trybu ukończenia zasoby, których nie ma w szablonie, są usuwane. W poniższym przykładzie wdraża [szablon, który nie ma zasobów zdefiniowanych](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) w trybie kompletnym.

Aby wyświetlić podgląd zmian przed `-Confirm` wdrożeniem szablonu, użyj parametru przełącznika z poleceniem wdrażania. Jeśli zmiany są zgodnie z oczekiwaniami, upewnij się, że chcesz, aby wdrożenie zostało ukończone.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Ponieważ w szablonie nie zdefiniowano żadnych zasobów, a tryb wdrażania jest ustawiony na ukończenie, sieć wirtualna zostanie usunięta.

![Zakończenie trybu wdrażania szablonu menedżera zasobów co jeśli jest w trybie wdrażania wyjściowego](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Dane wyjściowe tekstu to:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Zostaną wyświetlają oczekiwane zmiany i można potwierdzić, że chcesz wdrożenia do uruchomienia.

## <a name="next-steps"></a>Następne kroki

- Jeśli zauważysz nieprawidłowe wyniki z wersji zapoznawczej [https://aka.ms/whatifissues](https://aka.ms/whatifissues)co-jeśli, zgłoś problemy w pliku .
- Aby wdrożyć szablony za pomocą programu Azure PowerShell, zobacz [Wdrażanie zasobów za pomocą szablonów ARM i programu Azure PowerShell](deploy-powershell.md).
- Aby wdrożyć szablony z pomocą REST, zobacz [Wdrażanie zasobów za pomocą szablonów ARM i interfejsu API REST Menedżera zasobów](deploy-rest.md).
