---
title: Diagnozowanie i rozwiązywanie problemów podczas korzystania z zestawu SDK Azure Cosmos DB .NET
description: Korzystaj z funkcji, takich jak rejestrowanie po stronie klienta i innych narzędzi innych firm, aby identyfikować, diagnozować i rozwiązywać problemy Azure Cosmos DB podczas korzystania z zestawu .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 51b37c43b94ad59090f32af0d57bbefaa57f30fa
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932553"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnozowanie i rozwiązywanie problemów podczas korzystania z zestawu SDK Azure Cosmos DB .NET
W tym artykule opisano typowe problemy, obejścia, kroki diagnostyczne i narzędzia używane w przypadku korzystania z [zestawu .NET SDK](sql-api-sdk-dotnet.md) z kontami interfejsu API SQL Azure Cosmos DB.
Zestaw .NET SDK zapewnia logiczną reprezentację po stronie klienta, aby uzyskać dostęp do Azure Cosmos DB interfejsu API SQL. W tym artykule opisano narzędzia i podejścia pomocne w przypadku napotkania problemów.

## <a name="checklist-for-troubleshooting-issues"></a>Lista kontrolna dotycząca rozwiązywania problemów:
Przed przeniesieniem aplikacji do środowiska produkcyjnego należy wziąć pod uwagę poniższą listę kontrolną. Korzystanie z listy kontrolnej uniemożliwi kilka typowych problemów, które mogą zostać wyświetlone. Można również szybko zdiagnozować w przypadku wystąpienia problemu:

