---
title: Zabezpieczenia sieci dla centrów zdarzeń platformy Azure
description: W tym artykule opisano sposób konfigurowania dostępu z prywatnych punktów końcowych
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: ab85cdb2854de5c147c68afd8e4fe5e17ac2899b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477943"
---
# <a name="network-security-for-azure-event-hubs"></a>Zabezpieczenia sieci dla centrów zdarzeń platformy Azure 
W tym artykule opisano sposób korzystania z następujących funkcji zabezpieczeń w usłudze Azure Event Hubs: 

- Tagi usługi
- Reguły zapory IP
- Punkty końcowe usługi sieciowej
- Prywatne punkty końcowe (wersja zapoznawcza)


## <a name="service-tags"></a>Tagi usługi
Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych. Aby uzyskać więcej informacji o tagach usług, zobacz [Omówienie tagów usługi](../virtual-network/service-tags-overview.md).

Za pomocą tagów usługi można zdefiniować kontrolki dostępu do sieci w [sieciowych grupach](../virtual-network/security-overview.md#security-rules) zabezpieczeń lub [Zaporze platformy Azure](../firewall/service-tags.md). Podczas tworzenia reguł zabezpieczeń należy używać tagów usług zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład **EventHub)** w odpowiednim polu *źródłowym* lub *docelowym* reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi.

| Tag usługi | Przeznaczenie | Czy można używać ruchu przychodzącego lub wychodzącego? | Może być regionalny? | Czy można używać z Zaporą platformy Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure Event Hubs. | Wychodzący | Tak | Tak |


## <a name="ip-firewall"></a>Zapora IP 
Domyślnie przestrzenie nazw centrum zdarzeń są dostępne z Internetu, o ile żądanie jest zawiera prawidłowe uwierzytelnianie i autoryzację. Zaporą IP można ją ograniczyć do tylko zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Ta funkcja jest przydatna w scenariuszach, w których usługi Azure Event Hubs powinny być dostępne tylko z niektórych dobrze znanych witryn. Reguły zapory umożliwiają konfigurowanie reguł do akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład jeśli używasz Usługi Event Hubs z [Azure Express Route][express-route], można utworzyć **regułę zapory,** aby zezwolić na ruch tylko z adresów IP infrastruktury lokalnej. 

Reguły zapory IP są stosowane na poziomie obszaru nazw Centrum zdarzeń. W związku z tym reguły mają zastosowanie do wszystkich połączeń z klientami przy użyciu dowolnego obsługiwanego protokołu. Każda próba połączenia z adresu IP, która nie jest zgodna z dozwoloną regułą IP w obszarze nazw Centrum zdarzeń, jest odrzucana jako nieautoryzowana. W odpowiedzi nie wspomina się o regule IP. Reguły filtru IP są stosowane w kolejności, a pierwsza reguła zgodna z adresem IP określa akcję akceptowania lub odrzucania.

Aby uzyskać więcej informacji, zobacz [Jak skonfigurować zaporę IP dla centrum zdarzeń](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Punkty końcowe usługi sieciowej
Integracja centrów zdarzeń z punktami końcowymi usługi [sieć wirtualna (VNet)][vnet-sep] umożliwia bezpieczny dostęp do funkcji obsługi wiadomości z obciążeń, takich jak maszyny wirtualne powiązane z sieciami wirtualnymi, przy czym ścieżka ruchu sieciowego jest zabezpieczona obu końcówek.

Po skonfigurowaniu do powiązanych z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiedni obszar nazw Centrum zdarzeń nie akceptuje już ruchu z dowolnego miejsca, ale autoryzowanych podsieci w sieciach wirtualnych. Z punktu widzenia sieci wirtualnej powiązanie obszaru nazw Centrum zdarzeń z punktem końcowym usługi konfiguruje tunel sieci izolowanej z podsieci sieci wirtualnej do usługi obsługi wiadomości. 

Wynikiem jest prywatna i izolowana relacja między obciążeniami powiązanymi z podsiecią i odpowiednim obszarem nazw Centrum zdarzeń, pomimo obserwowalnego adresu sieciowego punktu końcowego usługi obsługi wiadomości w zakresie publicznego adresu IP. Istnieje wyjątek od tego zachowania. Włączenie punktu końcowego usługi domyślnie `denyall` włącza regułę w [zaporze IP](event-hubs-ip-filtering.md) skojarzonej z siecią wirtualną. Można dodać określone adresy IP w zaporze IP, aby włączyć dostęp do publicznego punktu końcowego Centrum zdarzeń. 

> [!IMPORTANT]
> Sieci wirtualne są obsługiwane w warstwach **Standardowa** i **Dedykowana** usługi Event Hubs. Nie jest obsługiwany w warstwie **podstawowej.**

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Zaawansowane scenariusze zabezpieczeń włączone przez integrację sieci wirtualnej 

Rozwiązania, które wymagają ścisłego i podzielonego zabezpieczeń i w których podsieci sieci wirtualnej zapewniają segmentację między usługami podzielonymi na przedziały, nadal potrzebują ścieżek komunikacji między usługami zamieszkałymi w tych przedziałach.

Każda bezpośrednia trasa IP między przedziałami, w tym przewożącym HTTPS przez protokół TCP/IP, niesie ze sobą ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej. Usługi obsługi wiadomości zapewniają izolowane ścieżki komunikacji, gdzie wiadomości są nawet zapisywane na dysku podczas przechodzenia między stronami. Obciążenia w dwóch różnych sieciach wirtualnych, które są powiązane z tym samym wystąpieniem centrum zdarzeń, mogą komunikować się wydajnie i niezawodnie za pośrednictwem wiadomości, podczas gdy integralność granicy izolacji sieci jest zachowywana.
 
Oznacza to, że rozwiązania chmurowe z uwzględnieniem zabezpieczeń nie tylko uzyskują dostęp do wiodących w branży niezawodnych i skalowalnych asynchronicznych funkcji obsługi wiadomości asynchronicznych platformy Azure, ale teraz mogą używać wiadomości do tworzenia ścieżek komunikacji między bezpiecznymi przedziałami rozwiązań, które są z natury bezpieczniejsze niż to, co jest osiągalne w dowolnym trybie komunikacji typu peer-to-peer, w tym HTTPS i innych protokołów gnieździe zabezpieczonych protokołami TLS.

### <a name="bind-event-hubs-to-virtual-networks"></a>Powiąż centra zdarzeń z sieciami wirtualnymi

**Reguły sieci wirtualnej** to funkcja zabezpieczeń zapory, która określa, czy obszar nazw usługi Azure Event Hubs akceptuje połączenia z określonej podsieci sieci wirtualnej.

Powiązanie obszaru nazw Centrum zdarzeń z siecią wirtualną jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi sieci wirtualnej** w podsieci sieci wirtualnej i włączyć go dla **witryny Microsoft.EventHub,** jak wyjaśniono w artykule [przegląd punktu końcowego usługi][vnet-sep]. Po dodaniu punktu końcowego usługi należy powiązać obszar nazw Centrum zdarzeń z regułą **sieci wirtualnej.**

Reguła sieci wirtualnej jest skojarzeniem obszaru nazw Centrum zdarzeń z podsiecią sieci wirtualnej. Gdy reguła istnieje, wszystkie obciążenia powiązane z podsiecią są przyznawane dostęp do obszaru nazw Centrum zdarzeń. Usługa Event Hubs nigdy nie ustanawia połączeń wychodzących, nie musi uzyskiwać dostępu i dlatego nigdy nie uzyskała dostępu do podsieci, włączając tę regułę.

Aby uzyskać więcej informacji, zobacz [Jak skonfigurować punkty końcowe usługi sieci wirtualnej dla centrum zdarzeń](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Prywatne punkty końcowe

[Usługa Azure Private Link](../private-link/private-link-overview.md) umożliwia dostęp do usług platformy Azure (na przykład usługi Azure Event Hubs, Azure Storage i Azure Cosmos DB) oraz usługi klienta/partnera platformy Azure hostowane za pośrednictwem **prywatnego punktu końcowego** w sieci wirtualnej.

Prywatny punkt końcowy to interfejs sieciowy, który łączy cię prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, skutecznie wprowadzając usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, więc nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można połączyć się z wystąpieniem zasobu platformy Azure, co zapewnia najwyższy poziom szczegółowości w kontroli dostępu.

> [!NOTE]
> Ta funkcja jest obsługiwana tylko w **warstwie dedykowanej.** Aby uzyskać więcej informacji na temat warstwy dedykowanej, zobacz [Omówienie dedykowanych centrów zdarzeń](event-hubs-dedicated-overview.md). 
>
> Ta funkcja jest obecnie w **wersji zapoznawczej**. 


Aby uzyskać więcej informacji, zobacz [Jak skonfigurować prywatne punkty końcowe dla Centrum zdarzeń](private-link-service.md)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Jak skonfigurować zaporę IP dla centrum zdarzeń](event-hubs-ip-filtering.md)
- [Jak skonfigurować punkty końcowe usługi sieci wirtualnej dla centrum zdarzeń](event-hubs-service-endpoints.md)
- [Jak skonfigurować prywatne punkty końcowe dla centrum zdarzeń](private-link-service.md)