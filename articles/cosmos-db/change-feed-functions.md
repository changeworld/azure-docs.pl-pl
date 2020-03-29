---
title: Jak korzystać z kanału informacyjnego usługi Azure Cosmos DB w usłudze Azure Functions
description: Użyj usługi Azure Functions, aby połączyć się z kanałem informacyjnym zmian usługi Azure Cosmos DB. Później można utworzyć reaktywne funkcje platformy Azure, które są wyzwalane przy każdym nowym zdarzeniu.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851369"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architektury oparte na zdarzeniach bezserwerowe z usługą Azure Cosmos DB i usługą Azure Functions

Usługa Azure Functions zapewnia najprostszy sposób łączenia się z [kanałem informacyjnym zmian.](change-feed.md) Można utworzyć małe reaktywne usługi Azure Functions, które zostaną automatycznie wyzwolone na każde nowe zdarzenie w pliku danych o zmianach kontenera usługi Azure Cosmos.

![Funkcje oparte na zdarzeniach bezserwerowe współpracujące z wyzwalaczem usługi Azure Functions dla usługi Cosmos DB](./media/change-feed-functions/functions.png)

Za pomocą [wyzwalacza usługi Azure Functions dla usługi Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)można korzystać ze skalowania i niezawodnej funkcji wykrywania zdarzeń [procesora kanału informacyjnego](./change-feed-processor.md)zmian bez konieczności utrzymywania [infrastruktury procesu roboczego.](./change-feed-processor.md) Po prostu skup się na logice funkcji platformy Azure, nie martwiąc się o pozostałą część potoku pozyskiwania zdarzeń. Wyzwalacz można nawet mieszać z innymi [powiązaniami usługi Azure Functions.](../azure-functions/functions-triggers-bindings.md#supported-bindings)

> [!NOTE]
> Obecnie wyzwalacz usług Azure Functions dla usługi Cosmos DB jest obsługiwany tylko do użytku z interfejsem API Core (SQL).

## <a name="requirements"></a>Wymagania

Aby zaimplementować przepływ oparty na zdarzeniach bezserwerowy, należy:

* **Monitorowany kontener:** Monitorowany kontener jest monitorowanym kontenerem usługi Azure Cosmos i przechowuje dane, z których jest generowany źródło danych zmian. Wszelkie wstawia, aktualizacje monitorowanego kontenera są odzwierciedlane w posuwu zmian kontenera.
* **Kontener dzierżawy:** kontener dzierżawy utrzymuje stan w wielu i dynamicznych wystąpieniach funkcji platformy Azure bez serwera i umożliwia skalowanie dynamiczne. Ten kontener dzierżawy można ręcznie lub automatycznie utworzyć za pomocą wyzwalacza usługi Azure Functions dla usługi Cosmos DB. Aby automatycznie utworzyć kontener dzierżawy, należy ustawić *createLeaseCollectionIfNotExists* flagi w [konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration). Kontenery dzierżawy podzielonej na `/id` partycje są wymagane do definicji klucza partycji.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Tworzenie wyzwalacza funkcji platformy Azure dla usługi Cosmos DB

Tworzenie funkcji platformy Azure za pomocą wyzwalacza usługi Azure Functions dla usługi Cosmos DB jest teraz obsługiwane we wszystkich integracjach IDE i interfejsu wiersza polecenia azure:

* [Rozszerzenie programu Visual Studio](../azure-functions/functions-develop-vs.md) dla użytkowników programu Visual Studio.
* [Rozszerzenie kodu programu Visual Studio](/azure/javascript/tutorial-vscode-serverless-node-01) dla użytkowników kodu programu Visual Studio.
* I wreszcie [podstawowe narzędzie CLI](../azure-functions/functions-run-local.md#create-func) dla wieloplatformowego środowiska niezależnego od IDE.

## <a name="run-your-trigger-locally"></a>Uruchom wyzwalacz lokalnie

Funkcję platformy Azure można uruchomić [lokalnie](../azure-functions/functions-develop-local.md) za pomocą [emulatora usługi Azure Cosmos DB,](./local-emulator.md) aby utworzyć i rozwinąć przepływy oparte na zdarzeniach bez użycia serwera bez subskrypcji platformy Azure lub ponoszenia żadnych kosztów.

Jeśli chcesz przetestować scenariusze na żywo w chmurze, możesz [wypróbować usługę Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) bez konieczności konieczności subskrypcji karty kredytowej lub platformy Azure.

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej o pliku danych o zmianach w następujących artykułach:

* [Omówienie kanału informacyjnego zmian](change-feed.md)
* [Sposoby czytania pliku danych o zmianach](read-change-feed.md)
* [Korzystanie z biblioteki procesora kanału informacyjnego zmian](change-feed-processor.md)
* [Jak pracować z biblioteką procesorów kanału informacyjnego zmian](change-feed-processor.md)
* [Przetwarzanie bezserwerowych baz danych przy użyciu usługi Azure Cosmos DB i usługi Azure Functions](serverless-computing-database.md)
