---
title: Odzyskiwanie po awarii geograficznej — usługi Azure Event Hubs| Dokumenty firmy Microsoft
description: Jak używać regionów geograficznych do pracy awaryjnej i wykonywania odzyskiwania po awarii w usłudze Azure Event Hubs
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 40db6e9f429569bc19641aa5f0f371f287db7b18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281472"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Usługi Azure Event Hubs — odzyskiwanie po awarii geograficznej 

Gdy całe regiony platformy Azure lub centra danych (jeśli nie są używane strefy dostępności) występują [przestoje,](../availability-zones/az-overview.md) ważne jest, aby przetwarzanie danych nadal działało w innym regionie lub centrum danych. W związku z tym *odzyskiwanie po awarii geograficznej* i *replikacja geograficzna* są ważnymi funkcjami dla każdego przedsiębiorstwa. Usługa Azure Event Hubs obsługuje zarówno odzyskiwanie po awarii geograficznej, jak i replikację geograficzną na poziomie obszaru nazw. 

> [!NOTE]
> Funkcja odzyskiwania po awarii geograficznej jest dostępna tylko dla [standardowych i dedykowanych jednostek SKU.](https://azure.microsoft.com/pricing/details/event-hubs/)  

## <a name="outages-and-disasters"></a>Awarie i awarie

Ważne jest, aby zwrócić uwagę na rozróżnienie między "awariami" i "katastrofami". *Awaria* jest tymczasowa niedostępność usługi Azure Event Hubs i może mieć wpływ na niektóre składniki usługi, takich jak magazyn wiadomości lub nawet całe centrum danych. Jednak po naprawieniu problemu centra zdarzeń stają się ponownie dostępne. Zazwyczaj awaria nie powoduje utraty wiadomości lub innych danych. Przykładem takiej awarii może być awaria zasilania w centrum danych. Niektóre awarie to tylko krótkie straty połączenia spowodowane przejściowymi lub problemami z siecią. 

*Awaria* jest definiowana jako trwała lub dłuższa utrata klastra usługi Event Hubs, regionu platformy Azure lub centrum danych. Region lub centrum danych może lub nie może stać się ponownie dostępne lub może być w dół przez wiele godzin lub dni. Przykładami takich katastrof są pożar, powodzie lub trzęsienie ziemi. Katastrofa, która staje się trwałe może spowodować utratę niektórych wiadomości, zdarzeń lub innych danych. Jednak w większości przypadków nie powinno być utraty danych i wiadomości można odzyskać po centrum danych jest kopii zapasowej.

Funkcja odzyskiwania po awarii geograficznej usługi Azure Event Hubs jest rozwiązaniem do odzyskiwania po awarii. Pojęcia i przepływ pracy opisane w tym artykule dotyczą scenariuszy awarii, a nie przejściowych lub tymczasowych awarii. Szczegółowe omówienie odzyskiwania po awarii na platformie Microsoft Azure można znaleźć w [tym artykule](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Podstawowe pojęcia i terminy

Funkcja odzyskiwania po awarii implementuje odzyskiwanie po awarii metadanych i opiera się na podstawowych i pomocniczych obszarach nazw odzyskiwania po awarii. 

Funkcja odzyskiwania po awarii geograficznej jest dostępna tylko dla [standardowych i dedykowanych jednostek SKU.](https://azure.microsoft.com/pricing/details/event-hubs/) Nie trzeba wprowadzać żadnych zmian ciągu połączenia, ponieważ połączenie jest nawiązywać za pośrednictwem aliasu.

W tym artykule używane są następujące terminy:

-  *Alias:* Nazwa skonfigurowana konfiguracji odzyskiwania po awarii. Alias zapewnia jeden stabilny ciąg połączenia W pełni kwalifikowana nazwa domeny (FQDN). Aplikacje używają tego ciągu połączenia aliasu do łączenia się z obszarem nazw. 

-  *Podstawowa/pomocnicza przestrzeń nazw*: Przestrzenie nazw, które odpowiadają aliasowi. Podstawowy obszar nazw jest "aktywny" i odbiera wiadomości (może to być istniejący lub nowy obszar nazw). Pomocniczy obszar nazw jest "pasywny" i nie odbiera wiadomości. Metadane między nimi są zsynchronizowane, więc oba mogą bezproblemowo akceptować wiadomości bez żadnych zmian kodu aplikacji lub ciągu połączenia. Aby upewnić się, że tylko aktywny obszar nazw odbiera wiadomości, należy użyć aliasu. 

-  *Metadane*: Jednostki, takie jak centra zdarzeń i grupy odbiorców; i ich właściwości usługi, które są skojarzone z obszarem nazw. Należy zauważyć, że tylko jednostki i ich ustawienia są replikowane automatycznie. Wiadomości i zdarzenia nie są replikowane. 

-  *Przewijanie w stan failover:* Proces aktywowania pomocniczego obszaru nazw.

## <a name="supported-namespace-pairs"></a>Obsługiwane pary obszarów nazw
Obsługiwane są następujące kombinacje podstawowych i pomocniczych obszarów nazw:  

| Podstawowa przestrzeń nazw | Pomocnicza przestrzeń nazw | Obsługiwane | 
| ----------------- | -------------------- | ---------- |
| Standardowa | Standardowa | Tak | 
| Standardowa | Dedykowane | Tak | 
| Dedykowane | Dedykowane | Tak | 
| Dedykowane | Standardowa | Nie | 

> [!NOTE]
> Nie można sparować obszarów nazw, które znajdują się w tym samym dedykowanym klastrze. Można sparować przestrzenie nazw, które znajdują się w oddzielnych klastrach. 

## <a name="setup-and-failover-flow"></a>Konfiguracja i przepływ pracy awaryjnej

Poniższa sekcja zawiera omówienie procesu pracy awaryjnej i wyjaśniono, jak skonfigurować początkową przerój stanu awaryjnego. 

![1][]

### <a name="setup"></a>Konfiguracja

Najpierw należy utworzyć lub użyć istniejącego podstawowego obszaru nazw i nowego pomocniczego obszaru nazw, a następnie sparować dwa. To parowanie zapewnia alias, którego można użyć do nawiązania połączenia. Ponieważ używasz aliasu, nie trzeba zmieniać ciągów połączeń. Do parowania trybu failover można dodać tylko nowe przestrzenie nazw. Na koniec należy dodać niektóre monitorowania, aby wykryć, czy pracy awaryjnej jest konieczne. W większości przypadków usługa jest częścią dużego ekosystemu, dlatego automatyczne praca awaryjna rzadko jest możliwa, ponieważ bardzo często praca awaryjna musi być wykonywana z synchronizacją z pozostałym podsystemem lub infrastrukturą.

### <a name="example"></a>Przykład

W jednym z przykładów tego scenariusza należy wziąć pod uwagę rozwiązanie punktu sprzedaży (POS), który emituje komunikaty lub zdarzenia. Usługa Event Hubs przekazuje te zdarzenia do rozwiązania mapowania lub formatowania, które następnie przekazuje zamapowane dane do innego systemu w celu dalszego przetwarzania. W tym momencie wszystkie te systemy mogą być hostowane w tym samym regionie platformy Azure. Decyzja o tym, kiedy i jaka część do awaryjnego przejęcia zależy od przepływu danych w infrastrukturze. 

Można zautomatyzować pracy awaryjnej za pomocą systemów monitorowania lub z niestandardowych rozwiązań monitorowania. Jednak taka automatyzacja wymaga dodatkowego planowania i pracy, która jest poza zakresem tego artykułu.

### <a name="failover-flow"></a>Przepływ trybu failover

W przypadku zainicjowania pracy awaryjnej wymagane są dwa kroki:

1. Jeśli wystąpi inna awaria, chcesz mieć możliwość ponownego pracy awaryjnej. W związku z tym należy skonfigurować inny pasywny obszar nazw i zaktualizować parowanie. 

2. Ściągaj wiadomości z dawnego podstawowego obszaru nazw, gdy będzie on ponownie dostępny. Następnie użyj tej przestrzeni nazw do regularnych wiadomości poza konfiguracją odzyskiwania geograficznego lub usuń stary podstawowy obszar nazw.

> [!NOTE]
> Obsługiwane są tylko semantyka do przodu. W tym scenariuszu można awaryjnie, a następnie ponownie sparować z nowym obszarem nazw. Niepowodzenie z powrotem nie jest obsługiwane; na przykład w klastrze SQL. 

![2][]

## <a name="management"></a>Zarządzanie

Jeśli popełniłeś błąd; na przykład sparowano niewłaściwe regiony podczas początkowej konfiguracji, można przerwać parowanie dwóch obszarów nazw w dowolnym momencie. Jeśli chcesz używać sparowanych obszarów nazw jako zwykłych obszarów nazw, usuń alias.

## <a name="samples"></a>Samples

Przykład [w usłudze GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) pokazuje, jak skonfigurować i zainicjować przebóję awaryjną. W tym przykładzie przedstawiono następujące pojęcia:

- Ustawienia wymagane w usłudze Azure Active Directory do korzystania z usługi Azure Resource Manager z centrum zdarzeń. 
- Kroki wymagane do wykonania przykładowego kodu. 
- Wysyłanie i odbieranie z bieżącego podstawowego obszaru nazw. 

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy zwrócić uwagę na następujące kwestie, o które należy pamiętać w tej wersji:

1. Zgodnie z projektem usługa Event Hubs geo-disaster recovery nie replikuje danych i dlatego nie można ponownie użyć starej wartości przesunięcia centrum zdarzeń podstawowych w pomocniczym centrum zdarzeń. Zalecamy ponowne uruchomienie odbiornika zdarzeń w jednym z następujących elementów:

- *EventPosition.FromStart()* — jeśli chcesz przeczytać wszystkie dane w centrum zdarzeń pomocniczych.
- *EventPosition.FromEnd()* — jeśli chcesz odczytać wszystkie nowe dane z czasu połączenia z centrum zdarzeń pomocniczych.
- *EventPosition.FromEnqueuedTime(dateTime)* — jeśli chcesz odczytać wszystkie dane otrzymane w centrum zdarzeń pomocniczych, począwszy od podanej daty i godziny.

2. W planowaniu pracy awaryjnej należy również wziąć pod uwagę współczynnik czasu. Na przykład w przypadku utraty łączności na dłużej niż 15 do 20 minut, może zdecydować się na zainicjowanie pracy awaryjnej. 
 
3. Fakt, że żadne dane nie są replikowane oznacza, że aktualnie aktywne sesje nie są replikowane. Ponadto wykrywanie duplikatów i zaplanowane wiadomości mogą nie działać. Nowe sesje, zaplanowane wiadomości i nowe duplikaty będą działać. 

4. Awaria złożonej infrastruktury rozproszonej powinna być [przećwiczona](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) co najmniej raz. 

5. Synchronizowanie jednostek może zająć trochę czasu, około 50-100 jednostek na minutę.

## <a name="availability-zones"></a>Strefy dostępności 

Usługa Event Hubs Standard SKU obsługuje [strefy dostępności,](../availability-zones/az-overview.md)zapewniając lokalizacje izolowane od błędów w regionie platformy Azure. 

> [!NOTE]
> Obsługa stref dostępności dla usługi Azure Event Hubs Standard jest dostępna tylko w [regionach platformy Azure,](../availability-zones/az-overview.md#services-support-by-region) w których znajdują się strefy dostępności.

Strefy dostępności można włączyć tylko w nowych obszarach nazw, korzystając z witryny Azure portal. Centra zdarzeń nie obsługują migracji istniejących obszarów nazw. Nadmiarowość strefy nie może być wyłączna po włączeniu jej w obszarze nazw.

![3][]

## <a name="next-steps"></a>Następne kroki

* [Przykład w usłudze GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) przechodzi przez prosty przepływ pracy, który tworzy parowanie geograficzne i inicjuje pracę awaryjną dla scenariusza odzyskiwania po awarii.
* [Odwołanie interfejsu API REST](/rest/api/eventhub/disasterrecoveryconfigs) opisuje interfejsy API do wykonywania konfiguracji odzyskiwania po awarii geograficznej.

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
