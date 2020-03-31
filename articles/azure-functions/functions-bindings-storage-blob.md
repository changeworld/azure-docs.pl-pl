---
title: Wyzwalacz i powiązania magazynu obiektów Blob platformy Azure dla usług Azure Functions
description: Dowiedz się, jak używać wyzwalacza magazynu obiektów Blob platformy Azure i powiązań w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277234"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Omówienie powiązań magazynu obiektów Blob platformy Azure dla usługi Azure Functions

Usługa Azure Functions integruje się z [usługą Azure Storage](https://docs.microsoft.com/azure/storage/) za pomocą [wyzwalaczy i powiązań.](./functions-triggers-bindings.md) Integracja z magazynu obiektów Blob umożliwia tworzenie funkcji, które reagują na zmiany w danych obiektów blob, a także wartości odczytu i zapisu.

| Akcja | Typ |
|---------|---------|
| Uruchamianie funkcji w miarę wprowadzania zmian danych magazynu obiektów blob | [Wyzwalacz](./functions-bindings-storage-blob-trigger.md) |
| Odczytywanie danych magazynu obiektów blob w funkcji | [Powiązanie wejściowe](./functions-bindings-storage-blob-input.md) |
| Zezwalaj funkcji na zapisywanie danych magazynu obiektów blob |[Oprawa wyjściowa](./functions-bindings-storage-blob-output.md) |

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

- [Uruchamianie funkcji po zmianie danych magazynu obiektów blob](./functions-bindings-storage-blob-trigger.md)
- [Odczytywanie danych magazynu obiektów blob po uruchomieniu funkcji](./functions-bindings-storage-blob-input.md)
- [Zapisywanie danych magazynu obiektów blob z funkcji](./functions-bindings-storage-blob-output.md)
