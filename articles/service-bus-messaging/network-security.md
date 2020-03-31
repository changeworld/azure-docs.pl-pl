---
title: Zabezpieczenia sieci dla usługi Azure Service Bus
description: W tym artykule opisano funkcje zabezpieczeń sieci, takie jak tagi usług, reguły zapory IP, punkty końcowe usługi i prywatne punkty końcowe.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: aschhab
ms.openlocfilehash: 95f8c2a3b47b59bab7df909be43dacdb1f9c58f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479282"
---
# <a name="network-security-for-azure-service-bus"></a>Zabezpieczenia sieci dla usługi Azure Service Bus 
W tym artykule opisano sposób korzystania z następujących funkcji zabezpieczeń w usłudze Azure Service Bus: 

- Tagi usługi
- Reguły zapory IP
- Punkty końcowe usługi sieciowej
- Prywatne punkty końcowe (wersja zapoznawcza)


## <a name="service-tags"></a>Tagi usługi
Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych. Aby uzyskać więcej informacji o tagach usług, zobacz [Omówienie tagów usługi](../virtual-network/service-tags-overview.md).

Za pomocą tagów usługi można zdefiniować kontrolki dostępu do sieci w [sieciowych grupach zabezpieczeń](../virtual-network/security-overview.md#security-rules) lub [Zaporze platformy Azure](../firewall/service-tags.md). Podczas tworzenia reguł zabezpieczeń należy używać tagów usług zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład **ServiceBus)** w odpowiednim polu *źródłowym* lub *docelowym* reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi.

| Tag usługi | Przeznaczenie | Czy można używać ruchu przychodzącego lub wychodzącego? | Może być regionalny? | Czy można używać z Zaporą platformy Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Ruch usługi Azure Service Bus, który używa warstwy usługi Premium. | Wychodzący | Tak | Tak |


## <a name="ip-firewall"></a>Zapora IP 
Domyślnie obszary nazw usługi Service Bus są dostępne z Internetu, o ile żądanie jest zawiera prawidłowe uwierzytelnianie i autoryzację. Zaporą IP można ją ograniczyć do tylko zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Ta funkcja jest przydatna w scenariuszach, w których usługa Azure Service Bus powinna być dostępna tylko z niektórych dobrze znanych witryn. Reguły zapory umożliwiają konfigurowanie reguł do akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład jeśli używasz usługi Service Bus z [Azure Express Route][express-route], można utworzyć **regułę zapory,** aby zezwolić na ruch tylko z infrastruktury lokalnej adresy IP lub adresy bramy terminala sieciowego firmy. 

Reguły zapory IP są stosowane na poziomie obszaru nazw usługi Service Bus. W związku z tym reguły mają zastosowanie do wszystkich połączeń z klientami przy użyciu dowolnego obsługiwanego protokołu. Każda próba połączenia z adresu IP, która nie jest zgodna z dozwoloną regułą IP w obszarze nazw usługi Service Bus, jest odrzucana jako nieautoryzowana. W odpowiedzi nie wspomina się o regule IP. Reguły filtru IP są stosowane w kolejności, a pierwsza reguła zgodna z adresem IP określa akcję akceptowania lub odrzucania.

Aby uzyskać więcej informacji, zobacz [Jak skonfigurować zaporę IP dla obszaru nazw magistrali usług](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Punkty końcowe usługi sieciowej
Integracja usługi Service Bus z [punktami końcowymi usługi sieci wirtualnej (VNet)](service-bus-service-endpoints.md) umożliwia bezpieczny dostęp do funkcji obsługi wiadomości z obciążeń, takich jak maszyny wirtualne, które są powiązane z sieciami wirtualnymi, przy czym ścieżka ruchu sieciowego jest zabezpieczona na obu końcach.

Po skonfigurowaniu do wiązanych z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiedni obszar nazw usługi Service Bus nie będzie już akceptował ruchu z dowolnego miejsca, ale autoryzowanych sieci wirtualnych. Z punktu widzenia sieci wirtualnej powiązanie obszaru nazw usługi Service Bus z punktem końcowym usługi konfiguruje tunel sieci izolowanej z podsieci sieci wirtualnej do usługi obsługi wiadomości.

Wynikiem jest prywatna i izolowana relacja między obciążeniami powiązanymi z podsiecią i odpowiednim obszarem nazw usługi Service Bus, pomimo obserwowalnego adresu sieciowego punktu końcowego usługi obsługi wiadomości w zakresie publicznego adresu IP.

> [!IMPORTANT]
> Sieci wirtualne są obsługiwane tylko w przestrzeniach nazw usługi Service Bus [warstwy Premium.](service-bus-premium-messaging.md)
> 
> Korzystając z punktów końcowych usługi sieci wirtualnej z usługą Service Bus, nie należy włączać tych punktów końcowych w aplikacjach, które łączą standardowe i premium warstwy obszarów nazw usługi Service Bus. Ponieważ warstwa standardowa nie obsługuje sieci wirtualnych. Punkt końcowy jest ograniczony tylko do obszarów nazw warstwy Premium.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Zaawansowane scenariusze zabezpieczeń włączone przez integrację sieci wirtualnej 

Rozwiązania, które wymagają ścisłego i podzielonego zabezpieczeń i w których podsieci sieci wirtualnej zapewniają segmentację między usługami podzielonymi na przedziały, zazwyczaj nadal potrzebują ścieżek komunikacji między usługami zamieszkałymi w tych przedziałach.

Każda bezpośrednia trasa IP między przedziałami, w tym przewożącym HTTPS przez protokół TCP/IP, niesie ze sobą ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej. Usługi obsługi wiadomości zapewniają całkowicie izolowane ścieżki komunikacji, gdzie wiadomości są nawet zapisywane na dysku podczas przechodzenia między stronami. Obciążenia w dwóch różnych sieciach wirtualnych, które są powiązane z tym samym wystąpieniem usługi Service Bus, mogą komunikować się wydajnie i niezawodnie za pośrednictwem wiadomości, podczas gdy integralność granic izolacji sieci jest zachowywana.
 
Oznacza to, że rozwiązania chmurowe z uwzględnieniem zabezpieczeń nie tylko uzyskują dostęp do wiodących w branży niezawodnych i skalowalnych asynchronicznych funkcji obsługi wiadomości asynchronicznych platformy Azure, ale teraz mogą używać wiadomości do tworzenia ścieżek komunikacji między bezpiecznymi przedziałami rozwiązań, które są z natury bezpieczniejsze niż to, co jest osiągalne w dowolnym trybie komunikacji typu peer-to-peer, w tym HTTPS i innych protokołów gnieździe zabezpieczonych protokołami TLS.

### <a name="bind-service-bus-to-virtual-networks"></a>Powiązanie magistrali usług z sieciami wirtualnymi

*Reguły sieci wirtualnej* to funkcja zabezpieczeń zapory, która określa, czy serwer usługi Azure Service Bus akceptuje połączenia z określonej podsieci sieci wirtualnej.

Powiązanie obszaru nazw usługi Service Bus z siecią wirtualną jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi sieci wirtualnej** w podsieci sieci wirtualnej i włączyć go dla **microsoft.ServiceBus,** jak wyjaśniono w [omówieniu punktu końcowego usługi](service-bus-service-endpoints.md). Po dodaniu punktu końcowego usługi należy powiązać obszar nazw usługi Service Bus z **regułą sieci wirtualnej**.

Reguła sieci wirtualnej jest skojarzeniem obszaru nazw usługi Service Bus z podsiecią sieci wirtualnej. Gdy reguła istnieje, wszystkie obciążenia powiązane z podsiecią mają dostęp do obszaru nazw usługi Service Bus. Sama usługa Service Bus nigdy nie ustanawia połączeń wychodzących, nie musi uzyskiwać dostępu i dlatego nigdy nie uzyskała dostępu do podsieci, włączając tę regułę.

Aby uzyskać więcej informacji, zobacz [Jak skonfigurować punkty końcowe usługi sieci wirtualnej dla obszaru nazw usługi Service Bus](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Prywatne punkty końcowe

Usługa Azure Private Link Service umożliwia dostęp do usług platformy Azure (na przykład usługi Azure Service Bus, Azure Storage i Azure Cosmos DB) oraz usługi klienta/partnera platformy Azure hostowane za pośrednictwem **prywatnego punktu końcowego** w sieci wirtualnej.

Prywatny punkt końcowy to interfejs sieciowy, który łączy cię prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, skutecznie wprowadzając usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, więc nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można połączyć się z wystąpieniem zasobu platformy Azure, co zapewnia najwyższy poziom szczegółowości w kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [Co to jest łącze prywatne platformy Azure?](../private-link/private-link-overview.md)

> [!NOTE]
> Ta funkcja jest obsługiwana w warstwie **premium** usługi Azure Service Bus. Aby uzyskać więcej informacji na temat warstwy premium, zobacz [warstwy obsługi wiadomości usługi Service Bus Premium i Standard.](service-bus-premium-messaging.md)
>
> Ta funkcja jest obecnie w **wersji zapoznawczej**. 


Aby uzyskać więcej informacji, zobacz [Jak skonfigurować prywatne punkty końcowe dla obszaru nazw usługi Service Bus](private-link-service.md)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Jak skonfigurować zaporę IP dla obszaru nazw magistrali usług](service-bus-ip-filtering.md)
- [Jak skonfigurować punkty końcowe usługi sieci wirtualnej dla obszaru nazw usługi Service Bus](service-bus-service-endpoints.md)
- [Jak skonfigurować prywatne punkty końcowe dla obszaru nazw usługi Service Bus](private-link-service.md)
