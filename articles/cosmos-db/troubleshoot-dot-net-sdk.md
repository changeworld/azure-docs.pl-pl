---
title: Diagnozowanie i rozwiązywanie problemów podczas korzystania z zestawu .NET SDK usługi Azure Cosmos DB
description: Użyj funkcji, takich jak rejestrowanie po stronie klienta i innych narzędzi innych firm do identyfikowania, diagnozowania i rozwiązywania problemów z usługą Azure Cosmos DB podczas korzystania z zestawu .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137958"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnozowanie i rozwiązywanie problemów podczas korzystania z zestawu .NET SDK usługi Azure Cosmos DB
W tym artykule opisano typowe problemy, obejścia, kroki diagnostyczne i narzędzia podczas korzystania z [zestawu .NET SDK](sql-api-sdk-dotnet.md) z kontami interfejsu API sql usługi Azure Cosmos DB.
Zestaw SDK platformy .NET zapewnia logiczną reprezentację po stronie klienta w celu uzyskania dostępu do interfejsu API SQL usługi Azure Cosmos DB. W tym artykule opisano narzędzia i podejścia pomocne w przypadku napotkania jakichkolwiek problemów.

## <a name="checklist-for-troubleshooting-issues"></a>Lista kontrolna rozwiązywania problemów:
Należy wziąć pod uwagę następującą listę kontrolną przed przeniesieniem aplikacji do produkcji. Korzystanie z listy kontrolnej zapobiegnie kilku typowym problemom, które mogą być widoczne. Można również szybko zdiagnozować, gdy wystąpi problem:

