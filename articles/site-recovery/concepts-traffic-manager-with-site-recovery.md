---
title: Menedżer ruchu Azure z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania usługi Azure Traffic Manager z usługą Azure Site Recovery do migrowania i odzyskiwania po awarii
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: d5b8887d4013f688cd20a0b2e4f6c0dbd5bdc9b6
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071363"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Usługa Azure Traffic Manager z usługą Azure Site Recovery

Usługi Azure Traffic Manager pozwala kontrolować dystrybucję ruchu między punktami końcowymi aplikacji. Punkt końcowy jest internetowy usługi hostowanej wewnątrz lub na zewnątrz Azure.

Menedżer ruchu używa systemu nazw domen (DNS) do klienta żądania kierowane do najbardziej odpowiedniego punktu końcowego, na podstawie metody routingu ruchu i kondycji punktów końcowych. Menedżer ruchu udostępnia szereg [metody routingu ruchu](../traffic-manager/traffic-manager-routing-methods.md) i [punktu końcowego opcje monitorowania](../traffic-manager/traffic-manager-monitoring.md) do własnych potrzeb różnych aplikacji i modele automatycznej pracy awaryjnej. Klienci łączą się z wybranego punktu końcowego bezpośrednio. Traffic Manager nie jest serwer proxy lub bramy, a nie widzi ruch przekazywanie między klientem a usługą.

W tym artykule opisano, jak można łączyć Monitor ruchu Azure inteligentnego routingu z funkcjami migracji i odzyskiwania po awarii zaawansowane usługi Azure Site Recovery.

## <a name="on-premises-to-azure-failover"></a>Dane lokalne na trybu failover systemu Azure

Dla pierwszego scenariusza, należy wziąć pod uwagę **firmy A** mający swoją infrastrukturę aplikacji uruchomionych w jej w środowisku lokalnym. Ze względów biznesowych ciągłości i zgodności **firmy A** decyduje o tym użyć usługi Azure Site Recovery do ochrony jego zastosowań.

**Firmy A** jest uruchamianie aplikacji za pomocą publiczne punkty końcowe i potrzebuje możliwości bezproblemowo przekierowywania ruchu sieciowego na platformie Azure w przypadku awarii. [Priorytet](../traffic-manager/traffic-manager-configure-priority-routing-method.md) metody routingu ruchu w usłudze Azure Traffic Manager umożliwia firmy A łatwe Implementowanie tego wzorca trybu failover.

