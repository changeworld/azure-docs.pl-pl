---
title: Jak używać Azure Cosmos DB ze źródłem zmian Azure Functions
description: Użyj Azure Cosmos DB źródła zmian z Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: db3f1b6657ae455ae049eaffd6758fc7e6944fb9
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001022"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architektury oparte na zdarzeniach bezserwerowych z Azure Cosmos DB i Azure Functions

Azure Functions zapewnia najprostszy sposób nawiązywania połączenia ze [źródłem zmian](change-feed.md). Można utworzyć małe Azure Functions reaktywne, które będą automatycznie wyzwalane dla każdego nowego zdarzenia w źródle zmian kontenera usługi Azure Cosmos.

![Funkcje oparte na zdarzeniach bezserwerowych działające z wyzwalaczem Azure Cosmos DB](./media/change-feed-functions/functions.png)

Korzystając z [wyzwalacza Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), można korzystać z funkcji skalowania i niezawodnego wykrywania zdarzeń przez [procesor kanału informacyjnego](./change-feed-processor.md), bez konieczności zachowywania [infrastruktury procesów roboczych](./change-feed-processor.md). Wystarczy skupić się na logice funkcji platformy Azure bez obaw o resztę potoku pozyskania zdarzeń. Można nawet mieszać wyzwalacz z innymi [powiązaniami Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Obecnie wyzwalacz Azure Cosmos DB jest obsługiwany tylko w przypadku interfejsu API Core (SQL).

## <a name="requirements"></a>Wymagania

Aby zaimplementować przepływ oparty na zdarzeniach bezserwerowych, potrzebne są:

* **Monitorowany kontener**: Monitorowany kontener jest monitorowanym kontenerem usługi Azure Cosmos i przechowuje dane, z których jest generowana podawanie zmian. Wszelkie operacje wstawiania i zmiany (np. CRUD) do monitorowanego kontenera są odzwierciedlane w kanale zmian kontenera.
* **Kontener dzierżawy**: Kontener dzierżawy przechowuje stan między wieloma i dynamicznymi wystąpieniami funkcji platformy Azure bez serwera i umożliwia dynamiczne skalowanie. Ten kontener dzierżawy może być ręcznie lub automatycznie tworzony przez Azure Cosmos DB Trigger.To automatycznie utworzyć kontener dzierżawy, w [konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)należy ustawić flagę *CreateLeaseCollectionIfNotExists* . Podzielone na partycje kontenery dzierżaw muszą mieć `/id` definicję klucza partycji.

## <a name="create-your-azure-cosmos-db-trigger"></a>Tworzenie wyzwalacza Azure Cosmos DB

Tworzenie funkcji platformy Azure z wyzwalaczem Azure Cosmos DB jest teraz obsługiwane dla wszystkich Azure Functions IDE i integracji z interfejsem wiersza polecenia:

* [Rozszerzenie programu Visual Studio](../azure-functions/functions-develop-vs.md) dla użytkowników programu Visual Studio.
* [Podstawowe rozszerzenie programu Visual Studio](https://code.visualstudio.com/tutorials/functions-extension/create-function) dla użytkowników Visual Studio Code.
* A wreszcie [podstawowe narzędzia interfejsu wiersza polecenia](../azure-functions/functions-run-local.md#create-func) dla wieloplatformowego środowiska IDE niezależny od.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Uruchamianie wyzwalacza Azure Cosmos DB lokalnie

[Funkcję platformy Azure](../azure-functions/functions-develop-local.md) można uruchamiać lokalnie za pomocą [emulatora Azure Cosmos DB](./local-emulator.md) , aby tworzyć i opracowywać przepływy oparte na zdarzeniach Bezserwerowych bez subskrypcji platformy Azure ani ponoszenia kosztów.

Jeśli chcesz przetestować scenariusze na żywo w chmurze, możesz [bezpłatnie Wypróbować Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez żadnej karty kredytowej lub subskrypcji platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz dalej dowiedzieć się więcej na temat źródła zmian w następujących artykułach:

* [Przegląd źródła zmian](change-feed.md)
* [Sposoby odczytywania źródła zmian](read-change-feed.md)
* [Za pomocą zmian źródła danych z biblioteką procesora](change-feed-processor.md)
* [Jak korzystać z biblioteki procesora źródła zmian](change-feed-processor.md)
* [Przetwarzanie baz danych bezserwerowe przy użyciu Azure Cosmos DB i Azure Functions](serverless-computing-database.md)
