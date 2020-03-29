---
title: Usługa Azure Traffic Manager z usługą Azure Site Recovery | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać usługi Azure Traffic Manager z usługą Azure Site Recovery do odzyskiwania po awarii i migracji
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 6c77cd43231d4596535c11564313a0fe90633cdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60947806"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Usługa Azure Traffic Manager z usługą Azure Site Recovery

Usługa Azure Traffic Manager umożliwia kontrolowanie dystrybucji ruchu między punktami końcowymi aplikacji. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią.

Usługa Traffic Manager używa systemu nazw domen (DNS) do kierowania żądań klientów do najbardziej odpowiedniego punktu końcowego, na podstawie metody routingu ruchu i kondycji punktów końcowych. Usługa Traffic Manager udostępnia szereg [metod routingu ruchu](../traffic-manager/traffic-manager-routing-methods.md) oraz [opcji monitorowania punktów końcowych](../traffic-manager/traffic-manager-monitoring.md), które zaspokoją potrzeby różnych aplikacji i modeli automatycznej pracy w trybie failover. Klienci bezpośrednio łączą się z wybranym punktem końcowym. Usługa Traffic Manager nie jest serwerem proxy ani bramą i nie widzi ruchu przechodzącego między klientem a usługą.

W tym artykule opisano, jak połączyć inteligentne routingu usługi Azure Traffic Monitor z zaawansowanymi funkcjami odzyskiwania po awarii i migracji usługi Azure Site Recovery.

## <a name="on-premises-to-azure-failover"></a>Lokalne przejście awaryjne na platformie Azure

W pierwszym scenariuszu należy wziąć pod uwagę **firmę A,** która ma całą swoją infrastrukturę aplikacji działającą w środowisku lokalnym. Ze względu na ciągłość działania i zgodność z **przepisami firma A** decyduje się na użycie usługi Azure Site Recovery w celu ochrony swoich aplikacji.

**Firma A** uruchamia aplikacje z publicznymi punktami końcowymi i chce mieć możliwość bezproblemowego przekierowania ruchu na platformę Azure w przypadku awarii. Metoda routingu ruchu [priorytetowego](../traffic-manager/traffic-manager-configure-priority-routing-method.md) w usłudze Azure Traffic Manager umożliwia firmie A łatwe wdrożenie tego wzorca trybu failover.

