---
title: Template deployment co zrobić (wersja zapoznawcza)
description: Przed wdrożeniem szablonu Azure Resource Manager Ustal, jakie zmiany będą miały miejsce w swoich zasobach.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388527"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Wdrożenie szablonu Menedżer zasobów operacji działania if (wersja zapoznawcza)

Przed wdrożeniem szablonu warto wyświetlić podgląd zmian, które będą miały miejsce. Azure Resource Manager zapewnia operację działania warunkowego, która pozwala zobaczyć, jak zasoby zmienią się w przypadku wdrożenia szablonu. Operacja działania warunkowego nie wprowadza żadnych zmian w istniejących zasobach. Zamiast tego przewiduje zmiany w przypadku wdrożenia określonego szablonu.

> [!NOTE]
> Operacja działania warunkowego jest obecnie w wersji zapoznawczej. Aby go użyć, musisz [utworzyć konto w wersji zapoznawczej](https://aka.ms/armtemplatepreviews). W wersji zapoznawczej wyniki mogą czasami wskazywać, że zasób ulegnie zmianie, gdy nie zostanie wykonana żadna zmiana. Pracujemy nad zmniejszeniem tych problemów, ale potrzebujemy pomocy. Zgłoś te problemy w [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Operacji działania warunkowego można użyć z poleceniami programu PowerShell lub operacjami interfejsu API REST.

W programie PowerShell dane wyjściowe zawierają wyniki kodowane kolorami, które pomagają zobaczyć różne typy zmian.

![Wdrożenie szablonu Menedżer zasobów operacji fullresourcepayload i typów zmian](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Tekst ouptput:

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

## <a name="what-if-commands"></a>Polecenia warunkowe

Do operacji działania warunkowego można użyć zarówno Azure PowerShell, jak i interfejsu API REST platformy Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Aby wyświetlić podgląd zmian przed wdrożeniem szablonu, należy dodać parametr przełącznika `-Whatif` do polecenia wdrożenia.

* `New-AzResourceGroupDeployment -Whatif` wdrożeń grup zasobów
* `New-AzSubscriptionDeployment -Whatif` i `New-AzDeployment -Whatif` dla wdrożeń na poziomie subskrypcji

Można też użyć parametru przełącznika `-Confirm`, aby wyświetlić podgląd zmian i uzyskać monit o kontynuowanie wdrożenia.

* `New-AzResourceGroupDeployment -Confirm` wdrożeń grup zasobów
* `New-AzSubscriptionDeployment -Confirm` i `New-AzDeployment -Confirm` dla wdrożeń na poziomie subskrypcji

Powyższe polecenia zwracają podsumowanie tekstu, które można ręcznie sprawdzić. Aby uzyskać obiekt, który można programowo sprawdzić pod kątem zmian, użyj:

* `$results = Get-AzResourceGroupDeploymentWhatIf` wdrożeń grup zasobów
* `$results = Get-AzSubscriptionDeploymentWhatIf` lub `$results = Get-AzDeploymentWhatIf` dla wdrożeń na poziomie subskrypcji

> [!NOTE]
> Przed wydaniem wersji 2.0.1-alpha5 użyto polecenia `New-AzDeploymentWhatIf`. To polecenie zostało zastąpione poleceniami `Get-AzDeploymentWhatIf`, `Get-AzResourceGroupDeploymentWhatIf`i `Get-AzSubscriptionDeploymentWhatIf`. Jeśli używasz wcześniejszej wersji, musisz ją zaktualizować. `-ScopeType` parametr został usunięty.

### <a name="azure-rest-api"></a>Interfejs API REST platformy Azure

W przypadku interfejsu API REST wykonaj następujące czynności:

* [Wdrożenia — What If](/rest/api/resources/deployments/whatif) do wdrożeń grup zasobów
* [Wdrożenia — What If w zakresie subskrypcji](/rest/api/resources/deployments/whatifatsubscriptionscope) dla wdrożeń na poziomie subskrypcji

## <a name="change-types"></a>Zmień typy

Operacja działania warunkowego zawiera listę sześciu różnych typów zmian:

- **Utwórz**: zasób nie istnieje, ale jest zdefiniowany w szablonie. Zasób zostanie utworzony.

- **Usuń**: ten typ zmiany stosuje się tylko w przypadku korzystania z [trybu kompletnego](deployment-modes.md) dla wdrożenia. Zasób istnieje, ale nie jest zdefiniowany w szablonie. W przypadku trybu kompletnego zasób zostanie usunięty. Ten typ zmiany obejmuje tylko zasoby [obsługujące usuwanie w trybie pełnym](complete-mode-deletion.md) .

- **Ignoruj**: zasób istnieje, ale nie jest zdefiniowany w szablonie. Zasób nie zostanie wdrożony ani zmodyfikowany.

- **NOCHANGE**: zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie, ale właściwości zasobu nie ulegną zmianie. Ten typ zmiany jest zwracany, gdy [ResultFormat](#result-format) jest ustawiony na `FullResourcePayloads`, która jest wartością domyślną.

- **Modyfikacja**: zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie, a właściwości zasobu zmienią się. Ten typ zmiany jest zwracany, gdy [ResultFormat](#result-format) jest ustawiony na `FullResourcePayloads`, która jest wartością domyślną.

- **Wdróż**: zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie. Właściwości zasobu mogą lub nie mogą się zmieniać. Operacja zwraca ten typ zmiany, jeśli nie ma wystarczających informacji, aby określić, czy zmiany zostaną zmienione. Ten warunek jest wyświetlany tylko wtedy, gdy [ResultFormat](#result-format) jest ustawiony na `ResourceIdOnly`.

## <a name="result-format"></a>Format wyniku

Można kontrolować poziom szczegółowości zwracanych informacji o przewidywanych zmianach. W poleceniach wdrażania (`New-Az*Deployment`) Użyj parametru **-WhatIfResultFormat** . W poleceniach obiektów programistycznych (`Get-Az*DeploymentWhatIf`) Użyj parametru **ResultFormat** .

Ustaw parametr format na **FullResourcePayloads** , aby uzyskać listę zasobów, które zostaną zmienione i szczegółowe informacje o właściwościach, które zostaną zmienione. Ustaw parametr format na **ResourceIdOnly** , aby uzyskać listę zasobów, które zostaną zmienione. Wartość domyślna to **FullResourcePayloads**.  

Poniższe Wyniki pokazują dwa różne formaty danych wyjściowych:

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

## <a name="run-what-if-operation"></a>Uruchom operację wykonywania czynności

### <a name="set-up-environment"></a>Konfigurowanie środowiska

Aby zobaczyć, jak działa działanie, Uruchommy kilka testów. Najpierw Wdróż [szablon, który tworzy sieć wirtualną](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Ta sieć wirtualna będzie używana do testowania, w jaki sposób zmiany są raportowane przez co to jest.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Modyfikacja testu

Po zakończeniu wdrażania możesz sprawdzić, czy operacja co należy wykonać. Ten czas wdraża [szablon, który zmienia sieć wirtualną](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Brak jednego z oryginalnych tagów, podsieć została usunięta, a prefiks adresu został zmieniony.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Dane wyjściowe, które są wyświetlane podobnie:

![Wdrożenie szablonu Menedżer zasobów dane wyjściowe operacji](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Tekst wyjściowy to:

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

Zwróć uwagę na początku, że kolory są zdefiniowane, aby wskazać typ zmian.

W dolnej części danych wyjściowych widać, że właściciel tagu został usunięty. Prefiks adresu został zmieniony z 10.0.0.0/16 na 10.0.0.0/15. Podsieć o nazwie subnet001 została usunięta. Pamiętaj, że zmiany nie zostały faktycznie wdrożone. Zobaczysz Podgląd zmian, które nastąpią w przypadku wdrożenia szablonu.

Niektóre z właściwości, które są wymienione jako usunięte, nie ulegną zmianie. Właściwości mogą być nieprawidłowo zgłaszane jako usunięte, jeśli nie znajdują się w szablonie, ale są automatycznie ustawiane podczas wdrażania jako wartości domyślne. Ten wynik jest uznawany za "szum" w odpowiedzi "co jeśli". Ostatecznie wdrożony zasób będzie miał wartości ustawione dla właściwości. Zgodnie z oczekiwaniami, te właściwości zostaną odfiltrowane z wyniku.

## <a name="programmatically-evaluate-what-if-results"></a>Programowo Oceń wyniki

Teraz można programowo oszacować wyniki działania warunkowego, ustawiając polecenie na zmienną.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Można zobaczyć podsumowanie każdej zmiany.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Potwierdzenie usunięcia

Operacja działania warunkowego obsługuje używanie [trybu wdrożenia](deployment-modes.md). Po ustawieniu na tryb kompletny zasoby, które nie znajdują się w szablonie, są usuwane. W poniższym przykładzie wdrożono [szablon, który nie zawiera żadnych zasobów zdefiniowanych](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) w trybie kompletnym.

Aby wyświetlić podgląd zmian przed wdrożeniem szablonu, użyj parametru przełącznika `-Confirm` z poleceniem Deployment. Jeśli zmiany są zgodnie z oczekiwaniami, potwierdź, że wdrożenie ma zostać ukończone.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Ponieważ żadne zasoby nie są zdefiniowane w szablonie, a Tryb wdrożenia jest ustawiony na ukończono, Sieć wirtualna zostanie usunięta.

![Wdrożenie szablonu Menedżer zasobów wykonywanie trybu wdrożenia danych wyjściowych operacji](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Tekst wyjściowy to:

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

Zobaczysz oczekiwane zmiany i można potwierdzić, że chcesz, aby wdrożenie zostało uruchomione.

## <a name="next-steps"></a>Następne kroki

- Jeśli zauważysz nieprawidłowe wyniki z wersji zapoznawczej rozwiązania tego problemu, zgłoś problemy w [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Aby wdrożyć szablony z Azure PowerShell, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](deploy-powershell.md).
- Aby wdrożyć szablony przy użyciu usługi REST, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Menedżer zasobów interfejsu API REST](deploy-rest.md).
