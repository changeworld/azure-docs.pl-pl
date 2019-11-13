---
title: Template deployment co zrobić (wersja zapoznawcza)
description: Przed wdrożeniem szablonu Azure Resource Manager Ustal, jakie zmiany będą miały miejsce w swoich zasobach.
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: jgao
ms.openlocfilehash: 84540543f2b809520ab56df5b3e098018fa0d76e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74015875"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Wdrożenie szablonu Menedżer zasobów operacji działania if (wersja zapoznawcza)

Przed wdrożeniem szablonu warto wyświetlić podgląd zmian, które będą miały miejsce. Azure Resource Manager zapewnia operację działania warunkowego, która pozwala zobaczyć, jak zasoby zmienią się w przypadku wdrożenia szablonu. Operacja działania warunkowego nie wprowadza żadnych zmian w istniejących zasobach. Zamiast tego przewiduje zmiany w przypadku wdrożenia określonego szablonu.

> [!NOTE]
> Operacja działania warunkowego jest obecnie w wersji zapoznawczej. Aby go użyć, musisz [utworzyć konto w wersji zapoznawczej](https://aka.ms/armtemplatepreviews). W wersji zapoznawczej wyniki mogą czasami wskazywać, że zasób ulegnie zmianie, gdy nie zostanie wykonana żadna zmiana. Pracujemy nad zmniejszeniem tych problemów, ale potrzebujemy pomocy. Zgłoś te problemy w [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues).

Operacji działania warunkowego można użyć z poleceniem programu `New-AzDeploymentWhatIf` PowerShell lub [What If](/rest/api/resources/deployments/whatif) operacji Rest.

W programie PowerShell dane wyjściowe wyglądają następująco:

![Wdrożenie szablonu Menedżer zasobów operacji fullresourcepayload i typów zmian](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>Zmień typy

Operacja działania warunkowego zawiera listę sześciu różnych typów zmian:

- **Utwórz**: zasób nie istnieje, ale jest zdefiniowany w szablonie. Zasób zostanie utworzony.

- **Usuń**: ten typ zmiany stosuje się tylko w przypadku korzystania z [trybu kompletnego](deployment-modes.md) dla wdrożenia. Zasób istnieje, ale nie jest zdefiniowany w szablonie. W przypadku trybu kompletnego zasób zostanie usunięty. Ten typ zmiany obejmuje tylko zasoby [obsługujące usuwanie w trybie pełnym](complete-mode-deletion.md) .

- **Ignoruj**: zasób istnieje, ale nie jest zdefiniowany w szablonie. Zasób nie zostanie wdrożony ani zmodyfikowany.

- **NOCHANGE**: zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie, ale właściwości zasobu nie ulegną zmianie. Ten typ zmiany jest zwracany, gdy [ResultFormat](#result-format) jest ustawiony na `FullResourcePayloads`, która jest wartością domyślną.

- **Modyfikacja**: zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie, a właściwości zasobu zmienią się. Ten typ zmiany jest zwracany, gdy [ResultFormat](#result-format) jest ustawiony na `FullResourcePayloads`, która jest wartością domyślną.

- **Wdróż**: zasób istnieje i jest zdefiniowany w szablonie. Zasób zostanie wdrożony ponownie. Właściwości zasobu mogą lub nie mogą się zmieniać. Operacja zwraca ten typ zmiany, jeśli nie ma wystarczających informacji, aby określić, czy zmiany zostaną zmienione. Ten warunek jest wyświetlany tylko wtedy, gdy [ResultFormat](#result-format) jest ustawiony na `ResourceIdOnly`.

## <a name="deployment-scope"></a>Zakres wdrożenia

Operacji działania warunkowego można użyć do wdrożeń na poziomie subskrypcji lub grupy zasobów. Należy ustawić zakres wdrożenia z parametrem `-ScopeType`. Akceptowane wartości to `Subscription` i `ResourceGroup`. W tym artykule przedstawiono wdrożenia grup zasobów.

Aby dowiedzieć się więcej o wdrożeniach na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md#).

## <a name="result-format"></a>Format wyniku

Można kontrolować poziom szczegółowości zwracanych informacji o przewidywanych zmianach. Ustaw parametr `ResultFormat` na `FullResourcePayloads`, aby uzyskać listę zasobów, które zmienią i szczegółowe informacje o właściwościach, które zostaną zmienione. Ustaw parametr `ResultFormat` na `ResourceIdOnly`, aby uzyskać listę zasobów, które zostaną zmienione. Wartość domyślna to `FullResourcePayloads`.  

Poniższe zrzuty ekranu pokazują dwa różne formaty danych wyjściowych:

- Pełne ładunki zasobów

    ![Wdrożenie szablonu Menedżer zasobów operacji fullresourcepayloads](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- Tylko identyfikator zasobu

    ![Wdrożenie szablonu Menedżer zasobów operacji resourceidonly](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>Uruchom operację wykonywania czynności

### <a name="set-up-environment"></a>Konfigurowanie środowiska

Aby zobaczyć, jak działa działanie, Uruchommy kilka testów. Najpierw Wdróż szablon z [szablonów szybkiego startu platformy Azure, które tworzą konto magazynu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json). Domyślny typ konta magazynu to `Standard_LRS`. To konto magazynu zostanie użyte do przetestowania, w jaki sposób zmiany są raportowane przez co to jest.

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>Modyfikacja testu

Po zakończeniu wdrażania możesz sprawdzić, czy operacja co należy wykonać. Uruchom polecenie co-IF, ale Zmień typ konta magazynu na `Standard_GRS`.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

Dane wyjściowe, które są podobne do:

![Wdrożenie szablonu Menedżer zasobów dane wyjściowe operacji](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

Zwróć uwagę na początku, że kolory są zdefiniowane, aby wskazać typ zmian.

W dolnej części danych wyjściowych zostanie wyświetlona nazwa jednostki SKU (typ konta magazynu) zostanie zmieniona z **Standard_LRS** na **Standard_GRS**.

### <a name="test-deletion"></a>Usuwanie testu

Operacja działania warunkowego obsługuje używanie [trybu wdrożenia](deployment-modes.md). Po ustawieniu na tryb kompletny zasoby, które nie znajdują się w szablonie, są usuwane. W poniższym przykładzie wdrożono [szablon, który nie zawiera żadnych zasobów zdefiniowanych](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) w trybie kompletnym.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Ponieważ żadne zasoby nie są zdefiniowane w szablonie, a Tryb wdrożenia jest ustawiony na ukończono, konto magazynu zostanie usunięte.

![Wdrożenie szablonu Menedżer zasobów wykonywanie trybu wdrożenia danych wyjściowych operacji](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Ważne jest, aby pamiętać, co oznacza, że nie są rzeczywiste zmiany. Konto magazynu nadal istnieje w grupie zasobów.

## <a name="next-steps"></a>Następne kroki

- Jeśli zauważysz nieprawidłowe wyniki z wersji zapoznawczej rozwiązania tego problemu, zgłoś problemy w [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues).
- Aby wdrożyć szablony z Azure PowerShell, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](resource-group-template-deploy.md).
- Aby wdrożyć szablony przy użyciu usługi REST, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Menedżer zasobów interfejsu API REST](resource-group-template-deploy-rest.md).
- Aby wrócić do pomyślnego wdrożenia, gdy wystąpi błąd, zobacz [wycofywanie po pomyślnym wdrożeniu](rollback-on-error.md).
