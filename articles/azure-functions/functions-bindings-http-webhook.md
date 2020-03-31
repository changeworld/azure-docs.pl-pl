---
title: Wyzwalacze i powiązania HTTP usługi Azure Functions
description: Dowiedz się, jak używać wyzwalaczy i powiązań HTTP w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462109"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Omówienie wyzwalaczy i powiązań HTTP usług Http usług Azure Functions

Usługi Azure Functions mogą być wywoływane za pośrednictwem żądań HTTP do tworzenia interfejsów API bezserwerowych i odpowiadania na [programy webhook.](https://en.wikipedia.org/wiki/Webhook)

| Akcja | Typ |
|---------|---------|
| Uruchamianie funkcji z żądania HTTP | [Wyzwalacz](./functions-bindings-http-webhook-trigger.md) |
| Zwracanie odpowiedzi HTTP z funkcji |[Oprawa wyjściowa](./functions-bindings-http-webhook-output.md) |

Kod w tym artykule domyślnie ma być składnia .NET Core, używana w programach Functions w wersji 2.x lub nowszej. Aby uzyskać informacje na temat składni 1.x, zobacz [szablony funkcji 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

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
[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Aktualizowanie rozszerzeń]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Funkcje 1.x aplikacje automatycznie mają odwołanie do pakietu [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, wersja 2.x.

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji z żądania HTTP](./functions-bindings-http-webhook-trigger.md)
- [Zwracanie odpowiedzi HTTP z funkcji](./functions-bindings-http-webhook-output.md)
