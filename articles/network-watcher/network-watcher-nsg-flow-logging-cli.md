---
title: Zarządzanie dziennikami przepływu grupy zabezpieczeń sieci z obserwatora sieciowego Azure - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Ta strona wyjaśnia sposób zarządzania przepływem grupy zabezpieczeń sieci dzienniki w obserwatora sieciowego Azure z wiersza polecenia platformy Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fa90d406313d756f3dd852305a6d61db29815192
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurowanie dzienników przepływu grupy zabezpieczeń sieci przy użyciu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowej grupy zabezpieczeń są funkcją obserwatora sieciowego, który służy do wyświetlania informacji na temat przychodzące i wychodzące ruchu IP za pośrednictwem grupy zabezpieczeń sieci. Te dzienniki przepływu są zapisywane w formacie json i Pokaż przepływów wychodzącego i przychodzącego na podstawie reguły w poszczególnych kart przepływ dotyczy 5-elementowej informacji o przepływie (źródłowego i docelowego adresu IP, portu źródłowego i docelowego Protocol), i jeśli ruch został dozwolony lub niedozwolony.

W tym artykule wykorzystano naszej nowej generacji interfejsu wiersza polecenia model wdrażania zarządzania zasobów, Azure CLI 2.0, który jest dostępny dla systemu Windows, Mac i Linux.

Aby wykonać kroki opisane w tym artykule, należy [instalowanie interfejsu wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="register-insights-provider"></a>Zarejestruj dostawcę usługi Insights

Aby rejestrowanie działało poprawnie, przepływ **elemencie Microsoft.Insights** dostawcy musi być zarejestrowana. Jeśli nie masz pewności Jeśli **elemencie Microsoft.Insights** dostawca został zarejestrowany, uruchom następujący skrypt.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Dzienniki przepływu włączyć grupy zabezpieczeń sieci

Polecenie, aby umożliwić przepływ dzienników przedstawiono w poniższym przykładzie:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
```

Konta magazynu, które określisz nie może mieć zasady sieci skonfigurowane pod jego kątem ograniczenia dostępu sieciowego tylko do usług firmy Microsoft lub określonych sieci wirtualnych. Konto magazynu może być w tej samej lub innej subskrypcji platformy Azure, niż NSG, umożliwiające przepływ w dzienniku. Jeśli używasz różnych subskrypcji, oba muszą być skojarzone z tej samej dzierżawy usługi Azure Active Directory. Konto używane dla każdej subskrypcji musi mieć [niezbędne uprawnienia](required-rbac-permissions.md).

## <a name="disable-network-security-group-flow-logs"></a>Dzienniki przepływu wyłączyć grupy zabezpieczeń sieci

Skorzystaj z następującego przykładu, aby wyłączyć przepływ dzienników:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Pobierz dziennik przepływu

Lokalizacja magazynu dziennika przepływu jest definiowany podczas tworzenia. Wygodne narzędzie dostępu do tych dzienników przepływu na koncie magazynu jest Microsoft Azure Eksploratora usługi Storage, który można pobrać tutaj:  http://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Informacje o strukturze dziennika można znaleźć [dziennika przepływu grupy zabezpieczeń sieci — omówienie](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizacji dzienników przepływu grupy NSG z usługą Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizacji NSG przepływu dzienników przy użyciu narzędzi typu open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
