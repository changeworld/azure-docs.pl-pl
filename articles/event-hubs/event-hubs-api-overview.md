---
title: Omówienie usługi Azure Event Hubs API | Dokumentacja firmy Microsoft
description: Omówienie interfejsów API usługi dostępne Azure Event Hubs
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 80566b0246179064d2a479b8c9bf3c79a2a93aac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822618"
---
# <a name="available-event-hubs-apis"></a>Zdarzenie dostępne koncentratory API

W tym artykule opisano zestaw dostępnych klientów interfejsów API służących do zarządzania zasobami usługi Event Hubs.

## <a name="runtime-apis"></a>Interfejsów API środowiska wykonawczego

W poniższej sekcji opisano wszystkich aktualnie dostępnych klientów środowiska uruchomieniowego usługi Azure Event Hubs. Chociaż niektóre z tych bibliotek także zawiera funkcje zarządzania ograniczone, dostępne są także [określone biblioteki](#management-apis) przeznaczone do obsługi operacji zarządzania. Podstawowe tych bibliotek koncentruje się na wysyłanie oraz odbieranie komunikatów z Centrum zdarzeń.

Aby uzyskać więcej informacji o bieżącym statusie każdej biblioteki środowiska uruchomieniowego, zobacz [dodatkowe informacje](#additional-information).

| Języka/platformy | Pakiet klienta | EventProcessorHost pakietu | Repozytorium |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | ND |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Węzeł | [NPM](https://www.npmjs.com/package/azure-event-hubs) | ND | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | ND | ND | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Dodatkowe informacje

#### <a name="net"></a>.NET

Ekosystem platformy .NET ma wielu modułów wykonawczych, istnieje więc wiele bibliotek platformy .NET dla usługi Event Hubs. Biblioteki .NET Standard mogą być uruchamiane przy użyciu platformy .NET Core lub .NET Framework, natomiast biblioteka .NET Framework można uruchomić tylko w środowisku .NET Framework. Aby uzyskać więcej informacji na temat wersji programu .NET Framework, zobacz [framework w wersji](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Węzeł

[Biblioteka języka Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) jest obecnie dostępna w wersji zapoznawczej i jest obsługiwane jako projekt po stronie przez pracowników firmy Microsoft i współautorów zewnętrznych. Wszystkie współtworzone elementy, łącznie z kodem źródłowym Zapraszamy i zostanie przejrzana.

## <a name="management-apis"></a>Interfejsy API zarządzania

Poniższa tabela zawiera listę wszystkich aktualnie dostępnych bibliotek specyficznych dla zarządzania. Żadna z tych bibliotek zawierają operacji środowiska uruchomieniowego i są wyłącznie w celu zarządzania jednostkami usługi Event Hubs.

| Języka/platformy | Pakiet zarządzania | Repozytorium |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Kolejne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