*   Użyj najnowszego [zestawu SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). Zestawów SDK wersji zapoznawczych nie należy używać w środowisku produkcyjnym. Uniemożliwi to wyróżnianie znanych problemów, które zostały już naprawione.
*   Zapoznaj się z [poradami dotyczącymi wydajności](performance-tips.md)i postępuj zgodnie z zaleceniami. Pomoże to uniknąć skalowania, opóźnień i innych problemów z wydajnością.
*   Włącz rejestrowanie zestawu SDK, aby pomóc w rozwiązaniu problemu. Włączenie rejestrowania może mieć wpływ na wydajność, dlatego najlepiej je włączyć tylko w przypadku rozwiązywania problemów. Można włączyć następujące dzienniki:
    *   [Rejestruj metryki](monitor-accounts.md) przy użyciu Azure Portal. Metryki portalu pokazują Azure Cosmos DB dane telemetryczne, które ułatwiają określenie, czy problem odnosi się do Azure Cosmos DB, czy też jest po stronie klienta.
    *   Rejestruj [ciąg diagnostyczny](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) z odpowiedzi operacji punktu.
    *   Rejestruj [metryki zapytań SQL](sql-api-query-metrics.md) ze wszystkich odpowiedzi na zapytania 
    *   Postępuj zgodnie z konfiguracją [rejestrowania zestawu SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Zapoznaj się z sekcją [typowe problemy i obejścia](#common-issues-workarounds) w tym artykule.

Zapoznaj się z [sekcją problemów usługi GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) , która jest aktywnie monitorowana. Sprawdź, czy podobny problem z obejściem został już zgłoszony. Jeśli nie znajdziesz rozwiązania, zapoznaj się z tematem problemu z usługą GitHub. Możesz otworzyć cykl pomocy technicznej, aby uzyskać pilne problemy.


## <a name="common-issues-workarounds"></a>Typowe problemy i rozwiązania

### <a name="general-suggestions"></a>Ogólne sugestie
* Uruchom aplikację w tym samym regionie świadczenia usługi Azure jako konto Azure Cosmos DB, jeśli to możliwe. 
* Problemy z łącznością/dostępnością mogą być spowodowane brakiem zasobów na komputerze klienckim. Zalecamy monitorowanie użycia procesora CPU w węzłach z uruchomionym Azure Cosmos DB klienta i skalowanie w górę/w poziomie, jeśli są one uruchamiane przy dużym obciążeniu.

### <a name="check-the-portal-metrics"></a>Sprawdź metryki portalu
Sprawdzanie [metryk portalu](monitor-accounts.md) pomoże określić, czy jest to problem po stronie klienta, czy też występuje problem z usługą. Na przykład jeśli metryki zawierają dużą częstotliwość żądań o ograniczonej szybkości (kod stanu HTTP 429), co oznacza, że żądanie jest ograniczone, sprawdź [Zbyt duży współczynnik żądań] sekcję. 

### <a name="request-timeouts"></a>Limity czasu żądań
RequestTimeout zazwyczaj odbywa się przy użyciu protokołu Direct/TCP, ale może się zdarzyć w trybie bramy. Są to typowe znane przyczyny oraz sugestie dotyczące sposobu rozwiązania problemu.

* Użycie procesora CPU ma wysoką wartość, co spowoduje opóźnienia i/lub przekroczenie limitu czasu żądania. Klient może skalować w górę maszynę hosta w celu zapewnienia większej ilości zasobów, a obciążenie może być dystrybuowane na większej liczbie komputerów.
* Dostępność gniazda/portu może być niska. W przypadku uruchamiania na platformie Azure klienci korzystający z zestawu SDK platformy .NET mogą osiągać wyczerpanie portów z użyciem usługi Azure translatora adresów sieciowych. Aby zmniejszyć prawdopodobieństwo wystąpienia tego problemu, użyj najnowszej wersji 2. x lub 3. x zestawu .NET SDK. Jest to przykład, dlatego zaleca się zawsze uruchomienia najnowszej wersji zestawu SDK.
* Tworzenie wielu wystąpień DocumentClient może prowadzić do problemów związanych z rywalizacją i limitem czasu. Postępuj zgodnie ze [wskazówkami dotyczącymi wydajności](performance-tips.md)i korzystaj z jednego wystąpienia DocumentClient w całym procesie.
* Użytkownicy czasami zobaczą podwyższony czas oczekiwania lub limitów czasu żądania, ponieważ ich kolekcje są w niewystarczający sposób używane jako ograniczenia wewnętrznej bazy danych, a następnie ponawiają próbę klienta bez obsłużenia tego obiektu wywołującego. Sprawdź [metryki portalu](monitor-accounts.md).
* Azure Cosmos DB dystrybuuje ogólnie zainicjowaną przepływność równomiernie między partycjami fizycznymi. Sprawdź metryki portalu, aby sprawdzić, czy w obciążeniu występuje [klucz partycji](partition-data.md)aktywnej. Spowoduje to, że agregowana przepływność (RU/s) będzie wyświetlana w ramach aprowizacji jednostek ru, ale użycie jednej partycji (RU/s) spowoduje przekroczenie alokowanej przepływności. 
* Ponadto zestaw SDK 2,0 dodaje semantykę kanału do połączeń bezpośrednich/TCP. Jedno połączenie TCP jest używane dla wielu żądań w tym samym czasie. Może to prowadzić do dwóch problemów w określonych przypadkach:
    * Wysoki stopień współbieżności może prowadzić do rywalizacji o kanał.
    * Duże żądania lub odpowiedzi mogą prowadzić do blokowania w kanale i zaostrzania rywalizacji nawet przy stosunkowo niskim stopniu współbieżności.
    * Jeśli przypadek występuje w jednej z tych dwóch kategorii (lub jeśli jest podejrzane wysokie wykorzystanie procesora CPU), są to możliwe środki zaradcze:
        * Spróbuj skalować aplikację w górę/w dół.
        * Ponadto dzienniki zestawu SDK mogą być przechwytywane przez [odbiornik śledzenia](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) , aby uzyskać więcej szczegółów.

### <a name="connection-throttling"></a>Ograniczanie połączeń
Możliwe jest ograniczenie połączenia z powodu limitu połączeń na komputerze-hoście. Od poprzedniej do 2,0 klienci korzystający z platformy Azure mogą napotkać [Wyczerpanie portów (z) na platformie Azure].

### <a name="snat"></a>Wyczerpanie portów (z) na platformie Azure

Jeśli aplikacja jest wdrażana na platformie Azure Virtual Machines bez publicznego adresu IP, domyślnie [porty usługi Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) IPSec ustanawiają połączenia z dowolnym punktem końcowym poza maszyną wirtualną. Liczba połączeń dozwolonych między maszyną wirtualną a punktem końcowym Azure Cosmos DB jest ograniczona przez [konfigurację usługi Azure translatora adresów sieciowych](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Porty protokołu IPSec platformy Azure są używane tylko wtedy, gdy maszyna wirtualna ma prywatny adres IP, a proces z maszyny wirtualnej próbuje połączyć się z publicznym adresem IP. Istnieją dwa obejścia, aby uniknąć ograniczenia dotyczącego translatora adresów sieciowych platformy Azure:

* Dodaj punkt końcowy usługi Azure Cosmos DB do podsieci sieci wirtualnej platformy Azure Virtual Machines. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Po włączeniu punktu końcowego usługi żądania nie są już wysyłane z publicznego adresu IP do Azure Cosmos DB. Zamiast tego jest wysyłana tożsamość sieci wirtualnej i podsieci. Ta zmiana może spowodować, że Zapora spadnie, jeśli dozwolone są tylko publiczne adresy IP. Jeśli używasz zapory, po włączeniu punktu końcowego usługi Dodaj podsieć do zapory przy użyciu [list acl Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Przypisz publiczny adres IP do maszyny wirtualnej platformy Azure.

### <a name="http-proxy"></a>Serwer proxy HTTP
W przypadku korzystania z serwera proxy HTTP upewnij się, że może on obsługiwać liczbę połączeń skonfigurowanych w zestawie `ConnectionPolicy`SDK.
W przeciwnym razie nastąpiły problemy z połączeniem.

### Zbyt duży współczynnik żądań<a name="request-rate-too-large"></a>
"Częstotliwość żądań zbyt duża" lub kod błędu 429 wskazuje, że żądania są ograniczane, ponieważ wykorzystana przepływność (RU/s) przekroczyła zainicjowaną przepływność. Zestaw SDK będzie automatycznie ponawiać próbę żądania na podstawie określonych [zasad ponawiania](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Jeśli ten błąd występuje często, należy rozważyć zwiększenie przepływności kolekcji. Sprawdź [metryki portalu](use-metrics.md) , aby sprawdzić, czy są wyświetlane błędy 429. Przejrzyj [klucz partycji](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) , aby upewnić się, że jest to równomierny rozkład magazynu i woluminu żądania. 

### <a name="slow-query-performance"></a>Niska wydajność zapytań
[Metryki zapytań](sql-api-query-metrics.md) pomogą określić, gdzie zapytanie jest spędzane większością czasu. Z metryk zapytania można sprawdzić, jaka część jest używana na zapleczu programu vs a kliencie.
* Jeśli zapytanie zaplecza wraca szybko i spędza na tym dużą godzinę, sprawdź obciążenie maszyny. Prawdopodobnie nie ma wystarczającej ilości zasobów i zestaw SDK oczekuje na dostępność zasobów do obsługi odpowiedzi.
* Jeśli zapytanie zaplecza próbuje [zoptymalizować zapytanie](optimize-cost-queries.md) i przeszukać bieżące [zasady indeksowania](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Zbyt duży współczynnik żądań]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Wyczerpanie portów (z) na platformie Azure]: #snat
[Production check list]: #production-check-list


