---
title: Diagnozowanie i rozwiązywanie problemów w przypadku korzystania z zestawu .NET SDK usługi Azure Cosmos DB
description: Używanie funkcji, takich jak rejestrowania po stronie klienta i inne narzędzia innych firm do identyfikacji, diagnozowanie i rozwiązywanie problemów usługi Azure Cosmos DB, korzystając z zestawu SDK platformy .NET.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.devlang: c#
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7e48809537acc21edbcf12d299a333df486c258f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257150"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnozowanie i rozwiązywanie problemów w przypadku korzystania z zestawu .NET SDK usługi Azure Cosmos DB
W tym artykule omówiono typowe problemy, rozwiązania, czynności diagnostycznych i narzędzia, gdy używasz [zestawu .NET SDK](sql-api-sdk-dotnet.md) z kontami interfejsu API SQL usługi Azure Cosmos DB.
Zestaw .NET SDK udostępnia logiczna reprezentacja po stronie klienta do dostępu do interfejsu API SQL usługi Azure Cosmos DB. W tym artykule opisano narzędzia i podejścia, aby pomóc, jeśli napotkasz problemy.

## <a name="checklist-for-troubleshooting-issues"></a>Lista kontrolna dotycząca rozwiązywania problemów:
Przed przeniesieniem aplikacji do środowiska produkcyjnego, należy wziąć pod uwagę poniższej listy kontrolnej. Korzystanie z listy kontrolnej uniemożliwi kilka typowych problemów, które można napotkać. Możesz również szybko zdiagnozować po wystąpieniu problemu:

