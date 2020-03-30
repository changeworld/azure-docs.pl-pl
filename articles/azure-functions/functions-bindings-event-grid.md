---
title: Powiązania usługi Azure Event Grid dla funkcji platformy Azure
description: Dowiedz się, jak obsługiwać zdarzenia w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461083"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Powiązania usługi Azure Event Grid dla funkcji platformy Azure

W tym odwołaniu wyjaśniono, jak obsługiwać zdarzenia [w usłudze](../event-grid/overview.md) Azure Functions. Aby uzyskać szczegółowe informacje na temat obsługi wiadomości w siatce zdarzeń w punkcie końcowym HTTP, zobacz [Odbieranie zdarzeń do punktu końcowego HTTP](../event-grid/receive-events.md).

Usługa Event Grid to usługa platformy Azure, która wysyła żądania HTTP w celu powiadamiania o zdarzeniach, które mają miejsce w *wydawcach.* Wydawca jest usługą lub zasobem, który pochodzi ze zdarzenia. Na przykład konto magazynu obiektów blob platformy Azure jest wydawcą, [a przekazywanie lub usuwanie obiektu blob jest zdarzeniem.](../storage/blobs/storage-blob-event-overview.md) Niektóre [usługi platformy Azure mają wbudowaną obsługę publikowania zdarzeń w usłudze Event Grid.](../event-grid/overview.md#event-sources)

Programy obsługi zdarzeń *odbierają* i przetwarzają zdarzenia. Usługa Azure Functions jest jedną z kilku [usług platformy Azure, które mają wbudowaną obsługę obsługi zdarzeń w usłudze Event Grid.](../event-grid/overview.md#event-handlers) W tym odwołaniu nauczysz się używać wyzwalacza siatki zdarzeń do wywoływania funkcji po odebraniu zdarzenia z siatki zdarzeń i używania powiązania wyjściowego do wysyłania zdarzeń do [tematu niestandardowego siatki zdarzeń](../event-grid/post-to-custom-topic.md).

Jeśli wolisz, możesz użyć wyzwalacza HTTP do obsługi zdarzeń w siatce zdarzeń; zobacz [Odbieranie zdarzeń do punktu końcowego HTTP](../event-grid/receive-events.md). Obecnie nie można użyć wyzwalacza siatki zdarzeń dla aplikacji usługi Azure Functions, gdy zdarzenie jest dostarczane w [schemacie CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). Zamiast tego użyj wyzwalacza HTTP.

| Akcja | Typ |
|---------|---------|
| Uruchamianie funkcji podczas wywoływania zdarzenia Siatka zdarzeń | [Wyzwalacz](./functions-bindings-event-grid-trigger.md) |
| Wysyła zdarzenie siatki zdarzeń |[Oprawa wyjściowa](./functions-bindings-event-grid-output.md) |

Kod w tym odwołaniu domyślnie ma na celu składnię .NET Core, używaną w funkcjach w wersji 2.x lub nowszej. Aby uzyskać informacje na temat składni 1.x, zobacz [szablony funkcji 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

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
[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Aktualizowanie rozszerzeń]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Funkcje 1.x aplikacje automatycznie mają odwołanie do pakietu [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, wersja 2.x.

## <a name="next-steps"></a>Następne kroki
* [Uruchamianie funkcji podczas wywoływania zdarzenia Siatka zdarzeń](./functions-bindings-event-grid-trigger.md)
* [Wysyłanie zdarzenia w sieci zdarzeń](./functions-bindings-event-grid-trigger.md)
