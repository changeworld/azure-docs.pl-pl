---
title: Powiązania usługi Azure Service Bus dla funkcji platformy Azure
description: Dowiedz się, jak wysyłać wyzwalacze i powiązania usługi Azure Service Bus w usłudze Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277416"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Powiązania usługi Azure Service Bus dla funkcji platformy Azure

Usługa Azure Functions integruje się z [usługą Azure Service Bus](https://azure.microsoft.com/services/service-bus) za pomocą [wyzwalaczy i powiązań.](./functions-triggers-bindings.md) Integracja z usługą Service Bus umożliwia tworzenie funkcji, które reagują na i wysyłaj wiadomości kolejki lub tematu.

| Akcja | Typ |
|---------|---------|
| Uruchamianie funkcji podczas tworzenia kolejki lub komunikatu tematu usługi Service Bus | [Wyzwalacz](./functions-bindings-service-bus-trigger.md) |
| Wysyłanie komunikatów usługi Azure Service Bus |[Oprawa wyjściowa](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji Functions

### <a name="functions-2x-and-higher"></a>Funkcje 2.x lub wyższe

Praca z wyzwalaczem i powiązaniami wymaga odwołania się do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzenia jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet]w wersji 4.x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzia platformy Azure] zaleca się używać z visual studio kodu. |
| Skrypt języka C# (tylko w trybie online w witrynie Azure portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązania bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Aktualizowanie rozszerzeń]: ./install-update-binding-extensions-manual.md
[Rozszerzenie narzędzia platformy Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Funkcje 1.x aplikacje automatycznie mają odwołanie do pakietu [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, wersja 2.x.

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji podczas tworzenia kolejki lub komunikatu tematu usługi Service Bus (wyzwalacz)](./functions-bindings-service-bus-trigger.md)
- [Wysyłanie komunikatów usługi Azure Service Bus z usługi Azure Functions (powiązanie danych wyjściowych)](./functions-bindings-service-bus-output.md)
