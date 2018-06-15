---
title: Zarządzanie dziennikami przepływu grupy zabezpieczeń sieci z obserwatora sieciowego Azure - Azure CLI 1.0 | Dokumentacja firmy Microsoft
description: Ta strona wyjaśnia sposób zarządzania przepływem grupy zabezpieczeń sieci dzienniki w obserwatora sieciowego Azure z interfejsu wiersza polecenia platformy Azure w wersji 1.0
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
ms.openlocfilehash: 1cdef3454a326fbdbdef5ab51ac93cc8a558f34d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "26774492"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli-10"></a>Konfigurowanie dzienniki przepływu grupy zabezpieczeń sieci przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 1.0

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowej grupy zabezpieczeń są funkcją obserwatora sieciowego, który służy do wyświetlania informacji na temat przychodzące i wychodzące ruchu IP za pośrednictwem grupy zabezpieczeń sieci. Te dzienniki przepływu są zapisywane w formacie json i Pokaż przepływów wychodzącego i przychodzącego na podstawie reguły w poszczególnych kart przepływ dotyczy 5-elementowej informacji o przepływie (źródłowego i docelowego adresu IP, portu źródłowego i docelowego Protocol), i jeśli ruch został dozwolony lub niedozwolony.

W tym artykule wykorzystano 1.0 interfejsu wiersza polecenia platformy Azure i platform, która jest dostępna dla systemu Windows, Mac i Linux. Obserwatora sieciowego aktualnie używa interfejsu wiersza polecenia platformy Azure w wersji 1.0 do obsługi interfejsu wiersza polecenia.

## <a name="register-insights-provider"></a>Zarejestruj dostawcę usługi Insights

Aby rejestrowanie działało poprawnie, przepływ **elemencie Microsoft.Insights** dostawcy musi być zarejestrowana. Jeśli nie masz pewności Jeśli **elemencie Microsoft.Insights** dostawca został zarejestrowany, uruchom następujący skrypt.

```azurecli
azure provider register --namespace Microsoft.Insights --subscription <subscriptionid>
```

## <a name="enable-network-security-group-flow-logs"></a>Dzienniki przepływu włączyć grupy zabezpieczeń sieci

Polecenie, aby umożliwić przepływ dzienników przedstawiono w poniższym przykładzie:

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e true
```

## <a name="disable-network-security-group-flow-logs"></a>Dzienniki przepływu wyłączyć grupy zabezpieczeń sieci

Skorzystaj z następującego przykładu, aby wyłączyć przepływ dzienników:

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e false
```

## <a name="download-a-flow-log"></a>Pobierz dziennik przepływu

Lokalizacja magazynu dziennika przepływu jest definiowany podczas tworzenia. Wygodne narzędzie dostępu do tych dzienników przepływu na koncie magazynu jest Microsoft Azure Eksploratora usługi Storage, który można pobrać tutaj: http://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Informacje o strukturze dziennika można znaleźć [dziennika przepływu grupy zabezpieczeń sieci — omówienie](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizacji dzienników przepływu grupy NSG z usługą Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizacji NSG przepływu dzienników przy użyciu narzędzi typu open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
