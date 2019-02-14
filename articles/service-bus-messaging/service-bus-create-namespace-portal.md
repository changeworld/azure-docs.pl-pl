---
title: Jak utworzyć przestrzeń nazw usługi Service Bus w witrynie Azure Portal | Microsoft Docs
description: Tworzenie przestrzeni nazw usługi Service Bus za pomocą witryny Azure Portal.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/12/2019
ms.author: aschhab
ms.openlocfilehash: 632ef45d4db5de03369e0abb8b16590911bdffdb
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233306"
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Tworzenie przestrzeni nazw usługi Service Bus za pomocą witryny Azure Portal

Przestrzeń nazw jest kontenerem określania zakresu dla wszystkich składników służących do obsługi komunikatów. W jednej przestrzeni nazw może znajdować się wiele kolejek i tematów, a przestrzenie nazw często pełnią rolę kontenerów aplikacji. Są dwa sposoby tworzenia przestrzeni nazw usługi Service Bus:

1. Azure Portal (w tym artykule)
2. [Szablony usługi Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Tworzenie przestrzeni nazw w witrynie Azure Portal

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Gratulacje! Utworzono przestrzeń nazw obsługi komunikatów usługi Service Bus.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [przykładami w repozytorium GitHub][github-samples] dla usługi Service Bus. Przedstawiono w nich niektóre bardziej zaawansowane funkcje obsługi komunikatów w usłudze Service Bus.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
