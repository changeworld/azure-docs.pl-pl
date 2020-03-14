---
title: Azure Service Bus odzyskiwania po awarii geograficznej | Microsoft Docs
description: Jak używać regionów geograficznych do przełączania do trybu failover i odzyskiwania po awarii w Azure Service Bus
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259580"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geograficznie — odzyskiwanie po awarii

W przypadku awarii całego regionu platformy Azure lub centrów danych (jeśli nie są używane [strefy dostępności](../availability-zones/az-overview.md) ) nie ma możliwości zapewnienia przestoju w celu przeprowadzenia działania w innym regionie lub w centrum. W związku z tym *odzyskiwanie geograficznego odzyskiwania po awarii* jest ważną funkcją dla każdego przedsiębiorstwa. Azure Service Bus obsługuje odzyskiwanie geograficzne po awarii na poziomie przestrzeni nazw.

Funkcja odzyskiwania po awarii geograficznej jest globalnie dostępna dla jednostki SKU Service Bus Premium. 

>[!NOTE]
> Odzyskiwanie po awarii geograficznej obecnie gwarantuje, że metadane (kolejki, tematy, subskrypcje, filtry) są kopiowane z podstawowej przestrzeni nazw do pomocniczej przestrzeni nazw po sparowaniu.

## <a name="outages-and-disasters"></a>Wyłączeń i awarii

Ważne jest, aby należy zauważyć różnicę między "wyłączeń" i "awarii." 

*Awaria* to tymczasowa niedostępność Azure Service Bus i może mieć wpływ na niektóre składniki usługi, takie jak magazyn komunikatów, a nawet całe centrum danych. Jednak po rozwiązaniu problemu Service Bus znów będzie dostępny. Zazwyczaj awaria nie powoduje utraty wiadomości lub inne dane. Przykładem takich awarii może być awaria zasilania w centrum danych. Niektóre awarie są tylko połączenia krótki straty ze względu na problemy przejściowe lub sieci. 

*Awaria* jest definiowana jako trwała lub w długim czasie utrata Service Bus klastra, regionu platformy Azure lub centrum danych. Region lub centrum danych może nie może stać się dostępna ponownie lub może nie działać przez kilka godzin lub dni. Przykładami takich awarii są pożaru, przepełnieniu lub trzęsienie ziemi. Po awarii, która staje się stałe może spowodować utratę niektórych komunikatów, zdarzenia lub inne dane. Jednak w większości przypadków należy bez utraty danych, a komunikaty mogą być odzyskiwane po centrum danych zacznie ponownie działać.

