---
title: Przykład wdrażania skryptu interfejsu wiersza polecenia platformy Azure
description: Tworzenie bezpiecznego klastra systemu Linux usługi Service Fabric na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 397ae7ee3b5accbbac1bc17020e6c32397c105a2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592388"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Tworzenie bezpiecznego klastra systemu Linux usługi Service Fabric na platformie Azure

To polecenie tworzy certyfikat z podpisem własnym, dodaje go do magazynu kluczy i pobiera certyfikat lokalnie.  Nowy certyfikat służy do zabezpieczania klastra podczas wdrażania.  Zamiast tworzenia nowego certyfikatu można też użyć istniejącego.  W każdym razie nazwa podmiotu certyfikatu musi być zgodna z domeną używaną w celu uzyskiwania dostępu do klastra usługi Service Fabric. To dopasowanie jest wymagane, aby można było zapewnić protokół SSL dla punktów końcowych zarządzania protokołu HTTPS klastra i dla narzędzia Service Fabric Explorer. Nie można uzyskać z urzędu certyfikacji certyfikatu SSL dla domeny `.cloudapp.azure.com`. Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla danego klastra.

W razie potrzeby zainstaluj [interfejs wiersza polecenia platformy Azure](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, klaster i wszystkie powiązane zasoby.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az sf cluster create](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest) | Tworzy nowy klaster usługi Service Fabric.  |

## <a name="next-steps"></a>Następne kroki

Więcej przykładów dla interfejsu wiersza polecenia usługi Azure Service Fabric można znaleźć na stronie z [przykładami interfejsu wiersza polecenia usługi Service Fabric](../samples-cli.md).