*    Użyj najnowszego [SDK](sql-api-sdk-dotnet-standard.md). W wersji zapoznawczej nie należy używać do produkcji. Zapobiegnie to trafieniu znanych problemów, które zostały już naprawione.
*    Zapoznaj się ze [wskazówkami dotyczącymi skuteczności](performance-tips.md)i postępuj zgodnie z sugerowanymi praktykami. Pomoże to zapobiec skalowanie, opóźnienia i inne problemy z wydajnością.
*    Włącz rejestrowanie SDK, aby ułatwić rozwiązywanie problemu. Włączenie rejestrowania może mieć wpływ na wydajność, dlatego najlepiej włączyć ją tylko podczas rozwiązywania problemów. Można włączyć następujące dzienniki:
    *    [Rejestrowanie metryk](monitor-accounts.md) przy użyciu witryny Azure portal. Metryki portalu pokazują dane telemetryczne usługi Azure Cosmos DB, co jest przydatne do określenia, czy problem odpowiada usłudze Azure Cosmos DB lub jeśli jest od strony klienta.
    *    Zarejestruj [ciąg diagnostyki](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) w SDK V2 lub [diagnostyki](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) w V3 SDK z odpowiedzi operacji punktu.
    *    Rejestrowanie [metryk zapytań SQL](sql-api-query-metrics.md) ze wszystkich odpowiedzi na kwerendy 
    *    Postępuj zgodnie z konfiguracją [rejestrowania SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Zapoznaj się z [sekcją Typowe problemy i obejścia](#common-issues-workarounds) w tym artykule.

Sprawdź [sekcję Problemy z usługą GitHub,](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) która jest aktywnie monitorowana. Sprawdź, czy podobny problem z obejściem jest już zgłoszony. Jeśli nie znalazłeś rozwiązania, zgłań problem z githubą. Możesz otworzyć znacznik pomocy technicznej w pilnych sprawach.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Typowe problemy i ich rozwiązania

### <a name="general-suggestions"></a>Ogólne sugestie
* Uruchom aplikację w tym samym regionie platformy Azure, co konto usługi Azure Cosmos DB, gdy tylko jest to możliwe. 
* Z powodu braku zasobów na komputerze klienckim mogą wystąpić problemy z łącznością/dostępnością. Firma Microsoft zaleca monitorowanie wykorzystania procesora CPU w węzłach z uruchomionym klientem usługi Azure Cosmos DB i skalowanie w górę/na zewnątrz, jeśli są one uruchomione przy dużym obciążeniu.

### <a name="check-the-portal-metrics"></a>Sprawdź metryki portalu
Sprawdzanie [metryk portalu](monitor-accounts.md) pomoże ustalić, czy jest to problem po stronie klienta lub czy występuje problem z usługą. Na przykład jeśli metryki zawierają wysoką liczbę żądań o ograniczonej szybkości (kod stanu HTTP 429), co oznacza, że żądanie jest coraz ograniczone, a następnie sprawdź zbyt dużą sekcję [Szybkość żądania.] 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Limity czasu żądań
RequestTimeout zwykle dzieje się podczas korzystania z direct/TCP, ale może się zdarzyć w trybie bramy. Te błędy są powszechnie znane przyczyny i sugestie, jak rozwiązać problem.

* Wykorzystanie procesora CPU jest wysokie, co spowoduje opóźnienie i/lub limity czasu żądania. Klient może skalować komputer-host, aby nadać mu więcej zasobów lub obciążenie może być dystrybuowane na większej liczbie komputerów.
* Dostępność gniazda / portu może być niska. Podczas uruchamiania na platformie Azure klienci korzystający z narzędzia .NET SDK mogą nacisnąć na wyczerpanie portów usługi Azure SNAT (PAT). Aby zmniejszyć ryzyko wystąpienia tego problemu, należy użyć najnowszej wersji 2.x lub 3.x sdk .NET. Jest to przykład, dlaczego zaleca się zawsze uruchamiać najnowszą wersję SDK.
* Tworzenie wielu wystąpień DocumentClient może prowadzić do rywalizacji o połączenie i problemów z limitem czasu. Postępuj zgodnie [ze wskazówkami dotyczącymi wydajności](performance-tips.md)i użyj pojedynczego wystąpienia DocumentClient w całym procesie.
* Użytkownicy czasami widzą podwyższone opóźnienie lub żądania limity czasu, ponieważ ich kolekcje są nieuwzwolone niewystarczająco, back-end ograniczania żądań i klient ponawia wewnętrzne. Sprawdź [metryki portalu](monitor-accounts.md).
* Usługa Azure Cosmos DB rozdziela ogólną aprowizowaną przepływność równomiernie między partycjami fizycznymi. Sprawdź metryki portalu, aby sprawdzić, czy obciążenie napotyka [klucz partycji.](partition-data.md) Spowoduje to, że agregowana przepływność zużywana (RU/s) będzie być wyświetlana w obszarze aprowizowanych jednostek ru, ale pojedyncza partycja zużywana przepływność (RU/s) przekroczy aprowizowaną przepływność. 
* Ponadto 2.0 SDK dodaje semantyki kanału do połączeń direct/TCP. Jedno połączenie TCP jest używane dla wielu żądań w tym samym czasie. Może to prowadzić do dwóch kwestii w szczególnych przypadkach:
    * Wysoki stopień współbieżności może prowadzić do rywalizacji na kanale.
    * Duże żądania lub odpowiedzi może prowadzić do head-of-line blokowania na kanale i zaostrzyć rywalizacji, nawet przy stosunkowo niskim stopniu współbieżności.
    * Jeśli sprawa mieści się w żadnej z tych dwóch kategorii (lub jeśli podejrzewa się wysokie wykorzystanie procesora CPU), są to możliwe środki zaradcze:
        * Spróbuj skalować aplikację w górę/w górę.
        * Ponadto dzienniki SDK mogą być przechwytywane za pośrednictwem [trace listener,](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) aby uzyskać więcej szczegółów.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Duże opóźnienia w sieci
Duże opóźnienie sieci można zidentyfikować za pomocą [ciągu diagnostyki](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) w V2 SDK lub [diagnostyki](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) w V3 SDK.

Jeśli nie występują żadne [limity czasu,](#request-timeouts) a diagnostyka pokazuje pojedyncze żądania, w których duże opóźnienie jest widoczne na różnicy między `ResponseTime` i `RequestStartTime`, jak tak (>300 milisekund w tym przykładzie):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

To opóźnienie może mieć wiele przyczyn:

* Aplikacja nie jest uruchomiona w tym samym regionie co konto usługi Azure Cosmos DB.
* Konfiguracja [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) lub [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) jest niepoprawna i próbuje połączyć się z innym regionem, w którym aplikacja jest aktualnie uruchomiona.
* Może istnieć wąskie gardło w interfejsie sieciowym ze względu na duży ruch. Jeśli aplikacja jest uruchomiona na maszynach wirtualnych platformy Azure, istnieją możliwe obejścia:
    * Rozważ użycie [maszyny wirtualnej z włączoną obsługą przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Włącz [przyspieszoną sieć na istniejącej maszynie wirtualnej](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Rozważ użycie [maszyny wirtualnej wyższego końca](../virtual-machines/windows/sizes.md).

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Wyczerpanie portu Azure SNAT (PAT)

Jeśli aplikacja jest wdrażana na [maszynach wirtualnych platformy Azure bez publicznego adresu IP,](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)domyślnie [porty Usługi Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) ustanawiają połączenia z dowolnym punktem końcowym poza maszyną wirtualną. Liczba połączeń dozwolonych z maszyny Wirtualnej do punktu końcowego usługi Azure Cosmos DB jest ograniczona przez [konfigurację usługi Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Taka sytuacja może prowadzić do ograniczania połączeń, zamykania połączeń lub wyżej wymienionych [limitów czasu żądania.](#request-timeouts)

 Porty SNAT platformy Azure są używane tylko wtedy, gdy maszyna wirtualna ma prywatny adres IP łączy się z publicznym adresem IP. Istnieją dwa obejścia, aby uniknąć ograniczenia usługi Azure SNAT (pod warunkiem, że już używasz pojedynczego wystąpienia klienta w całej aplikacji):

* Dodaj punkt końcowy usługi Usługi Azure Cosmos DB do podsieci sieci wirtualnej maszyn wirtualnych platformy Azure. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi azure virtual network](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Gdy punkt końcowy usługi jest włączony, żądania nie są już wysyłane z publicznego adresu IP do usługi Azure Cosmos DB. Zamiast tego są wysyłane tożsamości sieci wirtualnej i podsieci. Ta zmiana może spowodować spadek zapory, jeśli dozwolone są tylko publiczne serwery IP. Jeśli używasz zapory, po włączeniu punktu końcowego usługi, dodaj podsieć do zapory przy użyciu [list ACL sieci wirtualnej](../virtual-network/virtual-networks-acl.md).
* Przypisywanie [publicznego adresu IP do maszyny Wirtualnej platformy Azure](../load-balancer/load-balancer-outbound-connections.md#assignilpip).

### <a name="http-proxy"></a>Serwer proxy HTTP
Jeśli używasz serwera proxy HTTP, upewnij się, że może on obsługiwać liczbę połączeń skonfigurowanych w pliku SDK `ConnectionPolicy`.
W przeciwnym razie występują problemy z połączeniem.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Stawka żądania jest zbyt duża
"Szybkość żądania zbyt duża" lub kod błędu 429 wskazuje, że żądania są ograniczane, ponieważ zużyta przepływność (RU/s) przekroczyła [aprowizowaną przepływność](set-throughput.md). SDK automatycznie ponowi żądania na podstawie określonych [zasad ponawiania.](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet) Jeśli często pojawia się ten błąd, należy rozważyć zwiększenie przepływności w kolekcji. Sprawdź [metryki portalu,](use-metrics.md) aby sprawdzić, czy otrzymujesz 429 błędów. Przejrzyj [klucz partycji,](partitioning-overview.md#choose-partitionkey) aby upewnić się, że powoduje równomierną dystrybucję woluminu magazynu i żądania. 

### <a name="slow-query-performance"></a>Niska wydajność kwerendy
[Metryki kwerendy](sql-api-query-metrics.md) pomogą określić, gdzie kwerenda spędza większość czasu. Z metryk kwerendy można zobaczyć, ile z nich jest wydawana na zapleczu vs klienta.
* Jeśli kwerenda zaplecza zwraca szybko i spędza dużo czasu na kliencie sprawdzić obciążenie na komputerze. Jest prawdopodobne, że nie ma wystarczającej ilości zasobów i SDK oczekuje na zasoby, które mają być dostępne do obsługi odpowiedzi.
* Jeśli kwerenda zaplecza jest powolna, spróbuj [zoptymalizować kwerendę](optimize-cost-queries.md) i przyjrzeć się bieżącej [zasad indeksowania](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Stawka żądania jest zbyt duża]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