Funkcja odzyskiwania po awarii geograficznej Azure Service Bus jest rozwiązaniem odzyskiwania po awarii. Pojęcia i przepływ pracy opisany w tym artykule mają zastosowanie do scenariuszy awarii, a nie przejściowe i tymczasowe awarii. Aby uzyskać szczegółowe omówienie odzyskiwania po awarii w Microsoft Azure, zobacz [ten artykuł](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Podstawowych pojęć i terminów

Funkcja odzyskiwania po awarii implementuje odzyskiwanie po awarii metadane i opiera się na przestrzeni nazw odzyskiwania po awarii podstawowego i pomocniczego. Należy pamiętać, że funkcja odzyskiwania geograficznego po awarii jest dostępna tylko dla [jednostki SKU Premium](service-bus-premium-messaging.md) . Nie trzeba wprowadzać żadnych zmian ciągu połączenia, ponieważ połączenie zostało nawiązane za pomocą aliasu.

W tym artykule są używane następujące terminy:

-  *Alias*: Nazwa skonfigurowanej konfiguracji odzyskiwania po awarii. Alias zapewnia pojedynczy ciąg stabilne połączenie w pełni kwalifikowanej domeny nazwę (FQDN). Aplikacje za pomocą te parametry połączenia aliasu połączyć z przestrzeni nazw. Użycie aliasu gwarantuje, że parametry połączenia nie zmienią się podczas wyzwolenia trybu failover.

-  *Podstawowa/pomocnicza przestrzeń nazw*: przestrzenie nazw, które odpowiadają aliasu. Podstawowa przestrzeń nazw jest "aktywny" i odbiera komunikaty (może to być istniejącej lub nowej przestrzeni nazw). Pomocnicza przestrzeń nazw jest "pasywny" i nie otrzymywać wiadomości. Metadanych między obiema jest zsynchronizowany, więc zarówno bezproblemowo może akceptować komunikaty bez wprowadzania żadnych zmian parametry aplikacji, jak połączenie lub kod. Aby upewnić się, że tylko aktywnej przestrzeni nazw odbiera komunikaty, musisz użyć tego aliasu. 

-  *Metadane*: jednostki, takie jak kolejki, tematy i subskrypcje; i ich właściwości usługi, które są skojarzone z przestrzenią nazw. Należy pamiętać, że tylko jednostek i ich ustawienia są replikowane automatycznie. Komunikaty nie są replikowane.

-  *Tryb failover*: proces aktywowania pomocniczej przestrzeni nazw.

## <a name="setup"></a>Konfigurowanie

Poniższa sekcja zawiera omówienie konfigurowania parowania między przestrzeniami nazw.

![1][]

Proces instalacji jest następujący:

1. Zainicjuj obsługę przestrzeni nazw ***podstawowej*** Service Bus Premium.

2. Zainicjuj obsługę administracyjną przestrzeni nazw ***pomocniczej*** Service Bus w regionie *innym niż lokalizacja podstawowej przestrzeni nazw*. Pomoże to zapewnić izolację błędów w różnych regionach centrum danych.

3. Utwórz parowanie między podstawową przestrzenią nazw i pomocniczą przestrzenią nazw, aby uzyskać ***alias***.

    >[!NOTE] 
    > Jeśli [przeprowadzono migrację Azure Service Bus standardowej przestrzeni nazw do Azure Service Bus Premium](service-bus-migrate-standard-premium.md), należy użyć istniejącego aliasu (tj. Service Bus parametrów połączenia standardowej przestrzeni nazw), aby utworzyć konfigurację odzyskiwania po awarii za pomocą narzędzia **PS/CLI** lub **interfejsu API REST**.
    >
    >
    > Wynika to z faktu, że podczas migracji Azure Service Bus standardowe parametry połączenia z przestrzenią nazw/sama nazwa DNS staną się aliasem przestrzeni nazw Azure Service Bus Premium.
    >
    > Aplikacje klienckie muszą korzystać z tego aliasu (tj. Azure Service Bus standardowe parametry połączenia z przestrzenią nazw) do nawiązywania połączenia z przestrzenią nazw Premium, w której skonfigurowano parowanie odzyskiwania po awarii.
    >
    > W przypadku korzystania z portalu w celu skonfigurowania konfiguracji odzyskiwania po awarii, w portalu zostaną one abstrakcyjne.


4. Użyj ***aliasu*** uzyskanego w kroku 3, aby połączyć aplikacje klienckie z podstawową przestrzenią nazw obsługującą funkcję odzyskiwania po awarii. Początkowo alias wskazuje podstawową przestrzeń nazw.

5. Obowiązkowe Dodaj monitorowanie, aby wykryć, czy w trybie failover jest wymagane.

## <a name="failover-flow"></a>Przepływ pracy awaryjnej

Przełączenie w tryb failover jest wyzwalane ręcznie przez klienta (jawnie za pomocą polecenia lub za pomocą logiki biznesowej należącej do klienta, która wyzwala polecenie), a nie przez platformę Azure. Zapewnia to klientowi pełną własność i widoczność na potrzeby rozwiązywania awarii w sieci szkieletowej platformy Azure.

![4][]

Po wyzwoleniu trybu failover

1. Parametry połączenia ***aliasu*** są aktualizowane w celu wskazywania pomocniczej przestrzeni nazw w warstwie Premium.

2. Klienci (nadawcy i odbiorcy) automatycznie łączą się z pomocniczą przestrzenią nazw.

3. Istniejące parowanie między podstawową i pomocniczą przestrzenią nazw Premium jest zerwane.

Po zainicjowaniu trybu failover —

1. W przypadku wystąpienia innej awarii należy ponownie przejść do trybu failover. W związku z tym Ustaw innej pasywnym przestrzeni nazw i zaktualizuj parowanie. 

2. Ściągają komunikaty z poprzedniej wersji portalu podstawowej przestrzeni nazw, gdy będzie znowu dostępna. Po tym regularne komunikatów poza konfigurację odzyskiwania replikacji geograficznej na użytek tej przestrzeni nazw lub Usuń stare podstawowej przestrzeni nazw.

> [!NOTE]
> Obsługiwane są wyłącznie zakończyć się niepowodzeniem do przodu semantyki. W tym scenariuszu w trybie Failover, a następnie ponownie łączyć się z nową przestrzeń nazw. Powrotem nie jest obsługiwana; na przykład w klastrze programu SQL. 

Można zautomatyzować trybu failover z systemów monitorowania lub za pomocą niestandardowej rozwiązania do monitorowania. Jednak takie automatyzacji zajmują dodatkowego planowania i współpracować, która wykracza poza zakres tego artykułu.

![2][]

## <a name="management"></a>Serwer

Jeśli w przypadku popełnienia; na przykład sparowane regiony problem podczas początkowej konfiguracji, może przerwać parowanie dwie przestrzeni nazw w dowolnym momencie. Jeśli chcesz użyć sparowane przestrzenie nazw jako regularne przestrzenie nazw, Usuń ten alias.

## <a name="use-existing-namespace-as-alias"></a>Użyj istniejącej przestrzeni nazw jako aliasu

Jeśli masz scenariusz, w którym nie można zmienić połączeń producentów i konsumentów, możesz ponownie użyć nazwy przestrzeni nazw jako aliasu. Zapoznaj się z [przykładowym kodem w witrynie GitHub tutaj](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Przykłady

[Przykłady w witrynie GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) pokazują, jak skonfigurować i zainicjować tryb failover. Te przykłady przedstawiają następujące koncepcje:

- Przykład i ustawienia programu .NET, które są wymagane w Azure Active Directory do użycia Azure Resource Manager z Service Bus, w celu skonfigurowania i włączenia geograficznego odzyskiwania po awarii.
- Kroki wymagane do wykonania w przykładowym kodzie.
- Jak używać istniejącej przestrzeni nazw jako aliasu.
- Kroki umożliwiające Alternatywnie umożliwienie odzyskiwania po awarii geograficznej za pomocą programu PowerShell lub interfejsu wiersza polecenia.
- [Wyślij i Odbierz](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) z bieżącej podstawowej lub pomocniczej przestrzeni nazw przy użyciu aliasu.

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy zwrócić uwagę następujące kwestie, które należy uwzględnić w tej wersji:

1. W procesie planowania trybu failover, należy również rozważyć współczynnik czasu. Na przykład jeśli w przypadku utraty łączności przez czas dłuższy niż 15-20 minut, można zdecydować do zainicjowania trybu failover.

2. Fakt, że żadne dane nie są replikowane oznacza, że obecnie aktywnych sesji nie są replikowane. Ponadto wykrywania duplikatów i zaplanowane komunikaty mogą nie działać. Nowe sesje, nowe zaplanowane wiadomości i nowe duplikaty będą działały. 

3. Przełączenie w tryb failover złożonej infrastruktury rozproszonej powinno być [rehearsed](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) co najmniej raz.

4. Synchronizowanie jednostek może zająć trochę czasu około 50 – 100 jednostek na minutę. Subskrypcje i reguły są również liczone jako jednostki.

## <a name="availability-zones"></a>Strefy dostępności

Jednostka SKU Service Bus Premium obsługuje także [strefy dostępności](../availability-zones/az-overview.md), zapewniając lokalizacje izolowane w regionie platformy Azure.

> [!NOTE]
> Strefy dostępności wsparcia dla Azure Service Bus Premium jest dostępna tylko w [regionach świadczenia usługi Azure](../availability-zones/az-overview.md#services-support-by-region) , w których znajdują się strefy dostępności.

Strefy dostępności można włączyć na nowe przestrzenie nazw, przy użyciu witryny Azure portal. Service Bus nie obsługuje migracji istniejących przestrzeni nazw. Nie można wyłączyć nadmiarowości strefy po jej włączeniu, w ramach przestrzeni nazw.

![3][]

## <a name="next-steps"></a>Następne kroki

- Zobacz [Informacje o interfejsie API REST](/rest/api/servicebus/disasterrecoveryconfigs)odzyskiwania po awarii geograficznej w tym miejscu.
- Uruchom przykład odzyskiwania geograficznego po awarii [w serwisie GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Zobacz przykład odzyskiwania geograficznego po awarii [, który wysyła komunikaty do aliasu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące artykuły:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Interfejs API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
