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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: d09dec2f327d8b5911a4e55832ba106838c7ebc3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057681"
---
# <a name="azure-resource-manager-template-considerations"></a>Zagadnienia dotyczące szablonów usługi Azure Resource Manager

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Podczas opracowywania aplikacji, jest ważne, aby zapewnić Przenoszalność szablonu między platformą Azure i usługi Azure Stack. Ten artykuł zawiera informacje dotyczące tworzenia usługi Azure Resource Manager [szablony](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), dzięki czemu możesz prototypu w Twojej aplikacji i przetestuj wdrożenie na platformie Azure bez dostępu do środowiska usługi Azure Stack.

## <a name="resource-provider-availability"></a>Dostępność dostawcy zasobów

Szablon, który zamierzasz wdrożyć należy używać tylko usług Microsoft Azure, które są już dostępne lub w wersji zapoznawczej w usłudze Azure Stack.

## <a name="public-namespaces"></a>Publiczne obszary nazw

Ponieważ usługi Azure Stack znajduje się w centrum danych, ma przestrzenie nazw punktu końcowego innej usługi niż chmury publicznej platformy Azure. W rezultacie zapisane na stałe publiczne punkty końcowe w szablonach usługi Azure Resource Manager zakończyć się niepowodzeniem podczas próby wdróż je w usłudze Azure Stack. Można tworzyć dynamicznie przy użyciu punktów końcowych usługi *odwołania* i *łączenie* funkcji można pobrać wartości od dostawcy zasobów podczas wdrażania. Na przykład zamiast hardcoding *blob.core.windows.net* w szablonie, należy pobrać [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) aby dynamicznie ustawić *osDisk.URI* punktu końcowego:

     "osDisk": {"name": "osdisk","vhd": {"uri":
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

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

Usługa Azure Resource Manager [funkcje](../../azure-resource-manager/resource-group-template-functions.md) zapewniają możliwości, które są wymagane do tworzenia dynamicznych szablonów. Na przykład można użyć funkcji zadań, takich jak:

* Łączenie lub przycinanie ciągów.
* Odwołuje się do wartości, z poziomu innych zasobów.
* Iteracja na zasoby do wdrożenia w wielu wystąpieniach.

Te funkcje nie są dostępne w usłudze Azure Stack:

* Pomiń
* Wypełnij

## <a name="resource-location"></a>Lokalizacja zasobu

Szablony usługi Azure Resource Manager umożliwia umieść zasoby podczas wdrażania przez atrybut lokalizacji. Na platformie Azure lokalizacje dotyczą regionu zachodnie stany USA lub Ameryki Południowej. W usłudze Azure Stack lokalizacje są różne, ponieważ usługa Azure Stack znajduje się w centrum danych. Aby upewnić się, że szablony są możliwej między platformą Azure i usługi Azure Stack, powinny odwoływać lokalizację grupy zasobów, podczas wdrażania poszczególnych zasobów. Można to zrobić za pomocą `[resourceGroup().Location]` aby upewnić się, wszystkie zasoby dziedziczą lokalizację grupy zasobów. Poniższy fragment jest przykładem korzystania z tej funkcji podczas wdrażania konta magazynu:

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

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie szablonów za pomocą programu PowerShell](azure-stack-deploy-template-powershell.md)
* [Wdrażanie szablonów za pomocą wiersza polecenia platformy Azure](azure-stack-deploy-template-command-line.md)
* [Wdrażanie szablonów za pomocą programu Visual Studio](azure-stack-deploy-template-visual-studio.md)
