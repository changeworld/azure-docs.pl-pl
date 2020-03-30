---
title: Skalowanie usługi Azure Cosmos DB zgodnie z harmonogramem przy użyciu czasomierza usługi Azure Functions
description: Dowiedz się, jak skalować zmiany przepływności w usłudze Azure Cosmos DB przy użyciu programów PowerShell i usługi Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75935170"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Skalowanie przepływności usługi Azure Cosmos DB przy użyciu wyzwalacza czasomierza usług Azure Functions

Wydajność konta usługi Azure Cosmos jest oparta na ilości aprowizowanej przepływności wyrażonej w jednostkach żądań na sekundę (RU/s). Inicjowanie obsługi administracyjnej jest na drugim ziarnistości i jest rozliczane na podstawie najwyższej ru/s na godzinę. Ten model aprowizowanej pojemności umożliwia usługi, aby zapewnić przewidywalną i spójną przepływność, gwarantowane małe opóźnienia i wysoką dostępność. Większość obciążeń produkcyjnych tych funkcji. Jednak w środowiskach deweloperskich i testowych, w których usługa Azure Cosmos DB jest używana tylko w godzinach pracy, można skalować przepływność w górę rano i skalować z powrotem w dół wieczorem po godzinach pracy.

Przepływność można ustawić za pomocą [szablonów usługi Azure Resource Manager](resource-manager-samples.md), interfejsu [wiersza polecenia platformy Azure](cli-samples.md)i programu [PowerShell](powershell-samples-sql.md), dla kont interfejsu API core (SQL) lub przy użyciu specyficznych dla danego języka zestawów SDK usługi Azure Cosmos DB. Zaletą korzystania z szablonów menedżera zasobów, interfejsu wiersza polecenia platformy Azure lub programu PowerShell jest to, że obsługują one wszystkie interfejsy API modelu usługi Azure Cosmos DB.

## <a name="throughput-scheduler-sample-project"></a>Przykładowy projekt harmonogramu przepływności

Aby uprościć proces skalowania usługi Azure Cosmos DB zgodnie z harmonogramem, utworzyliśmy przykładowy projekt o nazwie [Harmonogram przepływności usługi Azure Cosmos.](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) Ten projekt jest aplikacją usługi Azure Functions z dwoma wyzwalaczami czasomierza - "ScaleUpTrigger" i "ScaleDownTrigger". Wyzwalacze uruchamiają skrypt programu PowerShell, który ustawia przepływność każdego zasobu zgodnie z definicją `resources.json` w pliku w każdym wyzwalaczu. ScaleUpTrigger jest skonfigurowany do pracy z 8 AM UTC i ScaleDownTrigger jest skonfigurowany do pracy na `function.json` 6 PM UTC i te czasy mogą być łatwo aktualizowane w pliku dla każdego wyzwalacza.

Można sklonować ten projekt lokalnie, zmodyfikować go, aby określić zasoby usługi Azure Cosmos DB do skalowania w górę i w dół oraz harmonogram do uruchomienia. Później można wdrożyć go w subskrypcji platformy Azure i zabezpieczyć go przy użyciu tożsamości usługi zarządzanej z uprawnieniami [kontroli dostępu opartego na rolach](role-based-access-control.md) (RBAC) z rolą "Operator usługi Azure Cosmos DB", aby ustawić przepływność na kontach usługi Azure Cosmos.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej i pobierz próbkę z [harmonogramu przepływności usługi Azure Cosmos DB.](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)
