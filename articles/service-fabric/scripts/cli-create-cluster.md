---
title: Przykład wdrażania skryptu interfejsu wiersza polecenia platformy Azure
description: Jak utworzyć bezpieczny klaster linux sieci szkieletowej usług na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure (CLI).
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 2ef8f322ff17eeb5d75d3cc8e4f8604f02d4ef0e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366540"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Tworzenie bezpiecznego klastra systemu Linux usługi Service Fabric na platformie Azure

To polecenie tworzy certyfikat z podpisem własnym, dodaje go do magazynu kluczy i pobiera certyfikat lokalnie.  Nowy certyfikat służy do zabezpieczania klastra podczas wdrażania.  Zamiast tworzenia nowego certyfikatu można też użyć istniejącego.  W każdym razie nazwa podmiotu certyfikatu musi być zgodna z domeną używaną w celu uzyskiwania dostępu do klastra usługi Service Fabric. To dopasowanie jest wymagane do zapewnienia protokołu TLS dla punktów końcowych zarządzania HTTPS klastra i Eksploratora sieci szkieletowej usług. Nie można uzyskać certyfikatu TLS/SSL `.cloudapp.azure.com` od urzędu certyfikacji dla domeny. Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla danego klastra.

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