Konfiguracja jest następująca:
- **Firma A** tworzy [profil usługi Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Korzystając z metody routingu **priorytet,** **firma A** tworzy dwa punkty końcowe — **podstawowy** dla środowiska lokalnego i **pracy awaryjnej** dla platformy Azure. **Podstawowy** jest przypisany priorytet 1 i **pracy awaryjnej** jest przypisany priorytet 2.
- Ponieważ **podstawowy** punkt końcowy jest obsługiwany poza platformą Azure, punkt końcowy jest tworzony jako [zewnętrzny](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) punkt końcowy.
- W usłudze Azure Site Recovery witryna platformy Azure nie ma żadnych maszyn wirtualnych ani aplikacji uruchomionych przed przeczesyniem pracy awaryjnej. Tak punkt końcowy **trybu failover** jest również tworzony jako **zewnętrzny** punkt końcowy.
- Domyślnie ruch użytkownika jest kierowany do aplikacji lokalnej, ponieważ ten punkt końcowy ma najwyższy priorytet skojarzony z nim. Żaden ruch nie jest kierowany do platformy Azure, jeśli **podstawowy** punkt końcowy jest w dobrej kondycji.

![Platforma lokalna na platformę Azure przed przełączeniem trybu failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

W przypadku awarii firma A może wyzwolić [przebłażenie awaryjne](site-recovery-failover.md) na platformie Azure i odzyskać swoje aplikacje na platformie Azure. Gdy usługa Azure Traffic Manager wykryje, że **podstawowy** punkt końcowy nie jest już w dobrej kondycji, automatycznie używa punktu końcowego **trybu failover** w odpowiedzi DNS i użytkownicy łączą się z aplikacją odzyskaną na platformie Azure.

![Platforma Lokalna do platformy Azure po przemijenie awaryjne](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

W zależności od wymagań biznesowych **firma A** może wybrać wyższą lub niższą [częstotliwość sondowania,](../traffic-manager/traffic-manager-monitoring.md) aby przełączać się między lokalnymi na platformę Azure w przypadku awarii i zapewnić minimalny czas przestoju dla użytkowników.

Gdy awaria jest zawarta, **firma A** może po awarii z platformy Azure do środowiska lokalnego[(VMware](vmware-azure-failback.md) lub [Hyper-V)](hyper-v-azure-failback.md)przy użyciu usługi Azure Site Recovery. Teraz, gdy usługa Traffic Manager wykryje, że **podstawowy** punkt końcowy jest w dobrej kondycji ponownie, automatycznie wykorzystuje **podstawowy** punkt końcowy w swoich odpowiedziach DNS.

## <a name="on-premises-to-azure-migration"></a>Lokalna migracja do platformy Azure

Oprócz odzyskiwania po awarii usługa Azure Site Recovery umożliwia również [migracje na platformę Azure.](migrate-overview.md) Korzystając z zaawansowanych funkcji trybu failover testowych usługi Azure Site Recovery, klienci mogą oceniać wydajność aplikacji na platformie Azure bez wpływu na ich środowisko lokalne. A gdy klienci są gotowi do migracji, mogą wybrać migrację całych obciążeń razem lub wybrać do migracji i skalowania stopniowo.

Metoda routingu [ważonego](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) usługi Azure Traffic Manager może służyć do kierowania części ruchu przychodzącego na platformę Azure, jednocześnie kierując większość do środowiska lokalnego. Takie podejście może pomóc w ocenie wydajności skalowania, ponieważ można nadal zwiększać wagę przypisaną do platformy Azure podczas migracji coraz większej liczby obciążeń na platformę Azure.

Na przykład **firma B** decyduje się na migrację w fazach, przenosząc niektóre swoje środowisko aplikacji, zachowując resztę lokalnie. Podczas początkowych etapów, gdy większość środowiska jest lokalnie, większa waga jest przypisywana do środowiska lokalnego. Menedżer ruchu zwraca punkt końcowy na podstawie wag przypisanych do dostępnych punktów końcowych.

![Migracja lokalna do platformy Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Podczas migracji oba punkty końcowe są aktywne, a większość ruchu jest kierowana do środowiska lokalnego. W miarę migracji większą wagę można przypisać do punktu końcowego na platformie Azure i na koniec lokalnego punktu końcowego można dezaktywować po migracji.

## <a name="azure-to-azure-failover"></a>Azure do pracy awaryjnej platformy Azure

W tym przykładzie należy wziąć pod uwagę **firma C,** która ma całą swoją infrastrukturę aplikacji z uruchomieniu platformy Azure. Ze względu na ciągłość działania i zgodność z przepisami **firma C** decyduje się na użycie usługi Azure Site Recovery do ochrony swoich aplikacji.

**Firma C** uruchamia aplikacje z publicznymi punktami końcowymi i chce mieć możliwość bezproblemowego przekierowania ruchu do innego regionu platformy Azure w przypadku awarii. Metoda routingu ruchu [priorytet](../traffic-manager/traffic-manager-configure-priority-routing-method.md) umożliwia **firmie C** łatwo zaimplementować ten wzorzec pracy awaryjnej.

Konfiguracja jest następująca:
- **Firma C** tworzy [profil usługi Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Korzystając z metody routingu **priorytetu,** **firma C** tworzy dwa punkty końcowe — podstawowy dla regionu **źródłowego** (Azure East Asia) i **pracy awaryjnej** dla regionu odzyskiwania (Azure Southeast Asia). **Podstawowy** jest przypisany priorytet 1 i **pracy awaryjnej** jest przypisany priorytet 2.
- Ponieważ **podstawowy** punkt końcowy jest obsługiwany na platformie Azure, punkt końcowy może być jako punkt końcowy [platformy Azure.](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints)
- W usłudze Azure Site Recovery lokacja platformy Azure odzyskiwania nie ma żadnych maszyn wirtualnych lub aplikacji uruchomionych przed przeczesyniem pracy awaryjnej. Tak punkt końcowy **trybu failover** można utworzyć jako [zewnętrzny](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) punkt końcowy.
- Domyślnie ruch użytkownika jest kierowany do aplikacji regionu źródłowego (Azja Wschodnia), ponieważ ten punkt końcowy ma najwyższy priorytet skojarzony z nim. Żaden ruch nie jest kierowany do regionu odzyskiwania, jeśli **podstawowy** punkt końcowy jest w dobrej kondycji.

![Platforma Azure na platformę Azure przed przełączeniem awaryjnym](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

W przypadku awarii **firma C** może wyzwolić pracy [awaryjnej](azure-to-azure-tutorial-failover-failback.md) i odzyskać swoje aplikacje w regionie platformy Azure odzyskiwania. Gdy usługa Azure Traffic Manager wykryje, że podstawowy punkt końcowy nie jest już w dobrej kondycji, automatycznie używa punktu końcowego **trybu failover** w odpowiedzi DNS i użytkownicy łączą się z aplikacją odzyskaną w regionie platformy Azure odzyskiwania (Azja Południowo-Wschodnia).

![Platforma Azure na platformę Azure po przemijenie awaryjne](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

W zależności od wymagań biznesowych **firma C** może wybrać wyższą lub niższą [częstotliwość sondowania,](../traffic-manager/traffic-manager-monitoring.md) aby przełączać się między regionami źródłowymi i odzyskiwania i zapewnić minimalny czas przestoju dla użytkowników.

Gdy awaria jest zawarta, **firma C** może po awarii z odzyskiwania regionu platformy Azure do źródłowego regionu platformy Azure przy użyciu usługi Azure Site Recovery. Teraz, gdy usługa Traffic Manager wykryje, że **podstawowy** punkt końcowy jest w dobrej kondycji ponownie, automatycznie wykorzystuje **podstawowy** punkt końcowy w swoich odpowiedziach DNS.

## <a name="protecting-multi-region-enterprise-applications"></a>Ochrona wieloregionowych aplikacji dla przedsiębiorstw

Globalne przedsiębiorstwa często poprawiają jakość obsługi klienta, dostosowując swoje aplikacje do potrzeb regionalnych. Lokalizacja i zmniejszenie opóźnienia może prowadzić do infrastruktury aplikacji podzielone między regiony. Przedsiębiorstwa są również związane regionalnymi przepisami dotyczącymi danych w niektórych obszarach i decydują się na odizolowanie części infrastruktury aplikacji w granicach regionalnych.  

Rozważmy przykład, w którym **firma D** podzieliła swoje punkty końcowe aplikacji, aby oddzielnie obsługiwać Niemcy i resztę świata. **Firma D** korzysta z metody routingu [geograficznego](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) usługi Azure Traffic Manager, aby to skonfigurować. Każdy ruch pochodzący z Niemiec jest kierowany do **punktu końcowego 1,** a każdy ruch pochodzący spoza Niemiec jest kierowany do **punktu końcowego 2**.

Problem z tą konfiguracją polega na tym, że jeśli **punkt końcowy 1** przestanie działać z jakiegokolwiek powodu, nie ma przekierowania ruchu do **punktu końcowego 2**. Ruch pochodzący z Niemiec jest nadal kierowany do **punktu końcowego 1,** niezależnie od kondycji punktu końcowego, pozostawiając niemieckich użytkowników bez dostępu do aplikacji firmy D.Traffic originating from Germany continues to be directed to Endpoint 1 regardless of the health of the endpoint, leaving German users without access to **Company D's**application. Podobnie, jeśli **punkt końcowy 2** przejdzie w tryb offline, nie ma przekierowania ruchu do **punktu końcowego 1**.

![Aplikacja wieloregiona przed](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Aby uniknąć wystąpienia tego problemu i zapewnić odporność aplikacji, **firma D** używa [zagnieżdżonych profilów usługi Traffic Manager](../traffic-manager/traffic-manager-nested-profiles.md) za pomocą usługi Azure Site Recovery. W konfiguracji profilu zagnieżdżonego ruch nie jest kierowany do poszczególnych punktów końcowych, ale do innych profilów usługi Traffic Manager. Oto jak działa ta konfiguracja:
- Zamiast korzystać z routingu geograficznego z poszczególnymi punktami końcowymi, **firma D** używa routingu geograficznego z profilami usługi Traffic Manager.
- Każdy podrzędny profil usługi Traffic Manager korzysta z routingu **priorytetu** z punktem końcowym podstawowym i odzyskiwania, a tym samym zagnieżdżanie routingu **priorytetu** w ramach routingu **geograficznego.**
- Aby włączyć odporność aplikacji, każda dystrybucja obciążenia korzysta z usługi Azure Site Recovery do pracy awaryjnej w regionie odzyskiwania na podstawie przypadku awarii.
- Gdy nadrzędny Menedżer ruchu odbiera kwerendę DNS, jest on kierowany do odpowiedniego podrzędnego menedżera ruchu, który odpowiada na kwerendę z dostępnym punktem końcowym.

![Aplikacja wieloregiona po](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Na przykład jeśli punkt końcowy w Niemczech Central nie powiedzie się, aplikacja może być szybko odzyskane do Niemiec północno-wschodniej. Nowy punkt końcowy obsługuje ruch pochodzący z Niemiec przy minimalnym przestoju dla użytkowników. Podobnie awaria punktu końcowego w Europie Zachodniej może być obsługiwana przez odzyskiwanie obciążenia aplikacji w Europie Północnej, przy czym usługa Azure Traffic Manager obsługuje przekierowania DNS do dostępnego punktu końcowego.

Powyższą konfigurację można rozszerzyć, aby uwzględnić dowolną liczbę wymaganych kombinacji regionu i punktu końcowego. Usługa Traffic Manager umożliwia maksymalnie 10 poziomów profili zagnieżdżonych i nie zezwala na pętle w konfiguracji zagnieżdżonej.

## <a name="recovery-time-objective-rto-considerations"></a>Zagadnienia dotyczące celu czasu odzyskiwania (RTO)

W większości organizacji dodawanie lub modyfikowanie rekordów DNS jest obsługiwane przez oddzielny zespół lub przez osobę spoza organizacji. To sprawia, że zadanie zmiany rekordów DNS jest bardzo trudne. Czas aktualizacji rekordów DNS przez inne zespoły lub organizacje zarządzające infrastrukturą DNS różni się w zależności od organizacji i ma wpływ na czas pracy aplikacji.

Korzystając z usługi Traffic Manager, można ładować z przodu pracę wymaganą dla aktualizacji DNS. W czasie rzeczywistego trybu failover nie jest wymagana żadna akcja ręczna ani skryptowa. Takie podejście pomaga w szybkim przełączaniu (a tym samym obniżaniu RTO), a także unikaniu kosztownych czasochłonnych błędów zmiany DNS w przypadku awarii. W usłudze Traffic Manager nawet krok powrotu po awarii jest zautomatyzowany, co w przeciwnym razie musiałoby być zarządzane oddzielnie.

Ustawienie prawidłowego [interwału sondowania](../traffic-manager/traffic-manager-monitoring.md) za pomocą podstawowych lub szybkich kontroli kondycji interwału może znacznie obniżyć czas pracy wdrówce podczas pracy awaryjnej i skrócić czas przestoju użytkowników.

Można dodatkowo zoptymalizować wartość czasu wygaśnięcia dns (TTL) dla profilu usługi Traffic Manager. TTL jest wartością, dla której wpis DNS będzie buforowany przez klienta. Dla rekordu DNS nie będzie wyszukiwany dwa razy w zakresie czasu wygaśnięcia. Z każdym rekordem DNS jest skojarzony czas wygaśnięcia. Zmniejszenie tej wartości powoduje więcej zapytań DNS do Usługi Traffic Manager, ale można zmniejszyć RTO, odnajdując awarie szybciej.

Czas wygaśnięcia doświadczany przez klienta również nie zwiększa się, jeśli liczba programów rozpoznawania nazw DNS między klientem a autorytatywnym serwerem DNS wzrasta. Programy rozpoznawania nazw DNS "odliczają" czas wygaśnięcia i przekazują tylko wartość czasu wygaśnięcia, która odzwierciedla czas, jaki upłynął od momentu buforowania rekordu. Gwarantuje to, że rekord DNS zostanie odświeżony na kliencie po czasie wygaśnięcia, niezależnie od liczby programów rozpoznawania nazw DNS w łańcuchu.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metodach routingu](../traffic-manager/traffic-manager-routing-methods.md)usługi Traffic Manager .
- Dowiedz się więcej o [zagnieżdżonych profilach usługi Traffic Manager](../traffic-manager/traffic-manager-nested-profiles.md).
- Dowiedz się więcej o [monitorowaniu punktów końcowych](../traffic-manager/traffic-manager-monitoring.md).
- Dowiedz się więcej o [planach odzyskiwania](site-recovery-create-recovery-plans.md) w celu zautomatyzowania pracy awaryjnej aplikacji.
