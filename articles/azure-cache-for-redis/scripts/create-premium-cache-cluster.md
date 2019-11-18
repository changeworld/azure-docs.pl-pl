---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie pamięci podręcznej platformy Azure w warstwie Premium dla usługi Redis
description: Skrypt interfejsu wiersza polecenia platformy Azure przykładowy — Tworzenie usługi Azure Cache w warstwie Premium dla usługi Redis z klastrowaniem
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 1813bf7112969436012627147b94f656537029d5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122493"
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
