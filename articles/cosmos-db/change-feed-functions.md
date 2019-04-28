---
title: Jak używać usługi Azure Cosmos DB zmiany źródła danych za pomocą usługi Azure Functions
description: Użyj usługi Azure Cosmos DB zmiany źródła danych za pomocą usługi Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 18780deba1910b3ea77f7313bfb6d204dacabd82
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112025"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architektury oparte na zdarzeniach bezserwerowe za pomocą usługi Azure Cosmos DB i Azure Functions

Usługa Azure Functions zapewnia Najprostszym sposobem, aby nawiązać połączenie [zestawienia zmian](change-feed.md). Małe reaktywne usługi Azure Functions wyzwalającego automatycznie można tworzyć na każde nowe zdarzenie w kontenerze usługi Azure Cosmos zmiany w źródle danych.

![Funkcje niewymagające użycia serwera opartego na zdarzeniach — Praca z wyzwalacza usługi Azure Cosmos DB](./media/change-feed-functions/functions.png)

Za pomocą [usługi Azure Cosmos DB wyzwalacza](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), można wykorzystać [procesora zestawienia zmian](./change-feed-processor.md)na skalowanie i zdarzeń niezawodne funkcje wykrywania bez konieczności przechowywania dowolnego [procesu roboczego Infrastruktura](./change-feed-processor.md#implementing-the-change-feed-processor-library). Po prostu skoncentrować się na logice funkcji platformy Azure bez martwienia się o pozostałego potoku określania źródła zdarzeń. Wyzwalacz można nawet łączyć z innymi [powiązań usługi Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Obecnie wyzwalacza usługi Azure Cosmos DB jest obsługiwane do użytku z podstawowej (SQL) interfejsu API tylko.

## <a name="requirements"></a>Wymagania

Aby zaimplementować przepływu bez użycia serwera opartego na zdarzeniach, potrzebne są:

* **Kontener monitorowanych**: Monitorowane kontener jest monitorowane kontenera usługi Azure Cosmos i przechowuje dane, z którego jest generowany zestawienia zmian. Wszelkie operacje wstawiania i zmiany (np. CRUD) w kontenerze monitorowane są odzwierciedlane w zestawienia zmian w kontenerze.
* **Kontener dzierżawy**: Kontener dzierżawy zachowuje stan między wieloma i dynamiczne bezserwerowej funkcji platformy Azure wystąpień i umożliwia dynamiczne skalowanie. Ten kontener dzierżawy mogą być ręcznie lub automatycznie tworzone przez usługi Azure Cosmos DB Trigger.To automatycznie utworzyć kontener dzierżawy, ustaw *CreateLeaseCollectionIfNotExists* znacznik w [konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Kontenery partycjonowane dzierżawy są wymagane do `/id` Definicja klucza partycji.

## <a name="create-your-azure-cosmos-db-trigger"></a>Tworzenie wyzwalacza usługi Azure Cosmos DB

Tworzenie funkcji platformy Azure za pomocą wyzwalacza usługi Azure Cosmos DB jest teraz obsługiwana we wszystkich funkcji platformy Azure w środowisku IDE i integracji interfejsu wiersza polecenia:

* [Rozszerzenie programu Visual Studio](../azure-functions/functions-develop-vs.md) dla użytkowników programu Visual Studio.
* [Rozszerzenie programu Visual Studio Core](https://code.visualstudio.com/tutorials/functions-extension/create-function) dla użytkowników programu Visual Studio Code.
* A na koniec [narzędzi interfejsu wiersza polecenia podstawowe](../azure-functions/functions-run-local.md#create-func) niezależny od środowisko IDE dla wielu platform.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Lokalne uruchamianie wyzwalacza usługi Azure Cosmos DB

Można uruchomić usługi [funkcji platformy Azure lokalnie](../azure-functions/functions-develop-local.md) z [emulatora usługi Azure Cosmos DB](./local-emulator.md) do tworzenia i programowania bezserwerowe przepływy oparte na zdarzeniach bez subskrypcji platformy Azure i ponoszenia żadnych kosztów.

Jeśli chcesz przetestować scenariusze, na żywo w chmurze, możesz to zrobić [Wypróbuj usługę Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) bez karty kredytowej lub wymagana jest subskrypcja platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz dowiedzieć się więcej o zmianie kanału informacyjnego w następujących artykułach:

* [Omówienie Kanał informacyjny zmian](change-feed.md)
* [Sposoby na odczytywanie zestawienia zmian](read-change-feed.md)
* [Za pomocą zmian źródła danych z biblioteką procesora](change-feed-processor.md)
* [Jak pracować z zmiany źródła danych w bibliotece procesora](change-feed-processor.md)
* [Bezserwerowa baza danych obliczeń z użyciem usługi Azure Cosmos DB i Azure Functions](serverless-computing-database.md)
