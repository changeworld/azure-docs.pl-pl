---
title: Omówienie usługi Azure Event Hubs API | Microsoft Docs
description: Ten artykuł zawiera omówienie dostępnych interfejsów API (środowisko uruchomieniowe i zarządzanie) do korzystania z usługi Azure Event Hubs.
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
ms.openlocfilehash: c852bdeb30efe6acf626ae67028ec1ccb9e0b6db
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310972"
---
# <a name="available-event-hubs-apis"></a>Dostępne Event Hubs interfejsy API

W tym artykule opisano zestaw dostępnych klientów interfejsu API, których można używać do zarządzania zasobami Event Hubs.

## <a name="runtime-apis"></a>Interfejsy API środowiska uruchomieniowego

W poniższej sekcji opisano wszystkie aktualnie dostępne klienci środowiska uruchomieniowego usługi Azure Event Hubs. Niektóre z tych bibliotek zawierają również ograniczoną funkcjonalność zarządzania, ale istnieją również pewne [biblioteki](#management-apis) przeznaczone do operacji zarządzania. Podstawowym fokusem tych bibliotek jest wysyłanie i odbieranie komunikatów z centrum zdarzeń.

Aby uzyskać więcej informacji o bieżącym stanie każdej biblioteki środowiska uruchomieniowego, zobacz [dodatkowe informacje](#additional-information).

| Język/platforma | Pakiet klienta | Pakiet klasy eventprocessorhost | Repozytorium |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | ND |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Węzeł | [NPM](https://www.npmjs.com/package/azure-event-hubs) | ND | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | ND | ND | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Dodatkowe informacje

#### <a name="net"></a>.NET

Ekosystem .NET ma wiele środowisk uruchomieniowych, więc istnieje wiele bibliotek .NET do Event Hubs. Bibliotekę .NET Standard można uruchomić przy użyciu platformy .NET Core lub .NET Framework, podczas gdy biblioteka .NET Framework może być uruchamiana tylko w środowisku .NET Framework. Aby uzyskać więcej informacji na temat wersji .NET Framework, zobacz [wersje architektury](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Węzeł

[Biblioteka Node. js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) jest obecnie w wersji zapoznawczej i jest utrzymywana jako projekt po stronie firmy Microsoft i zewnętrznych współautorów. Wszystkie wkłady, w tym kod źródłowy, są powitane i zostaną zrecenzowane.

## <a name="management-apis"></a>Interfejsy API zarządzania

W poniższej tabeli wymieniono wszystkie aktualnie dostępne biblioteki specyficzne dla zarządzania. Żadna z tych bibliotek nie zawiera operacji środowiska uruchomieniowego i służy wyłącznie do zarządzania jednostkami Event Hubs.

| Język/platforma | Pakiet zarządzania | Repozytorium |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
