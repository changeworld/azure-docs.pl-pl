---
title: Tryby wdrażania
description: W tym artykule opisano, jak określić, czy w usłudze Azure Resource Manager ma być używany tryb wdrażania pełnego czy przyrostowego.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 1077d92f076797fb03c4fe750b353e2306f9b6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460249"
---
# <a name="azure-resource-manager-deployment-modes"></a>Tryby wdrażania usługi Azure Resource Manager

Podczas wdrażania zasobów, można określić, że wdrożenie jest aktualizacja przyrostowa lub pełną aktualizację. Różnica między tymi dwoma trybami polega na tym, jak Menedżer zasobów obsługuje istniejące zasoby w grupie zasobów, których nie ma w szablonie.

W przypadku obu trybów Menedżer zasobów próbuje utworzyć wszystkie zasoby określone w szablonie. Jeśli zasób już istnieje w grupie zasobów, a jego ustawienia pozostają niezmienione, nie jest podejmowana żadna operacja dla tego zasobu. Jeśli zmienisz wartości właściwości zasobu, zasób zostanie zaktualizowany o te nowe wartości. Jeśli spróbujesz zaktualizować lokalizację lub typ istniejącego zasobu, wdrożenie zakończy się niepowodzeniem z powodu błędu. Zamiast tego należy wdrożyć nowy zasób z lokalizacją lub typem, którego potrzebujesz.

Domyślny tryb jest przyrostowy.

## <a name="complete-mode"></a>Tryb kompletny

W trybie kompletnym Menedżer zasobów **usuwa** zasoby, które istnieją w grupie zasobów, ale nie są określone w szablonie.

Jeśli szablon zawiera zasób, który nie jest wdrażany, ponieważ [warunek](conditional-resource-deployment.md) ma wartość false, wynik zależy od wersji interfejsu API REST używane do wdrażania szablonu. Jeśli używasz wersji wcześniejszej niż 2019-05-10, zasób **nie zostanie usunięty**. W 2019-05-10 lub nowszym zasób **jest usuwany**. Najnowsze wersje programu Azure PowerShell i interfejsu wiersza polecenia platformy Azure usuwają zasób.

Należy zachować ostrożność podczas korzystania z trybu pełnego z [pętlami kopiowania](copy-resources.md). Wszystkie zasoby, które nie są określone w szablonie po rozwiązaniu pętli kopiowania są usuwane.

W przypadku wdrażania [w więcej niż jednej grupie zasobów w szablonie](cross-resource-group-deployment.md)zasoby w grupie zasobów określone w operacji wdrażania mogą zostać usunięte. Zasoby w dodatkowych grupach zasobów nie są usuwane.

Istnieją pewne różnice w sposobie, w jaki typy zasobów obsługują usunięcie trybu pełnego. Zasoby nadrzędne są automatycznie usuwane, gdy nie jest to szablon wdrożony w trybie kompletnym. Niektóre zasoby podrzędne nie są automatycznie usuwane, gdy nie ma ich w szablonie. Jednak te zasoby podrzędne są usuwane, jeśli zasób nadrzędny zostanie usunięty.

Jeśli na przykład grupa zasobów zawiera strefę DNS (typ zasobu Microsoft.Network/dnsZones) i rekord CNAME (typ zasobu Microsoft.Network/dnsZones/CNAME), strefa DNS jest zasobem nadrzędnym rekordu CNAME. Jeśli wdrożysz w trybie kompletnym i nie uwzględnisz strefy DNS w szablonie, strefa DNS i rekord CNAME zostaną usunięte. Jeśli dodajesz strefę DNS do szablonu, ale nie uwzględnisz rekordu CNAME, CNAME nie zostanie usunięte.

Aby uzyskać listę sposobu, w jaki typy zasobów obsługują usuwanie, zobacz [Usuwanie zasobów platformy Azure w celu wdrożenia w trybie kompletnym](complete-mode-deletion.md).

Jeśli grupa zasobów jest [zablokowana,](../management/lock-resources.md)tryb ukończenia nie powoduje usunięcia zasobów.

> [!NOTE]
> Tylko szablony na poziomie głównym obsługują tryb wdrażania całego. W przypadku [szablonów połączonych lub zagnieżdżonych](linked-templates.md)należy użyć trybu przyrostowego.
>
> [Wdrożenia na poziomie subskrypcji](deploy-to-subscription.md) nie obsługują trybu pełnego.
>
> Obecnie portal nie obsługuje trybu pełnego.
>

## <a name="incremental-mode"></a>Tryb przyrostowy

W trybie przyrostowym Menedżer zasobów **pozostawia niezmienione** zasoby, które istnieją w grupie zasobów, ale nie są określone w szablonie. Zasoby w szablonie **są dodawane** do grupy zasobów.

> [!NOTE]
> Podczas ponownego rozmieszczania istniejącego zasobu w trybie przyrostowym wszystkie właściwości są ponownie stosowane. **Właściwości nie są dodawane stopniowo**. Częstym nieporozumieniem jest myślenie, że właściwości, które nie są określone w szablonie, pozostają niezmienione. Jeśli nie określisz niektórych właściwości, Menedżer zasobów interpretuje wdrożenie jako zastąpienie tych wartości. Właściwości, które nie są uwzględnione w szablonie są resetowane do wartości domyślnych. Określ wszystkie wartości inne niż domyślne dla zasobu, a nie tylko te, które aktualizujesz. Definicja zasobu w szablonie zawsze zawiera ostateczny stan zasobu. Nie może reprezentować częściowej aktualizacji do istniejącego zasobu.

## <a name="example-result"></a>Przykładowy wynik

Aby zilustrować różnicę między trybami przyrostowymi i kompletnymi, należy wziąć pod uwagę następujący scenariusz.

**Grupa zasobów** zawiera:

* Zasób A
* Zasób B
* Zasób C

**Szablon** zawiera:

* Zasób A
* Zasób B
* Zasób D

Po wdrożeniu w trybie **przyrostowym** grupa zasobów ma:

* Zasób A
* Zasób B
* Zasób C
* Zasób D

Po wdrożeniu w trybie **kompletnym** zasób C jest usuwany. Grupa zasobów ma:

* Zasób A
* Zasób B
* Zasób D

## <a name="set-deployment-mode"></a>Ustawianie trybu wdrażania

Aby ustawić tryb wdrażania podczas wdrażania z programem PowerShell, należy użyć tego parametru. `Mode`

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Aby ustawić tryb wdrażania podczas wdrażania z `mode` platformą Azure CLI, należy użyć parametru.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

W poniższym przykładzie przedstawiono połączony szablon ustawiony na tryb wdrażania przyrostowego:

```json
"resources": [
  {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tworzeniu szablonów Usługi Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](template-syntax.md).
* Aby dowiedzieć się więcej o wdrażaniu zasobów, zobacz [Wdrażanie aplikacji za pomocą szablonu usługi Azure Resource Manager](deploy-powershell.md).
* Aby wyświetlić operacje dla dostawcy zasobów, zobacz [Interfejs API usługi Azure REST](/rest/api/).
