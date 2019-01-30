---
title: Tworzenie szablonów usługi Azure Stack | Dokumentacja firmy Microsoft
description: Poznaj najlepsze rozwiązania dla szablonu usługi Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: b71fd64692f564c693ced48ca19afd1f5f2d0179
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238809"
---
# <a name="azure-resource-manager-template-considerations"></a>Zagadnienia dotyczące szablonów usługi Azure Resource Manager

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Podczas opracowywania aplikacji, jest ważne, aby zapewnić Przenoszalność szablonu między platformą Azure i usługi Azure Stack. Ten artykuł zawiera informacje dotyczące tworzenia usługi Azure Resource Manager [szablony](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), dzięki czemu możesz prototypu w Twojej aplikacji i przetestuj wdrożenie na platformie Azure bez dostępu do środowiska usługi Azure Stack.

## <a name="resource-provider-availability"></a>Dostępność dostawcy zasobów

Szablon, który planujesz wdrożyć należy używać tylko usług Microsoft Azure, które są już dostępne lub w wersji zapoznawczej w usłudze Azure Stack.

## <a name="public-namespaces"></a>Publiczne obszary nazw

Ponieważ usługi Azure Stack znajduje się w centrum danych, ma przestrzenie nazw punktu końcowego innej usługi niż chmury publicznej platformy Azure. W rezultacie ustaloną publiczne punkty końcowe w szablonach usługi Azure Resource Manager zakończyć się niepowodzeniem przy próbie wdróż je w usłudze Azure Stack. Można tworzyć dynamicznie przy użyciu punktów końcowych usługi `reference` i `concatenate` funkcji można pobrać wartości od dostawcy zasobów podczas wdrażania. Na przykład zamiast kodować *blob.core.windows.net* w szablonie, należy pobrać [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) aby dynamicznie ustawić *osDisk.URI* punktu końcowego:

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>Obsługa wersji interfejsu API

Wersje usługi platformy Azure mogą się różnić między platformą Azure i usługi Azure Stack. Każdy zasób wymaga **apiVersion** atrybut, który definiuje możliwości oferowane. Aby zapewnić zgodność wersji interfejsu API w usłudze Azure Stack, następujących wersji interfejsu API są prawidłowe dla każdego dostawcy zasobów:

| Dostawca zasobów | apiVersion |
| --- | --- |
| Wystąpienia obliczeniowe |`'2015-06-15'` |
| Sieć |`'2015-06-15'`, `'2015-05-01-preview'` |
| Magazyn |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| App Service |`'2015-08-01'` |

## <a name="template-functions"></a>Funkcje szablonów

Usługa Azure Resource Manager [funkcje](../../azure-resource-manager/resource-group-template-functions.md) możliwości są wymagane do tworzenia dynamicznych szablonów. Na przykład program funkcje zadań, takich jak:

* Łączenie lub przycinanie ciągów.
* Odwołuje się do wartości, z poziomu innych zasobów.
* Iteracja na zasoby do wdrożenia w wielu wystąpieniach.

Te funkcje nie są dostępne w usłudze Azure Stack:

* Pomiń
* Wypełnij

## <a name="resource-location"></a>Lokalizacja zasobu

Użyj szablonów usługi Azure Resource Manager `location` atrybutu, aby umieścić zasoby podczas wdrażania. Na platformie Azure lokalizacje dotyczą regionu, takich jak zachodnie stany USA lub Ameryki Południowej. W usłudze Azure Stack lokalizacje są różne, ponieważ usługa Azure Stack znajduje się w centrum danych. Aby upewnić się, że szablony są niezbywalne między platformą Azure i usługi Azure Stack, powinny odwoływać lokalizację grupy zasobów, podczas wdrażania poszczególnych zasobów. Można to zrobić za pomocą `[resourceGroup().Location]` aby upewnić się, wszystkie zasoby dziedziczą lokalizację grupy zasobów. Poniższy kod jest przykładem korzystania z tej funkcji podczas wdrażania konta magazynu:

```json
"resources": [
{
  "name": "[variables('storageAccountName')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "[variables('apiVersionStorage')]",
  "location": "[resourceGroup().location]",
  "comments": "This storage account is used to store the VM disks",
  "properties": {
  "accountType": "Standard_GRS"
  }
}
]
```

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie szablonów za pomocą programu PowerShell](azure-stack-deploy-template-powershell.md)
* [Wdrażanie szablonów za pomocą wiersza polecenia platformy Azure](azure-stack-deploy-template-command-line.md)
* [Wdrażanie szablonów za pomocą programu Visual Studio](azure-stack-deploy-template-visual-studio.md)
