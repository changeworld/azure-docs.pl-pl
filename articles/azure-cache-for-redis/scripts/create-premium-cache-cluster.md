---
title: Tworzenie pamięci podręcznej platformy Azure w warstwie Premium dla usługi Redis za pomocą klastrowania
description: Ten przykładowy kod interfejsu wiersza polecenia platformy Azure przedstawia sposób tworzenia pamięci podręcznej systemu Azure w warstwie Premium o pojemności 6 GB dla Redis z włączoną obsługą klastrów i dwoma fragmentów.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411147"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Tworzenie pamięci podręcznej Azure — wersja Premium dla usługi Redis z klastrowaniem

W tym scenariuszu dowiesz się, jak utworzyć 6 GB w warstwie Premium usługi Azure Cache dla pamięci podręcznej Redis przy włączonym klastrowaniu i dwóch fragmentów.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń, aby utworzyć grupę zasobów i włączyć pamięci podręcznej Azure redis Cache w warstwie Premium z usługą klastrowania. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Utwórz az redis](https://docs.microsoft.com/cli/azure/redis) | Tworzenie usługi Azure Cache dla wystąpienia usługi Redis. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe usługi Azure Cache dla przykładowych skryptów interfejsu wiersza polecenia platformy Redis można znaleźć w [usługi Azure Cache dokumentacja pamięci podręcznej Redis](../cli-samples.md).
