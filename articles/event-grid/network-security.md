---
title: Zabezpieczenia sieci zasobów usługi Azure Event Grid
description: W tym artykule opisano sposób konfigurowania dostępu z prywatnych punktów końcowych
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300156"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Zabezpieczenia sieci zasobów usługi Azure Event Grid
W tym artykule opisano sposób korzystania z następujących funkcji zabezpieczeń w usłudze Azure Event Grid: 

- Tagi usługi dla wyjścia (wersja zapoznawcza)
- Reguły zapory IP dla ruchu przychodzącego (wersja zapoznawcza)
- Prywatne punkty końcowe dla ruchu przychodzącego (wersja zapoznawcza)


## <a name="service-tags"></a>Tagi usługi
Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych. Aby uzyskać więcej informacji o tagach usług, zobacz [Omówienie tagów usługi](../virtual-network/service-tags-overview.md).

Za pomocą tagów usługi można zdefiniować kontrolki dostępu do sieci w [sieciowych grupach](../virtual-network/security-overview.md#security-rules) zabezpieczeń lub [Zaporze platformy Azure](../firewall/service-tags.md). Podczas tworzenia reguł zabezpieczeń należy używać tagów usług zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład **AzureEventGrid)** w odpowiednim polu *źródłowym* lub *docelowym* reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi.

| Tag usługi | Przeznaczenie | Czy można używać ruchu przychodzącego lub wychodzącego? | Może być regionalny? | Czy można używać z Zaporą platformy Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Usługa AzureEventGrid | Azure Event Grid. <br/><br/>*Uwaga:* Ten tag obejmuje punkty końcowe usługi Azure Event Grid tylko w południowo-środkowej, amerykańskiej, wschodniej, wschodniej części stanów USA 2, u. zachodnie 2 i środkowej w stanach ZJEDNOCZONYCH. | Obie | Nie | Nie |


## <a name="ip-firewall"></a>Zapora IP 
Usługa Azure Event Grid obsługuje formanty dostępu oparte na protokoiści ip do publikowania w tematach i domenach. Za pomocą formantów opartych na protoko: IP można ograniczyć wydawców do tematu lub domeny tylko do zestawu zatwierdzonych zestawów maszyn i usług w chmurze. Ta funkcja uzupełnia [mechanizmy uwierzytelniania](security-authentication.md) obsługiwane przez siatkę zdarzeń.

Domyślnie temat i domena są dostępne z Internetu, o ile żądanie jest wyposażone w prawidłowe uwierzytelnianie i autoryzację. Zaporą IP można ją ograniczyć do tylko zestawu adresów IP lub zakresów adresów IP w notacji [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Wydawcy pochodzący z innego adresu IP zostaną odrzuceni i otrzymają odpowiedź 403 (zabronione).


## <a name="private-endpoints"></a>Prywatne punkty końcowe
Za pomocą [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) można zezwolić na przychynienie się zdarzeń bezpośrednio z sieci wirtualnej do tematów i domen bezpiecznie za pośrednictwem łącza [prywatnego](../private-link/private-link-overview.md) bez przechodzenia przez publiczny Internet. Prywatny punkt końcowy to specjalny interfejs sieciowy dla usługi platformy Azure w sieci wirtualnej. Podczas tworzenia prywatnego punktu końcowego dla tematu lub domeny, zapewnia bezpieczną łączność między klientami w sieci wirtualnej i zasobów usługi Event Grid. Do prywatnego punktu końcowego jest przypisywany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym a usługą Event Grid używa bezpiecznego łącza prywatnego.

![Diagram architektury](./media/network-security/architecture-diagram.png)

Korzystanie z prywatnych punktów końcowych dla zasobu usługi Event Grid umożliwia:

- Bezpieczny dostęp do tematu lub domeny z sieci wirtualnej za pośrednictwem sieci szkieletowej firmy Microsoft, w przeciwieństwie do publicznego Internetu.
- Bezpiecznie połącz się z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu sieci VPN lub ExpressRoutes za pomocą prywatnej komunikacji równorzędnej.

Podczas tworzenia prywatnego punktu końcowego dla tematu lub domeny w sieci wirtualnej żądanie zgody jest wysyłane do zatwierdzenia do właściciela zasobu. Jeśli użytkownik żądający utworzenia prywatnego punktu końcowego jest również właścicielem zasobu, to żądanie zgody zostanie automatycznie zatwierdzone. W przeciwnym razie połączenie jest w stanie **oczekiwania,** dopóki nie zostanie zatwierdzone. Aplikacje w sieci wirtualnej można połączyć się z usługą Siatki zdarzeń za pośrednictwem prywatnego punktu końcowego bezproblemowo, przy użyciu tych samych ciągów połączeń i mechanizmów autoryzacji, które będą używać w przeciwnym razie. Właściciele zasobów mogą zarządzać żądaniami zgody i prywatnymi punktami końcowymi za pośrednictwem karty **Prywatne punkty końcowe** dla zasobu w witrynie Azure portal.

### <a name="connect-to-private-endpoints"></a>Łączenie się z prywatnymi punktami końcowymi
Wydawcy w sieci wirtualnej przy użyciu prywatnego punktu końcowego należy użyć tego samego ciągu połączenia dla tematu lub domeny jako klientów łączących się z publicznym punktem końcowym. Rozpoznawanie DNS automatycznie kieruje połączenia z sieci wirtualnej do tematu lub domeny za pośrednictwem łącza prywatnego. Usługa Event Grid domyślnie tworzy [prywatną strefę DNS](../dns/private-dns-overview.md) dołączoną do sieci wirtualnej z niezbędną aktualizacją dla prywatnych punktów końcowych. Jeśli jednak używasz własnego serwera DNS, może być konieczne wprowadzenie dodatkowych zmian w konfiguracji DNS.

### <a name="dns-changes-for-private-endpoints"></a>Zmiany DNS dla prywatnych punktów końcowych
Podczas tworzenia prywatnego punktu końcowego rekord CNAME DNS dla zasobu jest aktualizowany do aliasu w poddomenie z prefiksem `privatelink`. Domyślnie tworzona jest prywatna strefa DNS, która odpowiada poddomenie łącza prywatnego. 

Po rozwiązaniu tematu lub adresu URL punktu końcowego domeny spoza sieci wirtualnej z prywatnego punktu końcowego, jest rozpoznawany do publicznego punktu końcowego usługi. Rekordy zasobów DNS dla "topicA", po usunięciu **spoza sieci wirtualnej** obsługującej prywatny punkt końcowy, będą następujące:

| Nazwa                                          | Typ      | Wartość                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<profil usługi azure traffic manager\>

Można odmówić lub kontrolować dostęp dla klienta spoza sieci wirtualnej za pośrednictwem publicznego punktu końcowego przy użyciu [zapory IP](#ip-firewall). 

Po rozpoznaniu z sieci wirtualnej obsługującej prywatny punkt końcowy adres URL tematu lub punktu końcowego domeny jest rozpoznawany na adres IP prywatnego punktu końcowego. Rekordy zasobów DNS dla tematu "topicA", po rozwiązaniu z **wewnętrznej sieci wirtualnej** obsługującej prywatny punkt końcowy, będą następujące:

| Nazwa                                          | Typ      | Wartość                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Takie podejście umożliwia dostęp do tematu lub domeny przy użyciu tego samego ciągu połączenia dla klientów w sieci wirtualnej obsługujących prywatne punkty końcowe i klientów spoza sieci wirtualnej.

Jeśli używasz niestandardowego serwera DNS w sieci, klienci mogą rozpoznać nazwę FQDN dla tematu lub punktu końcowego domeny na prywatny adres IP punktu końcowego. Skonfiguruj serwer DNS, aby delegować poddomenę łącza prywatnego do prywatnej `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` strefy DNS sieci wirtualnej, lub skonfiguruj rekordy A przy połącz adres IP prywatnego punktu końcowego.

Zalecana nazwa strefy `privatelink.eventgrid.azure.net`DNS to .

### <a name="private-endpoints-and-publishing"></a>Prywatne punkty końcowe i publikowanie

W poniższej tabeli opisano różne stany połączenia prywatnego punktu końcowego i wpływ na publikowanie:

| Stan połączenia   |  Pomyślnie opublikować (Tak/Nie) |
| ------------------ | -------------------------------|
| Approved (Zatwierdzono)           | Tak                            |
| Odrzucone           | Nie                             |
| Oczekujące            | Nie                             |
| Odłączony       | Nie                             |

Aby publikowanie zakończyło się pomyślnie, należy **zatwierdzić**stan połączenia prywatnego punktu końcowego . Jeśli połączenie zostanie odrzucone, nie można go zatwierdzić przy użyciu witryny Azure portal. Jedyną możliwością jest usunięcie połączenia i utworzenie nowego.

## <a name="pricing-and-quotas"></a>Cennik i przydziały
**Prywatne punkty końcowe** są dostępne tylko z tematami warstwy premium i domenami. Usługa Event Grid umożliwia tworzenie maksymalnie 64 połączeń prywatnych punktów końcowych na temat lub domenę. Aby przejść z warstwy podstawowej do warstwy premium, zobacz artykuł [warstwy cenowej Aktualizowanie.](update-tier.md)

Funkcja **zapory IP** jest dostępna zarówno w warstwach podstawowej, jak i premium w siatce zdarzeń. Zezwalamy na tworzenie maksymalnie 16 reguł zapory IP na temat lub domenę.


## <a name="next-steps"></a>Następne kroki
Zaporę IP dla zasobu usługi Event Grid można skonfigurować, aby ograniczyć dostęp przez publiczny Internet tylko z wybranego zestawu adresów IP lub zakresów adresów IP. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie zapory IP](configure-firewall.md).

Można skonfigurować prywatne punkty końcowe, aby ograniczyć dostęp tylko z wybranych sieci wirtualnych. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie prywatnych punktów końcowych](configure-private-endpoints.md).
