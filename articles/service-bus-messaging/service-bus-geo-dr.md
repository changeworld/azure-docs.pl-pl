---
title: Odzyskiwanie geograficzne usługi Azure Service Bus po awarii | Dokumenty firmy Microsoft
description: Jak używać regionów geograficznych do pracy awaryjnej i wykonywania odzyskiwania po awarii w usłudze Azure Service Bus
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259580"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Odzyskiwanie geograficzne usługi Azure Service Bus

Gdy całe regiony platformy Azure lub centra danych (jeśli nie są używane strefy dostępności) występują [przestoje,](../availability-zones/az-overview.md) ważne jest, aby przetwarzanie danych nadal działało w innym regionie lub centrum danych. W związku z tym *odzyskiwanie po awarii geograficznej* jest ważną funkcją dla każdego przedsiębiorstwa. Usługa Azure Service Bus obsługuje odzyskiwanie po awarii geograficznej na poziomie obszaru nazw.

Funkcja odzyskiwania po awarii geograficznej jest dostępna na całym świecie dla jednostki SKU premium usługi Service Bus. 

>[!NOTE]
> Odzyskiwanie po awarii geograficznej zapewnia obecnie tylko, że metadane (kolejki, tematy, subskrypcje, filtry) są kopiowane z podstawowego obszaru nazw do pomocniczego obszaru nazw po sparowaniu.

## <a name="outages-and-disasters"></a>Awarie i awarie

Ważne jest, aby zwrócić uwagę na rozróżnienie między "awariami" i "katastrofami". 

*Awaria* jest tymczasowa niedostępność usługi Azure Service Bus i może mieć wpływ na niektóre składniki usługi, takich jak magazyn wiadomości lub nawet całe centrum danych. Jednak po naprawieniu problemu usługa Service Bus staje się ponownie dostępna. Zazwyczaj awaria nie powoduje utraty wiadomości lub innych danych. Przykładem takiej awarii może być awaria zasilania w centrum danych. Niektóre awarie to tylko krótkie straty połączenia spowodowane przejściowymi lub problemami z siecią. 

*Awaria* jest definiowana jako trwałe lub długoterminowe straty klastra usługi Service Bus, regionu platformy Azure lub centrum danych. Region lub centrum danych może lub nie może stać się ponownie dostępne lub może być w dół przez wiele godzin lub dni. Przykładami takich katastrof są pożar, powodzie lub trzęsienie ziemi. Katastrofa, która staje się trwałe może spowodować utratę niektórych wiadomości, zdarzeń lub innych danych. Jednak w większości przypadków nie powinno być utraty danych i wiadomości można odzyskać po centrum danych jest kopii zapasowej.

