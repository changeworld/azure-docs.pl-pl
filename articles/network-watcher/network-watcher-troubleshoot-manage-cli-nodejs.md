---
title: Rozwiązywanie problemów z bramy sieci wirtualnej platformy Azure i połączenia - Azure CLI 1.0 | Dokumentacja firmy Microsoft
description: Ta strona wyjaśniono, jak użyć obserwatora sieciowego Azure Rozwiązywanie problemów z interfejsu wiersza polecenia platformy Azure w wersji 1.0
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 778b1c52720ad0583c57182d5d09db0d765be52f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli-10"></a>Rozwiązywanie problemów z Brama sieci wirtualnej i połączeń przy użyciu usługi Azure sieci obserwatora Azure CLI 1.0

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Program PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [Interfejs API REST](network-watcher-troubleshoot-manage-rest.md)

Obserwatora sieciowego zawiera wiele funkcji w odniesieniu do zrozumienia zasobów sieciowych na platformie Azure. Jeden z tych funkcji jest zasobem rozwiązywania problemów. Rozwiązywanie problemów z zasobów można wywołać za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Po wywołaniu, obserwatora sieciowego sprawdza kondycję Brama sieci wirtualnej lub połączenie i zwraca jego wyniki.

W tym artykule wykorzystano 1.0 interfejsu wiersza polecenia platformy Azure i platform, która jest dostępna dla systemu Windows, Mac i Linux. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego.

Lista odwiedziny typy obsługiwanych bramy [bramy obsługiwane typy](/network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Przegląd

Rozwiązywanie problemów z zasobów umożliwia rozwiązywanie problemów, które wynikają z bramy sieci wirtualnej i połączenia. Po wysłaniu żądania do zasobów dotyczących rozwiązywania problemów, dzienniki są proszeni i inspekcji. Po zakończeniu kontroli wyniki są zwracane. Żądania dotyczące rozwiązywania problemów są długotrwałe żądania zasobów, może to potrwać kilka minut do zwrócenia wyniku. Dzienniki na rozwiązywanie problemów z są przechowywane w kontenerze na koncie magazynu, który jest określony.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Pobieranie połączenia bramy sieci wirtualnej

W tym przykładzie Rozwiązywanie problemów z zasobów jest są uruchomione na połączenie. Można również przekazać go Brama sieci wirtualnej. Następujące polecenie cmdlet wyświetla listę połączeń sieci vpn w grupie zasobów.

```azurecli
azure network vpn-connection list -g resourceGroupName
```

Można również uruchomić polecenie, aby wyświetlić połączeń w ramach subskrypcji.

```azurecli
azure network vpn-connection list -s subscription
```

Po utworzeniu nazwę połączenia, można uruchomić to polecenie, aby pobrać jego identyfikator zasobu:

```azurecli
azure network vpn-connection show -g resourceGroupName -n connectionName
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Rozwiązywanie problemów z zasobów zwraca dane dotyczące kondycji zasobu, zapisuje dzienniki na konto magazynu, należy sprawdzić. W tym kroku utworzymy konta magazynu, jeśli istnieje już konto magazynu można go.

1. Tworzenie konta magazynu

    ```azurecli
    azure storage account create -n storageAccountName -l location -g resourceGroupName
    ```

1. Uzyskaj klucze konta magazynu

    ```azurecli
    azure storage account keys list storageAccountName -g resourcegroupName
    ```

1. Tworzenie kontenera

    ```azurecli
    azure storage container create --account-name storageAccountName -g resourcegroupName --account-key {storageAccountKey} --container logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Uruchom Rozwiązywanie problemów z zasobów obserwatora sieciowego

Rozwiązywanie problemów z zasobami za pomocą `network watcher troubleshoot` polecenia cmdlet. Jest przekazywana polecenia cmdlet grupy zasobów, nazwę obserwatora sieciowego, identyfikator połączenia, identyfikator konta magazynu i ścieżki do obiektu blob do przechowywania Rozwiązywanie problemów z wynikiem.

```azurecli
azure network watcher troubleshoot -g resourceGroupName -n networkWatcherName -t connectionId -i storageId -p storagePath
```

Po uruchomieniu polecenia cmdlet obserwatora sieciowego przegląda zasobów, aby sprawdzić kondycję. Zwraca wyniki do powłoki, a zapisuje dzienniki wyników w określone konto magazynu.

## <a name="understanding-the-results"></a>Opis wyników

Tekst akcji zawiera ogólne wskazówki na temat sposobu rozwiązania problemu. Jeśli dla problemu można podjąć akcję, link jest dostarczany z dodatkowych wskazówek. W przypadku których nie ma dodatkowych wskazówek, odpowiedź zawiera adres url, aby otworzyć sprawę pomocy technicznej.  Aby uzyskać więcej informacji na temat właściwości odpowiedzi i dostępnych odwiedź [Rozwiązywanie problemów z obserwatora sieciowego — omówienie](network-watcher-troubleshoot-overview.md)

Aby uzyskać instrukcje dotyczące pobierania plików z kontami magazynu azure, zapoznaj się [Rozpoczynanie pracy z magazynem obiektów Blob platformy Azure przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Kolejnym narzędziem, który może służyć jest Eksploratora usługi Storage. Więcej informacji na temat Eksploratora usługi Storage można znaleźć tutaj przy użyciu następującego łącza: [Eksploratora usługi Storage](http://storageexplorer.com/)

## <a name="next-steps"></a>Kolejne kroki

Jeśli ustawienia zostały zmienione które zatrzymania połączenia sieci VPN, zobacz [Zarządzaj grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md) śledzić reguły zabezpieczeń sieciowych grupy i zabezpieczeń, które mogą być zagrożona.
