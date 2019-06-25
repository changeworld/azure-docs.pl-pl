---
title: Tryby wdrażania usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Opisuje sposób określenia, czy ma być używany tryb pełną lub przyrostową wdrożenia za pomocą usługi Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: 1ff098bf329979d0702c41f83d8e5f8ee7cceca1
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206551"
---
# <a name="azure-resource-manager-deployment-modes"></a>Tryby wdrażania usługi Azure Resource Manager

Podczas wdrażania zasobów, należy określić, że wdrożenie jest aktualizację przyrostową lub ukończenia aktualizacji.  Główną różnicą między tymi dwoma trybami jest sposób obsługiwania przez istniejące zasoby w grupie zasobów, które nie są w szablonie usługi Resource Manager. Domyślny tryb jest przyrostowy.

Dla obu trybów usługi Resource Manager spróbuje utworzyć wszystkie zasoby, które określono w szablonie. Jeśli zasób już istnieje w grupie zasobów i jego ustawienia są bez zmian, żadna operacja nie zostanie podjęte dla tego zasobu. Po zmianie wartości właściwości zasobu, zasób jest aktualizowana o te nowe wartości. Jeśli próbujesz zaktualizować lokalizację i typ istniejącego zasobu, wdrożenie zakończy się niepowodzeniem z powodu błędu. Zamiast tego należy wdrożyć nowy zasób z lokalizacją lub typu, należy.

## <a name="complete-mode"></a>W trybie

W trybie, Menedżer zasobów **usuwa** zasoby, które istnieją w grupie zasobów, ale nie są określone w szablonie. Zasoby, które są określone w szablonie, ale nie wdrożona, ponieważ [warunek](resource-group-authoring-templates.md#condition) zwróci wartość false, nie są usuwane.

Istnieją pewne różnice w sposób obsługi przez typy zasobów w trybie usunięcia. Nadrzędny zasoby są automatycznie usuwane, gdy nie znajduje się w szablonie, które jest wdrożone w trybie. Niektóre zasoby podrzędne nie są automatycznie usuwane po nie w szablonie. Jednak te zasoby podrzędne są usuwane po usunięciu zasobu nadrzędnego. 

Na przykład jeśli dana grupa zasobów zawiera strefę DNS (typ zasobu Microsoft.Network/dnsZones) i rekord CNAME (typ zasobu Microsoft.Network/dnsZones/CNAME), strefa DNS jest zasobem nadrzędnym dla rekordu CNAME. Jeśli wdrażanie przy użyciu tryb pełny i nie uwzględniają stref DNS w szablonie, rekordu CNAME i strefy DNS są zarówno usuwane. Jeśli dołączysz strefy DNS w szablonie, ale nie zawierają rekord CNAME, rekord CNAME nie jest usuwany. 

Aby uzyskać listę sposób typów zasobów obsługi usuwania, zobacz [zasobów usunięcia platformy Azure w przypadku wdrożeń w trybie](complete-mode-deletion.md).

Jeśli grupa zasobów jest [zablokowane](resource-group-lock-resources.md), tryb pełny nie powoduje usunięcia zasobów.

> [!NOTE]
> Tylko szablony z katalogu głównego obsługują tryb całego procesu wdrażania. Aby uzyskać [połączonych lub zagnieżdżonych szablonów](resource-group-linked-templates.md), należy użyć trybu przyrostowego. 
>
> [Subskrypcja wdrożeń poziomu](deploy-to-subscription.md) nie obsługują w trybie.
>
> Obecnie portal nie obsługuje trybu ukończone.
>

## <a name="incremental-mode"></a>Tryb przyrostowy

W trybie przyrostowym, Menedżer zasobów **pozostawia niezmienione** zasoby, które istnieją w grupie zasobów, ale nie są określone w szablonie.

Jednak w przypadku ponownego wdrażania istniejącego zasobu w trybie przyrostowym, wynik jest inny. Określ wszystkie właściwości zasobu, a nie tylko te, które aktualizujesz. Typowe nieporozumienia jest myśleć, właściwości, które nie zostały określone są pozostawione bez zmian. Jeśli nie określisz niektórych właściwości usługi Resource Manager interpretuje update jako zastąpienie tych wartości.

## <a name="example-result"></a>Przykład wyniku

Aby zilustrować różnica między trybami przyrostowe i kompletne, rozważmy następujący scenariusz.

**Grupa zasobów** zawiera:

* Zasobu A
* Zasób B
* Zasób języka C

**Szablon** zawiera:

* Zasobu A
* Zasób B
* Zasób, D

Po wdrożeniu w **przyrostowe** tryb, grupa zasobów ma:

* Zasobu A
* Zasób B
* Zasób języka C
* Zasób, D

Po wdrożeniu w **pełną** trybie C zasób zostanie usunięty. Grupa zasobów zawiera:

* Zasobu A
* Zasób B
* Zasób, D

## <a name="set-deployment-mode"></a>Ustaw tryb wdrożenia

Aby ustawić tryb wdrożenia, w przypadku wdrażania przy użyciu programu PowerShell, użyj `Mode` parametru.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Aby ustawić tryb wdrożenia, w przypadku wdrażania przy użyciu wiersza polecenia platformy Azure, użyj `mode` parametru.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Poniższy przykład przedstawia połączony szablon ustawiony tryb wdrożenie przyrostowe:

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat tworzenia szablonów usługi Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby dowiedzieć się więcej na temat wdrażania zasobów, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).
* Aby wyświetlić operacje dla dostawcy zasobów, zobacz [interfejsu API REST usługi Azure](/rest/api/).
