---
title: Rozwiązywanie problemów z bramy sieci wirtualnej platformy Azure i połączenia — interfejs wiersza polecenia Azure | Dokumentacja firmy Microsoft
description: Tej stronie wyjaśniamy, jak używać usługi Azure Network Watcher Rozwiązywanie problemów z wiersza polecenia platformy Azure
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: 8910b4963c7f40b5b6ecdde06c7c7a0c9b86e97a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680339"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Rozwiązywanie problemów z bramy sieci wirtualnej i połączeń przy użyciu wiersza polecenia platformy Azure sieci obserwatora platformy Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Program PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-troubleshoot-manage-cli.md)
> - [Interfejs API REST](network-watcher-troubleshoot-manage-rest.md)

Usługa Network Watcher udostępnia wiele możliwości, w odniesieniu do zrozumienia zasobów sieciowych na platformie Azure. Jedną z tych funkcji jest zasobem rozwiązywania problemów. Rozwiązywanie problemów z zasobu może być wywoływany za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Po wywołaniu usługi Network Watcher sprawdza kondycję bramy sieci wirtualnej lub połączenie i zwraca jej ustaleń.

Aby wykonać kroki opisane w tym artykule, musisz [zainstalować interfejs wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto założenie, zostały już wykonane czynności opisane w [utworzyć usługę Network Watcher](network-watcher-create.md) utworzyć usługę Network Watcher.

Aby uzyskać listę typów obsługiwanych bramy, zobacz [typy bram obsługiwane](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Omówienie

Rozwiązywanie problemów z zasobów umożliwia rozwiązywanie problemów, które wynikają z bramy sieci wirtualnej i połączenia. Po wysłaniu żądania do zasobów dotyczących rozwiązywania problemów, dzienniki są wyszukiwane i inspekcji. Po ukończeniu inspekcji wyniki są zwracane. Zasób, który żądania dotyczące rozwiązywania problemów są długotrwałe żądania, może to potrwać kilka minut zwrócony wynik. Dzienniki z rozwiązywania problemów są przechowywane w kontenerze na koncie magazynu, który jest określony.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Pobieranie połączenia bramy sieci wirtualnej

W tym przykładzie Rozwiązywanie problemów z zasobu jest uruchomiono połączenia. Można również przekazać go bramy sieci wirtualnej. Następujące polecenie cmdlet wyświetla listę połączeń sieci vpn w grupie zasobów.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Po utworzeniu nazwę połączenia, można uruchomić to polecenie, aby uzyskać jego identyfikator zasobu:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Rozwiązywanie problemów z zasobów zwraca dane o kondycji zasobu, zapisuje dzienniki na koncie magazynu do przeglądu. W tym kroku utworzymy konto magazynu, jeśli istnieje już istniejące konto magazynu możesz użyć go.

1. Tworzenie konta magazynu

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Uzyskiwanie kluczy kont magazynu

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Tworzenie kontenera

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Uruchom Rozwiązywanie problemów z zasób usługi Network Watcher

Rozwiązywanie problemów z zasobami przy użyciu `az network watcher troubleshooting` polecenia cmdlet. Przekazujemy polecenia cmdlet w grupie zasobów nazwę usługi Network Watcher identyfikator połączenia, identyfikator konta magazynu i powoduje ścieżki do obiektu blob do przechowywania rozwiązywania problemów.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Po uruchomieniu polecenia cmdlet usługi Network Watcher przegląda na sprawdzenie kondycji zasobu. Jego zwraca wyniki do powłoki i dzienniki wyniki są przechowywane na koncie magazynu określonym.

## <a name="understanding-the-results"></a>Opis wyników

Tekst akcji zawiera ogólne wskazówki na temat sposobu rozwiązania problemu. Jeśli dla problemu można wykonać akcji, łącze jest dostarczana z dodatkowych wskazówek. W przypadku, w przypadku, gdy nie ma żadnych dodatkowych wskazówek, odpowiedź zawiera adres url, aby otworzyć zgłoszenie do pomocy technicznej.  Aby uzyskać więcej informacji na temat właściwości odpowiedzi i co jest zawarte w odwiedzić [rozwiązywania problemów z Network Watcher — omówienie](network-watcher-troubleshoot-overview.md)

Aby uzyskać instrukcje dotyczące pobierania plików z konta usługi azure storage, zapoznaj się [wprowadzenie do usługi Azure Blob storage przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Inne narzędzie, które mogą być używane jest Eksploratora usługi Storage. Więcej informacji na temat Eksploratora usługi Storage można znaleźć tutaj z łącza: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Kolejne kroki

Jeśli zmieniono ustawienia tego połączenia sieci VPN stop, zobacz [Zarządzanie sieciowymi grupami zabezpieczeń](../virtual-network/manage-network-security-group.md) ułatwiają śledzenie reguły zabezpieczeń sieci grupy i zabezpieczeń, które mogą być w danym.