Instalatora jest następująca:
- **Firmy A** tworzy [profilu usługi Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Przy użyciu **priorytet** metody routingu **firmy A** tworzy dwa punkty końcowe — **głównej** dla lokalnego i **pracy awaryjnej** dla platformy Azure. **Podstawowy** jest przypisany priorytet 1 i **pracy awaryjnej** jest przypisany priorytet 2.
- Ponieważ **głównej** punktu końcowego znajduje się poza Azure, punkt końcowy zostanie utworzona jako [zewnętrznych](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) punktu końcowego.
- Azure Site Recovery usługi Azure site nie ma żadnych maszyn wirtualnych lub aplikacje uruchomione przed trybu failover. Tak **pracy awaryjnej** jako również jest tworzony punkt końcowy **zewnętrznych** punktu końcowego.
- Domyślnie ruchu użytkowników jest kierowane do aplikacji lokalnych, ponieważ ten punkt końcowy ma najwyższy priorytet skojarzonych z nim. Żaden ruch jest kierowany do platformy Azure, jeśli **głównej** punktu końcowego jest w dobrej kondycji.

![W lokalnym do Azure przed trybu failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

W przypadku awarii, mogą wyzwalać firmy A [pracy awaryjnej](site-recovery-failover.md) na platformie Azure i Odzyskaj jego aplikacje na platformie Azure. Jeśli usługi Azure Traffic Manager wykryje, że **głównej** punktu końcowego nie jest już działa prawidłowo, automatycznie używa **pracy awaryjnej** punkt końcowy w odpowiedzi DNS i użytkownicy łączą się odzyskanymi na Azure.

![Na lokalnym do Azure po trybu failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

W zależności od wymagań biznesowych **firmy A** można wybrać wyższy lub niższy [częstotliwość sondowania](../traffic-manager/traffic-manager-monitoring.md) można przełączać się między lokalnymi na platformie Azure w przypadku awarii i upewnij się, minimalnym czasem przestoju dla użytkowników.

Znajdujące się po awarii, **firmy A** można powrotu po awarii z platformy Azure do jej w środowisku lokalnym ([VMware](vmware-azure-failback.md) lub [funkcji Hyper-V](hyper-v-azure-failback.md)) przy użyciu usługi Azure Site Recovery. Teraz, kiedy usługi Traffic Manager wykrycia, że **głównej** punktu końcowego jest w dobrej kondycji ponownie, automatycznie wykorzystuje **głównej** punkt końcowy w odpowiedzi DNS.

## <a name="on-premises-to-azure-migration"></a>Dane lokalne na Azure migracji

Oprócz odzyskiwania po awarii, umożliwia również usługi Azure Site Recovery [migracje Azure](migrate-overview.md). Przy użyciu usługi Azure Site Recovery zaawansowanych test trybu failover, klienci mogą ocenić wydajność aplikacji na platformie Azure bez wpływu na ich w środowisku lokalnym. I gdy klienci będą gotowe do migracji, można wybrać migrację obciążeń całego razem lub wybrać do migracji i stopniowo skalowania.

Azure Menedżera ruchu [ważone](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) metody routingu mogą służyć do kierowania część ruchu przychodzącego na platformie Azure podczas kierowania większość do środowiska lokalnego. Takie podejście może pomóc w ocenie skalowania wydajności, jak można kontynuować zwiększenie waga przypisana do platformy Azure, podczas migracji i więcej obciążeń na platformie Azure.

Na przykład **firmy B** wybierze do migracji w fazach, przeniesienie niektórych jego środowiska aplikacji przy zachowaniu rest lokalną. Podczas początkowych etapów po większość środowiska lokalnego, większe wagi jest przypisany do środowiska lokalnego. Menedżer ruchu zwraca punkt końcowy według wagi przypisane do dostępnych punktów końcowych.

![Migracja na lokalnym do Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Podczas migracji oba punkty końcowe są aktywne, a większość ruch jest kierowany do środowiska lokalnego. W trakcie wykonywania migracji większych wagi można przypisać do punktu końcowego na platformie Azure i na koniec lokalnego punktu końcowego może być wyłączone po migracji.

## <a name="azure-to-azure-failover"></a>Tryb failover Azure do platformy Azure

W tym przykładzie należy wziąć pod uwagę **C firmy** mający swoją infrastrukturę aplikacji systemem Azure. Ze względów biznesowych ciągłości i zgodności **C firmy** decyduje o tym użyć usługi Azure Site Recovery do ochrony jego zastosowań.

**Firma C** jest uruchamianie aplikacji za pomocą publiczne punkty końcowe i chce możliwość bezproblemowo przekierowania ruchu w innym regionie Azure, w przypadku awarii. [Priorytet](../traffic-manager/traffic-manager-configure-priority-routing-method.md) Metoda routingu ruchu umożliwia **C firmy** łatwe Implementowanie tego wzorca trybu failover.

Instalatora jest następująca:
- **Firma C** tworzy [profilu usługi Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Przy użyciu **priorytet** metody routingu **C firmy** tworzy dwa punkty końcowe — **głównej** dla regionu źródła (Azja Wschodnia Azure) i **trybuFailover** dla regionu odzyskiwania (Azja południowo-wschodnia Azure). **Podstawowy** jest przypisany priorytet 1 i **pracy awaryjnej** jest przypisany priorytet 2.
- Ponieważ **głównej** punktu końcowego jest hostowana na platformie Azure, punkt końcowy może być jako [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) punktu końcowego.
- Azure Site Recovery usługi Azure site recovery nie ma żadnych maszyn wirtualnych lub aplikacje uruchomione przed trybu failover. Tak **pracy awaryjnej** można utworzyć punktu końcowego jako [zewnętrznych](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) punktu końcowego.
- Domyślnie ruchu użytkowników jest kierowany do źródłowej aplikacji region (Azja Wschodnia) zgodnie z tego punktu końcowego ma najwyższy priorytet skojarzonych z nim. Żaden ruch jest kierowany do regionu odzyskiwania, jeśli **głównej** punktu końcowego jest w dobrej kondycji.

![Azure do Azure przed trybu failover](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

W przypadku awarii **C firmy** może wyzwolić [pracy awaryjnej](azure-to-azure-tutorial-failover-failback.md) i Odzyskaj jego aplikacje na odzyskiwania region platformy Azure. Gdy usługi Azure Traffic Manager wykryje, że podstawowy punkt końcowy nie jest już działa prawidłowo, automatycznie używa **pracy awaryjnej** punkt końcowy w odpowiedzi DNS i użytkownicy łączą się odzyskać odzyskiwania (region platformy Azure Azja południowo-wschodni).

![Azure do Azure po trybu failover](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

W zależności od wymagań biznesowych **C firmy** można wybrać wyższy lub niższy [częstotliwość sondowania](../traffic-manager/traffic-manager-monitoring.md) można przełączać się między regionami źródła i odzyskiwania i upewnij się, minimalnym czasem przestoju dla użytkowników.

Znajdujące się po awarii, **C firmy** można powrotu po awarii z odzyskiwania region platformy Azure w źródle region platformy Azure przy użyciu usługi Azure Site Recovery. Teraz, kiedy usługi Traffic Manager wykrycia, że **głównej** punktu końcowego jest w dobrej kondycji ponownie, automatycznie wykorzystuje **głównej** punkt końcowy w odpowiedzi DNS.

## <a name="protecting-multi-region-enterprise-applications"></a>Ochrona aplikacji w przypadku przedsiębiorstw

Globalne przedsiębiorstwa często udoskonalanie klienta przez dostosowywanie aplikacjom regionalnych potrzebami. Lokalizacja i zmniejszenia opóźnień może prowadzić do podziału w regionach infrastruktury aplikacji. Przedsiębiorstwa są również powiązane prawem danych regionalnych w niektórych obszarach i wybrać do izolowania części infrastruktury aplikacji w granicach regionalnych.  

Zastanówmy się przykładem gdzie **D firmy** została podzielona jego punkty końcowe aplikacji do obsługi oddzielnie Niemczech, a reszta świata. **Firma D** korzysta z usługi Azure Traffic Manager [Geographic](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) metody routingu tej konfiguracji. Cały ruch pochodzący z Niemcy zostaje skierowany do **punkt końcowy 1** , a wszelkie dane pochodzące spoza Niemcy zostaje skierowany do **punkt końcowy 2**.

Problem z tej instalacji jest Jeśli **punkt końcowy 1** przestaje działać z jakiejkolwiek przyczyny, nie istnieje żadne przekierowywanie ruchu do **punkt końcowy 2**. Ruch pochodzący z Niemcy nadal będą kierowane do **punkt końcowy 1** niezależnie od kondycji punktu końcowego, pozostawiając niemieckim użytkowników bez dostępu do **D firmy**w aplikacji. Podobnie jeśli **punkt końcowy 2** przechodzi w tryb offline, nie istnieje żadne przekierowywanie ruchu do **punkt końcowy 1**.

![W przypadku aplikacji przed](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Aby uniknąć działa w ten problem i zapewnić odporność aplikacji **D firmy** używa [zagnieżdżonych profilów usługi Traffic Manager](../traffic-manager/traffic-manager-nested-profiles.md) z usługą Azure Site Recovery. Ustawienia profilu zagnieżdżone ruch jest nie kierowane do poszczególnych punktów końcowych, ale zamiast tego do innych profilów usługi Traffic Manager. Oto, jak działa ta konfiguracja:
- Zamiast przy użyciu routingu geograficznej z poszczególnych punktów końcowych, **D firmy** używa geograficzne routingu przy użyciu profilów usługi Traffic Manager.
- Korzysta z każdego elementu podrzędnego profilu usługi Traffic Manager **priorytet** routingu z podstawowym i punktu końcowego odzyskiwania, dlatego zagnieżdżanie **priorytet** routingu w **Geographic** routingu.
- Aby włączyć odporność aplikacji, każdy rozkład obciążenia korzysta z usługi Azure Site Recovery do trybu failover w regionie odzyskiwania na podstawie w przypadku zdarzeń po awarii.
- Element nadrzędny Traffic Manager odbiera zapytanie DNS, są kierowane do odpowiednich podrzędnego menedżera ruchu, który odpowiada na zapytanie z dostępnego punktu końcowego.

![W przypadku aplikacji po](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Na przykład w przypadku niepowodzenia punktu końcowego w Niemczech centralnej aplikacji można szybko odzyskane do Niemcy północno-wschodnie. Nowy punkt końcowy obsługuje ruch pochodzący z Niemcy z minimalnym czasem przestojów dla użytkowników. Podobnie awarii punktu końcowego w Europie zachodnie są obsługiwane przez odzyskiwanie obciążenie aplikacji Europa Północna, z obsługą usługi Azure Traffic Manager, który przekierowuje DNS do dostępnego punktu końcowego.

Powyższe ustawienia można rozszerzyć, aby obejmują liczbę kombinacji regionu i punktu końcowego wymagane. Menedżer ruchu umożliwia maksymalnie 10 poziomów zagnieżdżonych profilów i nie zezwala na pętle w zagnieżdżonych konfiguracji.

## <a name="recovery-time-objective-rto-considerations"></a>Zagadnienia dotyczące celu czasu (RTO) odzyskiwanie

W większości organizacji dodawania lub modyfikowania rekordów DNS jest obsługiwany przez oddzielnego zespołu lub osoby spoza organizacji. Dzięki temu zadanie Zmienianie rekordy DNS bardzo trudne. Czas potrzebny do aktualizowania rekordów DNS przez inne zespoły lub organizacje zarządzania infrastrukturą DNS różni się w zależności od organizacji i ma wpływ na RTO aplikacji.

Korzystając z Menedżera ruchu, możesz frontload pracy wymaganej aktualizacji DNS. W tym czasie rzeczywistego uruchamiania trybu failover jest wymagana żadna akcja ręczna lub na skryptach. Takie podejście ułatwia szybkie przełączanie (i tym samym obniżenie RTO) oraz unikając kosztowne czasochłonne DNS zmiany w przypadku awarii. Z Menedżera ruchu, nawet w kroku powrotu po awarii jest automatyczny, które w przeciwnym razie musi być zarządzana oddzielnie.

Ustawienie prawidłowego [interwał sondowania](../traffic-manager/traffic-manager-monitoring.md) za pośrednictwem kondycji Interwał podstawowego lub szybkiej kontroli może znacznie obniżyć RTO podczas pracy awaryjnej i zmniejszyć przestoju dla użytkowników.

Ponadto można zoptymalizować DNS czas wygaśnięcia (TTL) wartości dla profilu usługi Traffic Manager. Czas wygaśnięcia jest to wartość, dla którego wpisu DNS będzie buforowana przez klienta. Dla rekordu DNS nie jest monitowany dwukrotnie w ramach zakresu TTL. Każdy rekord DNS ma wartości TTL skojarzonych z nim. Zmniejszenie tej wartości powoduje więcej zapytań DNS do usługi Traffic Manager, ale można zmniejszyć RTO przez wykrycie awarii szybciej.

Wartość TTL wystąpił przez klienta również nie zwiększa Jeśli zwiększa liczbę rozpoznawania nazw DNS między klientem a autorytatywnego serwera DNS. Programy rozpoznawania nazw DNS "odliczanie" czas wygaśnięcia i przekazać tylko na wartości TTL, która odzwierciedla czas, który upłynął, ponieważ rekord był buforowany. Zapewnia to, że rekord DNS pobiera odświeżany po stronie klienta po TTL, niezależnie od liczby rozpoznawania nazw DNS w łańcuchu.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o Menedżerze ruchu [metody routingu](../traffic-manager/traffic-manager-routing-methods.md).
- Dowiedz się więcej o [zagnieżdżonych profilów usługi Traffic Manager](../traffic-manager/traffic-manager-nested-profiles.md).
- Dowiedz się więcej o [monitorowania punktów końcowych](../traffic-manager/traffic-manager-monitoring.md).
- Dowiedz się więcej o [planów odzyskiwania](site-recovery-create-recovery-plans.md) Aby zautomatyzować tryb failover aplikacji.
