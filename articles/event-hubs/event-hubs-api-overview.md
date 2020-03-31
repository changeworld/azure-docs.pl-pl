---
title: Omówienie interfejsu API usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie dostępnych interfejsów API (środowiska uruchomieniowego i zarządzania) do korzystania z usługi Usługi Azure Event Hubs.
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
ms.openlocfilehash: 08e10996f633d35ffbf946b61937a0e9a76f7227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162229"
---
# <a name="available-event-hubs-apis"></a>Dostępne interfejsy API centrów zdarzeń

W tym artykule opisano zestaw dostępnych klientów interfejsu API, których można używać do zarządzania zasobami usługi Event Hubs.

## <a name="runtime-apis"></a>Interfejsy API środowiska wykonawczego

W poniższej sekcji opisano wszystkich aktualnie dostępnych klientów środowiska uruchomieniowego usługi Azure Event Hubs. Chociaż niektóre z tych bibliotek zawierają również ograniczone funkcje zarządzania, istnieją również [specyficzne biblioteki poświęcone](#management-apis) operacjom zarządzania. Głównym celem tych bibliotek jest wysyłanie i odbieranie wiadomości z centrum zdarzeń.

Aby uzyskać więcej informacji na temat bieżącego stanu każdej biblioteki środowiska wykonawczego, zobacz [dodatkowe informacje](#additional-information).

| Język/platforma | Pakiet klienta | Pakiet EventProcessorHost | Repozytorium |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | Nie dotyczy |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Węzeł | [Npm](https://www.npmjs.com/package/azure-event-hubs) | Nie dotyczy | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | Nie dotyczy | Nie dotyczy | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Dodatkowe informacje

#### <a name="net"></a>.NET

Ekosystem .NET ma wiele środowiska uruchomień, więc istnieje wiele bibliotek platformy .NET dla centrów zdarzeń. Bibliotekę .NET Standard można uruchomić przy użyciu programu .NET Core lub programu .NET Framework, podczas gdy biblioteka .NET Framework może być uruchamiana tylko w środowisku .NET Framework. Aby uzyskać więcej informacji na temat wersji programu .NET Framework, zobacz [wersje struktury](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Węzeł

[Biblioteka JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) jest obecnie w wersji zapoznawczej i jest obsługiwana jako projekt pobocznych przez pracowników firmy Microsoft i zewnętrznych współpracowników. Wszystkie wkłady, w tym kod źródłowy, są mile widziane i zostaną poddane przeglądowi.

## <a name="management-apis"></a>Interfejsy API zarządzania

W poniższej tabeli wymieniono wszystkie obecnie dostępne biblioteki specyficzne dla zarządzania. Żadna z tych bibliotek nie zawiera operacji środowiska uruchomieniowego i służą wyłącznie do zarządzania jednostkami centrum zdarzeń.

| Język/platforma | Pakiet zarządzania | Repozytorium |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