*   Użyj najnowszej [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). Zestawy SDK w wersji zapoznawczej nie należy używać w środowisku produkcyjnym. Pozwoli to uniknąć osiągnięcia znanych problemów, które zostały już usunięte.
*   Przegląd [porady dotyczące wydajności](performance-tips.md)i postępuj zgodnie z sugerowane rozwiązania. Pomoże to uniknąć skalowanie, opóźnienia i inne problemy z wydajnością.
*   Włącz rejestrowanie zestawu SDK, aby pomóc w rozwiązaniu problemu. Włączanie rejestrowania może wpłynąć na wydajność, najlepiej ją włączyć tylko wtedy, gdy Rozwiązywanie problemów z. Możesz włączyć następujące dzienniki:
    *   [Metryki logowania](monitor-accounts.md) przy użyciu witryny Azure portal. Portal metryki pokazują telemetrii usługi Azure Cosmos DB, co jest przydatne w celu ustalenia, czy problem odnosi się do usługi Azure Cosmos DB, lub jeśli po stronie klienta.
    *   Dziennik [ciąg diagnostyki](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) z punktu odpowiedzi operacji.
    *   Dziennik [metryki zapytania SQL](sql-api-query-metrics.md) z odpowiedzi zapytania 
    *   Postępuj zgodnie z ustawieniami [rejestrowanie zestawu SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Przyjrzyj się [typowe problemy i rozwiązania problemu](#common-issues-workarounds) sekcję w tym artykule.

Sprawdź [GitHub problemy z sekcji](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) , jest aktywnie monitorowane. Sprawdź, jeśli już złożono wszelkie podobne problemy związane z obejście tego problemu. Jeśli nie możesz znaleźć rozwiązania, następnie pliku problem w usłudze GitHub. Możesz otworzyć znacznik pomocy technicznej do pilnych problemów.


## <a name="common-issues-workarounds"></a>Typowe problemy i rozwiązania

### <a name="general-suggestions"></a>Ogólne sugestie
* Uruchom aplikację w tym samym regionie platformy Azure jako konto usługi Azure Cosmos DB, jeśli to możliwe. 
* Mogą występować problemy z łącznością/dostępnością powodu braku zasobów na komputerze klienckim. Zalecamy monitorowanie swoje użycie procesora CPU na węzłów z uruchomionym klientem usługi Azure Cosmos DB i skalowanie w górę/przekraczają limit, jeśli są one uruchamiane w dużym obciążeniem.

### <a name="check-the-portal-metrics"></a>Sprawdź metrykami w portalu
Sprawdzanie [metrykami w portalu](monitor-accounts.md) pomoże określić, jeśli jest to problem po stronie klienta lub jeśli występuje problem z usługą. Na przykład jeśli metryki zawierają wysoki stopień limited współczynnika żądań (kod stanu HTTP 429) co oznacza, że żądanie jest ograniczany Sprawdź [zbyt duża liczba żądań] sekcji. 

### <a name="request-timeouts"></a>Przekroczenia limitu czasu żądania
RequestTimeout się dzieje zazwyczaj, gdy za pomocą bezpośrednich/TCP, ale może zdarzyć się to w trybie bramy. Są to typowe przyczyny znane i sugestie dotyczące sposobu rozwiązania problemu.

* Użycie procesora CPU jest wysokie, które będą powodować opóźnienia i/lub przekroczenia limitu czasu żądania. Klient może skalować w górę maszyny hosta w celu nadania mu więcej zasobów lub obciążenia mogą być rozproszone między więcej maszyn.
* Gniazda / dostępność portu, być może brakuje. Korzystając z zestawów SDK platformy .NET, wydanych przed wersja 2.0, klientów działających na platformie Azure można trafień [Wyczerpanie portów w usłudze Azure SNAT (PAT)]. Ten przykład Dlaczego zalecane jest zawsze uruchomić najnowszą wersję zestawu SDK.
* Tworzenie wielu wystąpień DocumentClient może prowadzić do rywalizacji o zasoby połączenia i problemy limitu czasu. Postępuj zgodnie z [porady dotyczące wydajności](performance-tips.md)i używać jednego wystąpienia DocumentClient przez cały proces.
* Użytkownicy widzą czasami z podwyższonym poziomem uprawnień przekroczeń limitu czasu żądania lub opóźnienia, ponieważ ich kolekcje są aprowizowane niewystarczająco, serwer zaplecza ogranicza żądania, a klient ponawia próbę wewnętrznie bez dzięki czemu są ujawniane to do obiektu wywołującego. Sprawdź [metrykami w portalu](monitor-accounts.md).
* Usługa Azure Cosmos DB dystrybuuje ogólną przepływność aprowizowana równomiernie na fizyczne partycje. Sprawdź portal metryk w celu sprawdzenia, jeśli obciążenie napotyka warstwę gorącą [klucza partycji](partition-data.md). Spowoduje to łączny wykorzystanych przepływność (RU/s) być wydaje się być pod zainicjowanych jednostek zarezerwowanych, ale to pojedyncza partycja używane przepływność (RU/s) przekroczy aprowizowaną przepływność. 
* Ponadto 2.0 SDK dodaje semantyki kanału do połączeń bezpośrednich/TCP. Jedno połączenie TCP jest używany dla wielu żądań, w tym samym czasie. Może to prowadzić do dwa problemy w szczególnych przypadkach:
    * Wysoki stopień współbieżności może prowadzić do rywalizacji o zasoby w kanale.
    * Dużych żądań lub odpowiedzi może prowadzić do głowy wiersza blokowania na kanale i wzmocnienia rywalizacji, nawet w przypadku stosunkowo niska stopień współbieżności.
    * Jeśli tak, który znajduje się w żadnej z tych dwóch kategorii (lub podejrzewa się wysokie wykorzystanie procesora CPU), są możliwe środki zaradcze:
        * Spróbuj przeprowadzić skalowanie w górę/Wy aplikacji.
        * Ponadto dzienników zestawu SDK, można przechwycić za pomocą [odbiornik śledzenia](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) Aby uzyskać więcej szczegółów.

### <a name="connection-throttling"></a>Ograniczanie przepustowości połączenia
Ograniczanie przepustowości połączenia może się zdarzyć z powodu limitu połączeń na komputerze hosta. Wcześniejszych niż 2.0, klientów działających na platformie Azure można trafień [Wyczerpanie portów w usłudze Azure SNAT (PAT)].

### <a name="snat"></a>Wyczerpanie portów w usłudze Azure SNAT (PAT)

Jeśli Twoja aplikacja jest wdrożona w usłudze Azure Virtual Machines bez publicznego adresu IP domyślnie [porty Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) nawiązywać połączenia z dowolnego punktu końcowego poza maszyną Wirtualną. Liczba połączeń z maszyny Wirtualnej do punktu końcowego usługi Azure Cosmos DB jest ograniczona przez [konfiguracji Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Azure SNAT porty są używane tylko wtedy, gdy maszyna wirtualna ma prywatny adres IP i próbuje nawiązać połączenie z publicznym adresem IP procesu z maszyny Wirtualnej. Istnieją dwa obejścia problemu, aby uniknąć ograniczenia SNAT platformy Azure:

* Dodawanie punktu końcowego usługi Azure Cosmos DB do podsieci sieci wirtualnej usługi Azure Virtual Machines. Aby uzyskać więcej informacji, zobacz [punktów końcowych usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Po włączeniu punktu końcowego usługi żądania są już wysyłane z publicznego adresu IP do usługi Azure Cosmos DB. Zamiast tego wysyłane sieć wirtualną i podsieć tożsamości. Ta zmiana może spowodować spadnie zapory, jeśli tylko publiczne adresy IP są dozwolone. Jeśli używana jest zapora, po włączeniu punktu końcowego usługi, dodać podsieć do zapory przy użyciu [wirtualnego ACL sieci](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Przypisz publiczny adres IP do maszyny Wirtualnej platformy Azure.

### <a name="http-proxy"></a>Serwer proxy HTTP
Jeśli używasz serwera proxy HTTP, upewnij się, może obsługiwać liczbę połączeń, skonfigurowanych w zestawie SDK `ConnectionPolicy`.
W przeciwnym razie napotkasz problemy z połączeniem.

### Zbyt duża liczba żądań<a name="request-rate-too-large"></a>
"Liczba za duża żądań" lub kod błędu 429, wskazuje że żądania są ograniczane, ponieważ zużyta przepływność (RU/s) został przekroczony aprowizowanej przepływności. Zestaw SDK będzie automatycznie ponawiać próbę żądania na podstawie [zasady ponawiania](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Jeśli ten błąd często, należy rozważyć zwiększenie przepływności na kolekcję. Sprawdź [metryki portalu](use-metrics.md) aby zobaczyć, jeśli występują błędy 429. Przejrzyj swoje [klucza partycji](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) aby upewnić się, czego wynikiem równomiernego rozłożenia wolumin magazynu i żądania. 

### <a name="slow-query-performance"></a>Wydajność wolnych zapytań
[Zapytanie względem metryk](sql-api-query-metrics.md) pomoże określić, gdzie zapytanie spędza większość czasu. Z metryk zapytań widać, ile z nich jest poświęcony vs zaplecza klienta.
* Jeśli kwerenda zaplecza zwraca szybko i zużywa duże czas na kliencie Sprawdź obciążenia na maszynie. Istnieje prawdopodobieństwo, że nie ma wystarczającej ilości zasobów i zestaw SDK oczekuje na zasoby była możliwa obsługa odpowiedzi.
* Jeśli zapytanie zaplecza jest powolne, spróbuj [optymalizacji kwerendy](optimize-cost-queries.md) i przeglądając bieżące [zasad indeksowania](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Zbyt duża liczba żądań]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Wyczerpanie portów w usłudze Azure SNAT (PAT)]: #snat
[Production check list]: #production-check-list


