---
title: Tworzenie Centrum zdarzeń za pomocą grupy odbiorców — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Tworzenie przestrzeni nazw usługi Event Hubs z Centrum zdarzeń i grupy odbiorców za pomocą szablonów usługi Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/13/2019
ms.author: shvija
ms.openlocfilehash: c020a7673fe018565a6f1aeb9f7cb2124024a2c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118863"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Szybki start: Tworzenie Centrum zdarzeń za pomocą szablonu usługi Azure Resource Manager

Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym przewodniku Szybki Start utworzysz Centrum zdarzeń za pomocą [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Wdrażanie szablonu usługi Azure Resource Manager w celu utworzenia przestrzeni nazw typu [usługi Event Hubs](event-hubs-what-is-event-hubs.md), z jednym Centrum zdarzeń. W tym artykule opisano, jak zdefiniować zasoby, które są wdrażane i jak zdefiniować parametry, które są określone, gdy wdrożenie jest wykonywane. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb. Aby uzyskać informacje dotyczące tworzenia szablonów, zobacz [tworzenia usługi Azure Resource Manager][Authoring Azure Resource Manager templates]. Aby zapoznać się z właściwościami i składnią języka JSON, których należy użyć w szablonie, zobacz [Typy zasobów Microsoft.EventHub](/azure/templates/microsoft.eventhub/allversions).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

W tym przewodniku Szybki Start użyjesz [istniejący szablon usługi Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json). Aby uzyskać więcej przykładów szablonu, zobacz [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

Aby wdrożyć szablon:

1. Wybierz **wypróbuj** z następujący blok kodu i postępuj zgodnie z instrukcjami, aby zarejestrować się w usłudze Azure Cloud shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Może potrwać kilka minut, aby utworzyć Centrum zdarzeń.

1. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
1. Kliknij prawym przyciskiem myszy konsoli shell, a następnie wybierz **Wklej**.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Aby zweryfikować wdrożenie, możesz otworzyć grupy zasobów z [witryny Azure portal](https://portal.azure.com), lub użyj następującego skryptu programu Azure PowerShell.  Jeśli w usłudze Cloud shell jest wciąż otwarty, nie trzeba uruchomienia/kopiowania pierwszego wiersza (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. Jeśli w usłudze Cloud shell jest wciąż otwarty, nie trzeba uruchomienia/kopiowania pierwszego wiersza (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Kolejne kroki

W ramach tego artykułu utworzono przestrzeń nazw usługi Event Hubs oraz użyto aplikacji przykładowych do wysyłania zdarzeń do centrum zdarzeń i odbierania ich z niego. Aby uzyskać instrukcje krok po kroku wysyłania zdarzeń do (lub) odbieranie zdarzeń z Centrum zdarzeń, zobacz **wysyłania i odbierania zdarzeń** samouczków:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (tylko Wyślij)](event-hubs-c-getstarted-send.md)
- [Apache Storm (tylko reecive)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
