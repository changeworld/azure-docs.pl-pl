---
title: Powiązania usługi Azure Cosmos DB dla funkcji 2.x
description: Dowiedz się, jak używać wyzwalaczy i powiązań usługi Azure Cosmos DB w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605756"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Wyzwalacz i powiązania usługi Azure Cosmos DB dla usługi Azure Functions 2.x — omówienie

> [!div class="op_single_selector" title1="Wybierz wersję używanego środowiska wykonawczego usługi Azure Functions: "]
> * [Wersja 1](functions-bindings-cosmosdb.md)
> * [Wersja 2](functions-bindings-cosmosdb-v2.md)

Ten zestaw artykułów wyjaśnia, jak pracować z powiązaniami [usługi Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) w usłudze Azure Functions 2.x. Usługa Azure Functions obsługuje powiązania wyzwalania, wprowadzania i danych wyjściowych dla usługi Azure Cosmos DB.

| Akcja | Typ |
|---------|---------|
| Uruchamianie funkcji podczas tworzenia lub modyfikowanie dokumentu usługi Azure Cosmos DB | [Wyzwalacz](./functions-bindings-cosmosdb-v2-trigger.md) |
| Czytanie dokumentu usługi Azure Cosmos DB | [Powiązanie wejściowe](./functions-bindings-cosmosdb-v2-input.md) |
| Zapisywanie zmian w dokumencie usługi Azure Cosmos DB  |[Oprawa wyjściowa](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> To odwołanie dotyczy [usługi Azure Functions w wersji 2.x](functions-versions.md).  Aby uzyskać informacje dotyczące używania tych powiązań w usługach Functions 1.x, zobacz [Powiązania usługi Azure Cosmos DB dla usługi Azure Functions 1.x](functions-bindings-cosmosdb.md).
>
> To powiązanie pierwotnie nazwano DocumentDB. W funkcji w wersji 2.x wyzwalacz, powiązania i pakiet są o nazwie Cosmos DB.

## <a name="supported-apis"></a>Obsługiwane interfejsy API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji Functions

### <a name="functions-2x-and-higher"></a>Funkcje 2.x lub wyższe

Praca z wyzwalaczem i powiązaniami wymaga odwołania się do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzenia jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet]w wersji 3.x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzia platformy Azure] zaleca się używać z visual studio kodu. |
| Skrypt języka C# (tylko w trybie online w witrynie Azure portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązania bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Aktualizowanie rozszerzeń]: ./install-update-binding-extensions-manual.md
[Rozszerzenie narzędzia platformy Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Funkcje 1.x aplikacje automatycznie mają odwołanie do pakietu [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, wersja 2.x.

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji podczas tworzenia lub modyfikowanie dokumentu usługi Azure Cosmos DB (wyzwalacz)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Odczyt dokumentu usługi Azure Cosmos DB (powiązanie wejściowe)](./functions-bindings-cosmosdb-v2-input.md)
- [Zapisywanie zmian w dokumencie usługi Azure Cosmos DB (powiązanie danych wyjściowych)](./functions-bindings-cosmosdb-v2-output.md)
