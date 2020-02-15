---
title: Azure Functions wyzwalacze i powiązania HTTP
description: Dowiedz się, jak używać wyzwalaczy i powiązań HTTP w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: ebf0728184a5fc104e3e1e7d8a199fec328dbdc0
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210178"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Omówienie wyzwalaczy i powiązań HTTP Azure Functions

Azure Functions mogą być wywoływane przez żądania HTTP, aby kompilować bezserwerowe interfejsy API i odpowiadać na elementy [webhook](https://en.wikipedia.org/wiki/Webhook).

| Akcja | Typ |
|---------|---------|
| Uruchamianie funkcji z żądania HTTP | [Wyzwalacz](./functions-bindings-http-webhook-trigger.md) |
| Zwracanie odpowiedzi HTTP z funkcji |[Powiązanie danych wyjściowych](./functions-bindings-http-webhook-output.md) |

Kod w tym artykule jest domyślnie używany w przypadku składni .NET Core używanej w funkcjach w wersji 2. x i nowszych. Informacje na temat składni 1. x można znaleźć w [szablonach funkcji 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji funkcji

### <a name="functions-2x-and-higher"></a>Funkcje 2. x i nowsze

Praca z wyzwalaczem i powiązaniami wymaga odwołania do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas platformy .NET, podczas gdy rozszerzenie rozszerza wszystkie inne typy aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [Pakiet NuGet], wersja 3. x | |
| C#Skrypt, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzi platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) jest zalecane do użycia z Visual Studio Code. |
| C#Skrypt (tylko w trybie online w Azure Portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[core tools]: ./functions-run-local.md
[pakietu rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Aktualizowanie rozszerzeń]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Aplikacje Functions 1. x automatycznie mają odwołanie do pakietu NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x.

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji z żądania HTTP](./functions-bindings-http-webhook-trigger.md)
- [Zwracanie odpowiedzi HTTP z funkcji](./functions-bindings-http-webhook-output.md)
