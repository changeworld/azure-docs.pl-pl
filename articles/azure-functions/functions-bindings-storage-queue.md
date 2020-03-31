---
title: Usługa Azure Queue storage trigger and bindings for Azure Functions overview
description: Dowiedz się, jak używać powiązania wyzwalania i danych wyjściowych magazynu kolejki platformy Azure w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277312"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Usługa Azure Queue storage trigger and bindings for Azure Functions overview

Usługa Azure Functions może działać jako nowe komunikaty magazynu kolejki platformy Azure są tworzone i można zapisywać komunikaty kolejki w ramach funkcji.

| Akcja | Typ |
|---------|---------|
| Uruchamianie funkcji w miarę zmian danych magazynu kolejek | [Wyzwalacz](./functions-bindings-storage-queue-trigger.md) |
| Komunikaty magazynu kolejki zapisu |[Oprawa wyjściowa](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji Functions

### <a name="functions-2x-and-higher"></a>Funkcje 2.x lub wyższe

Praca z wyzwalaczem i powiązaniami wymaga odwołania się do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzenia jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet]w wersji 3.x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) zaleca się używać z visual studio kodu. |
| Skrypt języka C# (tylko w trybie online w witrynie Azure portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązania bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aktualizowanie rozszerzeń]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Funkcje 1.x aplikacje automatycznie mają odwołanie do pakietu [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, wersja 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji w miarę zmiany danych magazynu kolejek (wyzwalacz)](./functions-bindings-storage-queue-trigger.md)
- [Komunikaty magazynu kolejki zapisu (powiązanie wyjściowe)](./functions-bindings-storage-queue-output.md)