Funkcja odzyskiwania po awarii geograficznej usługi Azure Service Bus jest rozwiązaniem do odzyskiwania po awarii. Pojęcia i przepływ pracy opisane w tym artykule dotyczą scenariuszy awarii, a nie przejściowych lub tymczasowych awarii. Szczegółowe omówienie odzyskiwania po awarii na platformie Microsoft Azure można znaleźć w [tym artykule](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Podstawowe pojęcia i terminy

Funkcja odzyskiwania po awarii implementuje odzyskiwanie po awarii metadanych i opiera się na podstawowych i pomocniczych obszarach nazw odzyskiwania po awarii. Należy zauważyć, że funkcja odzyskiwania po awarii geograficznej jest dostępna tylko dla [jednostki SKU Premium.](service-bus-premium-messaging.md) Nie trzeba wprowadzać żadnych zmian ciągu połączenia, ponieważ połączenie jest nawiązywać za pośrednictwem aliasu.

W tym artykule używane są następujące terminy:

-  *Alias:* Nazwa skonfigurowana konfiguracji odzyskiwania po awarii. Alias zapewnia jeden stabilny ciąg połączenia W pełni kwalifikowana nazwa domeny (FQDN). Aplikacje używają tego ciągu połączenia aliasu do łączenia się z obszarem nazw. Użycie aliasu zapewnia, że parametry połączenia pozostają niezmienione po wyzwoleniu trybu failover.

-  *Podstawowa/pomocnicza przestrzeń nazw*: Przestrzenie nazw, które odpowiadają aliasowi. Podstawowy obszar nazw jest "aktywny" i odbiera wiadomości (może to być istniejący lub nowy obszar nazw). Pomocniczy obszar nazw jest "pasywny" i nie odbiera wiadomości. Metadane między nimi są zsynchronizowane, więc oba mogą bezproblemowo akceptować wiadomości bez żadnych zmian kodu aplikacji lub ciągu połączenia. Aby upewnić się, że tylko aktywny obszar nazw odbiera wiadomości, należy użyć aliasu. 

-  *Metadane*: Jednostki, takie jak kolejki, tematy i subskrypcje; i ich właściwości usługi, które są skojarzone z obszarem nazw. Należy zauważyć, że tylko jednostki i ich ustawienia są replikowane automatycznie. Wiadomości nie są replikowane.

-  *Przewijanie w stan failover:* Proces aktywowania pomocniczego obszaru nazw.

## <a name="setup"></a>Konfiguracja

Poniższa sekcja zawiera omówienie parowania konfiguracji między obszarami nazw.

![1][]

Proces konfiguracji jest następujący -

1. Aprowizuj obszar nazw premium magistrali usług ***podstawowych.***

2. Aprowizuj ***pomocniczy*** obszar nazw usługi Premium w regionie *innym niż obszar nazw podstawowy jest aprowizowany*. Pomoże to umożliwić izolację błędów w różnych regionach centrum danych.

3. Utwórz parowanie między głównym obszarem nazw a pomocniczą przestrzenią nazw, aby uzyskać ***alias***.

    >[!NOTE] 
    > W przypadku [migracji standardowego obszaru nazw usługi Azure Service Bus do usługi Azure Service Bus Premium](service-bus-migrate-standard-premium.md)należy użyć istniejącego aliasu (tj. ciągu połączenia standardowego obszaru nazw usługi Service Bus), aby utworzyć konfigurację odzyskiwania po awarii za pośrednictwem interfejsu API **PS/CLI** lub **REST**.
    >
    >
    > Dzieje się tak, ponieważ podczas migracji ciąg połączenia usługi Azure Service Bus standard/sama nazwa DNS staje się aliasem obszaru nazw usługi Azure Service Bus Premium.
    >
    > Aplikacje klienckie muszą korzystać z tego aliasu (tj. ciągu połączenia obszaru nazw usługi Azure Service Bus standard), aby połączyć się z obszarem nazw premium, w którym zostało skonfigurowane parowanie odzyskiwania po awarii.
    >
    > Jeśli używasz portalu do skonfigurowania konfiguracji odzyskiwania po awarii, portal będzie wyodrębnić to zastrzeżenie od Ciebie.


4. Alias ***uzyskany*** w kroku 3 umożliwia połączenie aplikacji klienckich z podstawową przestrzenią nazw z włączoną funkcją Geo-DR. Początkowo alias wskazuje na podstawowy obszar nazw.

5. [Opcjonalnie] Dodaj monitorowanie, aby wykryć, czy konieczne jest przejście awaryjne.

## <a name="failover-flow"></a>Przepływ trybu failover

Praca awaryjna jest wyzwalana ręcznie przez klienta (jawnie za pomocą polecenia lub za pośrednictwem logiki biznesowej należącej do klienta, która wyzwala polecenie) i nigdy przez platformę Azure. Daje to klientowi pełną własność i wgląd w rozwiązanie problemu awarii w szkieletie platformy Azure.

![4][]

Po wyzwoleniu pracy awaryjnej -

1. Ciąg połączenia ***aliasu*** jest aktualizowany w celu wskazanie obszaru nazw secondary premium.

2. Klienci (nadawcy i odbiorcy) automatycznie łączą się z pomocniczą przestrzenią nazw.

3. Istniejące parowanie między głównym i pomocniczym obszarem nazw premii jest uszkodzony.

Po zainicjowaniu pracy awaryjnej -

1. Jeśli wystąpi inna awaria, chcesz mieć możliwość ponownego przebienia awaryjnego. W związku z tym należy skonfigurować inny pasywny obszar nazw i zaktualizować parowanie. 

2. Ściągaj wiadomości z dawnego podstawowego obszaru nazw, gdy będzie on ponownie dostępny. Następnie użyj tej przestrzeni nazw do regularnych wiadomości poza konfiguracją odzyskiwania geograficznego lub usuń stary podstawowy obszar nazw.

> [!NOTE]
> Obsługiwane są tylko semantyka do przodu. W tym scenariuszu można awaryjnie, a następnie ponownie sparować z nowym obszarem nazw. Niepowodzenie z powrotem nie jest obsługiwane; na przykład w klastrze SQL. 

Można zautomatyzować pracy awaryjnej za pomocą systemów monitorowania lub z niestandardowych rozwiązań monitorowania. Jednak taka automatyzacja wymaga dodatkowego planowania i pracy, która jest poza zakresem tego artykułu.

![2][]

## <a name="management"></a>Zarządzanie

Jeśli popełniłeś błąd; na przykład sparowano niewłaściwe regiony podczas początkowej konfiguracji, można przerwać parowanie dwóch obszarów nazw w dowolnym momencie. Jeśli chcesz używać sparowanych obszarów nazw jako zwykłych obszarów nazw, usuń alias.

## <a name="use-existing-namespace-as-alias"></a>Używanie istniejącego obszaru nazw jako aliasu

Jeśli masz scenariusz, w którym nie można zmienić połączenia producentów i konsumentów, można ponownie użyć nazwy obszaru nazw jako nazwę aliasu. Zobacz [przykładowy kod na GitHub tutaj](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Samples

[Przykłady w usłudze GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) pokazują, jak skonfigurować i zainicjować przebłaganie pracy awaryjnej. W tych przykładach przedstawiono następujące pojęcia:

- Przykład .NET i ustawienia, które są wymagane w usłudze Azure Active Directory do korzystania z usługi Azure Resource Manager z usługą Service Bus, aby skonfigurować i włączyć odzyskiwanie po awarii geograficznej.
- Kroki wymagane do wykonania przykładowego kodu.
- Jak używać istniejącego obszaru nazw jako aliasu.
- Kroki, aby alternatywnie włączyć odzyskiwanie po awarii geograficznej za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia.
- [Wysyłanie i odbieranie](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) z bieżącego podstawowego lub pomocniczego obszaru nazw przy użyciu aliasu.

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy zwrócić uwagę na następujące kwestie, o które należy pamiętać w tej wersji:

1. W planowaniu pracy awaryjnej należy również wziąć pod uwagę współczynnik czasu. Na przykład w przypadku utraty łączności na dłużej niż 15 do 20 minut, może zdecydować się na zainicjowanie pracy awaryjnej.

2. Fakt, że żadne dane nie są replikowane oznacza, że aktualnie aktywne sesje nie są replikowane. Ponadto wykrywanie duplikatów i zaplanowane wiadomości mogą nie działać. Będą działać nowe sesje, nowe zaplanowane wiadomości i nowe duplikaty. 

3. Awaria złożonej infrastruktury rozproszonej powinna być [przećwiczona](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) co najmniej raz.

4. Synchronizowanie jednostek może zająć trochę czasu, około 50-100 jednostek na minutę. Subskrypcje i reguły są również liczone jako jednostki.

## <a name="availability-zones"></a>Strefy dostępności

Usługa Service Bus Premium SKU obsługuje również [strefy dostępności,](../availability-zones/az-overview.md)zapewniając lokalizacje izolowane od błędów w regionie platformy Azure.

> [!NOTE]
> Obsługa stref dostępności usługi Azure Service Bus Premium jest dostępna tylko w [regionach platformy Azure,](../availability-zones/az-overview.md#services-support-by-region) w których znajdują się strefy dostępności.

Strefy dostępności można włączyć tylko w nowych obszarach nazw, korzystając z witryny Azure portal. Usługa Service Bus nie obsługuje migracji istniejących obszarów nazw. Nadmiarowość strefy nie może być wyłączna po włączeniu jej w obszarze nazw.

![3][]

## <a name="next-steps"></a>Następne kroki

- Zobacz geo-disaster recovery [REST API odwołania tutaj](/rest/api/servicebus/disasterrecoveryconfigs).
- Uruchom przykład odzyskiwania po awarii [geograficznej w usłudze GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Zobacz przykład odzyskiwania po awarii [geograficznej, który wysyła wiadomości do aliasu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące artykuły:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Interfejs API odpoczynku](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
