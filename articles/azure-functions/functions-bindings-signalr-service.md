---
title: Powiązania usługi SignalR usługi azure functions
description: Dowiedz się, jak używać powiązań usługi SignalR z usługą Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523040"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Powiązania usługi SignalR Service dla usługi Azure Functions

Ten zestaw artykułów wyjaśnia, jak uwierzytelniać i wysyłać wiadomości w czasie rzeczywistym do klientów połączonych z [usługą Azure SignalR przy](https://azure.microsoft.com/services/signalr-service/) użyciu powiązań usługi SignalR w usłudze Azure Functions. Usługa Azure Functions obsługuje powiązania wejściowe i wyjściowe dla usługi SignalR Service.

| Akcja | Typ |
|---------|---------|
| Zwraca adres URL punktu końcowego usługi i token dostępu | [Powiązanie wejściowe](./functions-bindings-signalr-service-input.md) |
| Wysyłanie komunikatów usługi SignalR |[Oprawa wyjściowa](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji Functions

### <a name="functions-2x-and-higher"></a>Funkcje 2.x lub wyższe

Praca z wyzwalaczem i powiązaniami wymaga odwołania się do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzenia jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet]w wersji 3.x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzia platformy Azure] zaleca się używać z visual studio kodu. |
| Skrypt języka C# (tylko w trybie online w witrynie Azure portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązania bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Aktualizowanie rozszerzeń]: ./install-update-binding-extensions-manual.md
[Rozszerzenie narzędzia platformy Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Szczegółowe informacje na temat konfigurowania i używania usługi SignalR i usługi Azure Functions razem można znaleźć w [programie Azure Functions development and configuration with Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Biblioteka adnotacji (tylko Java)

Aby użyć adnotacji usługi SignalR w funkcjach Java, należy dodać zależność do artefaktu *azure-functions-java-library-signalr* (w wersji 1.0 lub nowszej) do pliku *pom.xml.*

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Następne kroki

- [Zwraca adres URL punktu końcowego usługi i token dostępu (powiązanie wejściowe)](./functions-bindings-signalr-service-input.md)
- [Wyślij komunikaty usługi SignalR (powiązanie wyjściowe)](./functions-bindings-signalr-service-output.md) 
