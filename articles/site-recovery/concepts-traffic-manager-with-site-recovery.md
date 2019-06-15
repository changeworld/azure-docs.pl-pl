---
title: Usługa Azure Traffic Manager za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Opisuje sposób używania usługi Azure Traffic Manager z usługą Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 6c77cd43231d4596535c11564313a0fe90633cdb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60947806"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Usługa Azure Traffic Manager z usługą Azure Site Recovery

Usługa Azure Traffic Manager umożliwia kontrolowanie dystrybucji ruchu między punktami końcowymi usługi aplikacji. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią.

Usługi Traffic Manager używa systemu nazw domen (DNS), aby kierować żądania klientów do najbardziej odpowiednich punktów końcowych na podstawie metody routingu ruchu i kondycji punktów końcowych. Usługa Traffic Manager udostępnia szereg [metod routingu ruchu](../traffic-manager/traffic-manager-routing-methods.md) oraz [opcji monitorowania punktów końcowych](../traffic-manager/traffic-manager-monitoring.md), które zaspokoją potrzeby różnych aplikacji i modeli automatycznej pracy w trybie failover. Klienci łączą się z wybranego punktu końcowego bezpośrednio. Usługa Traffic Manager nie jest serwer proxy lub bramy, a nie widzi ruch przekazywanie między klientem a usługą.

W tym artykule opisano, jak łączenie, monitorowanie ruchu Azure inteligentnego routingu z odzyskiwaniem zaawansowane usługi Azure Site Recovery i możliwości migracji.

## <a name="on-premises-to-azure-failover"></a>Środowiska lokalnego do trybu failover systemu Azure

Dla pierwszego scenariusza, należy wziąć pod uwagę **firmy A** ma swoją infrastrukturę aplikacji działające w jej w środowisku lokalnym. Ze względów biznesowych ciągłości i zgodności **firmy A** zdecyduje się użyć usługi Azure Site Recovery do ochrony jego aplikacji.

**Firmy A** są uruchamiane aplikacje z publicznymi punktami końcowymi i chce możliwość bezproblemowo przekierowywania ruchu sieciowego na platformę Azure, w przypadku awarii. [Priorytet](../traffic-manager/traffic-manager-configure-priority-routing-method.md) metody routingu ruchu w usłudze Azure Traffic Manager umożliwia firmie A, aby łatwo zaimplementować ten wzorzec pracy awaryjnej.

Instalator jest w następujący sposób:
- **Firmy A** tworzy [profilu usługi Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Przy użyciu **priorytet** metody routingu **firmy A** tworzy dwa punkty końcowe — **podstawowego** lokalnych i **trybu Failover** dla platformy Azure. **Podstawowy** jest przypisany priorytet 1 i **trybu Failover** jest przypisany priorytet 2.
- Ponieważ **podstawowego** punktu końcowego znajduje się poza systemem Azure, punkt końcowy zostanie utworzony jako [zewnętrznych](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) punktu końcowego.
- Usługa Azure Site Recovery usługi Azure site nie ma żadnych maszyn wirtualnych lub aplikacje uruchomione przed trybu failover. Dlatego **trybu Failover** punktu końcowego jest tworzona jako **zewnętrznych** punktu końcowego.
- Domyślnie użytkownik ruch będzie kierowany do aplikacji lokalnych, ponieważ punkt końcowy ma najwyższy priorytet, które są skojarzone z nią. Żaden ruch będzie kierowany do platformy Azure, jeśli **głównej** punkt końcowy jest w dobrej kondycji.

![W lokalnym — z platformą Azure przed włączeniem trybu failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

W przypadku awarii, możesz wyzwolić firmy A [trybu failover](site-recovery-failover.md) na platformie Azure i odzyskiwać swoje aplikacje na platformie Azure. Gdy usługi Azure Traffic Manager wykryje, że **podstawowego** punktu końcowego już nie jest w dobrej kondycji, automatycznie używa **trybu Failover** punktu końcowego w odpowiedzi DNS i użytkownicy łączą się odzyskać na Azure.

![W lokalnym — z platformą Azure po włączeniu trybu failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

W zależności od wymagań biznesowych **firmy A** można wybrać wyższe lub niższe [częstotliwość sondowania](../traffic-manager/traffic-manager-monitoring.md) przełączać się między lokalną na platformę Azure w przypadku awarii w celu zapewnienia minimalnym czasem przestojów dla użytkowników.

Gdy znajduje się po awarii, **firmy A** można przeprowadzić powrotu po awarii z platformy Azure do jej w środowisku lokalnym ([VMware](vmware-azure-failback.md) lub [funkcji Hyper-V](hyper-v-azure-failback.md)) przy użyciu usługi Azure Site Recovery. Teraz, gdy Traffic Manager wykryje, że **podstawowego** punkt końcowy jest w dobrej kondycji ponownie, automatycznie wykorzystuje **głównej** punktu końcowego w swojej odpowiedzi DNS.

## <a name="on-premises-to-azure-migration"></a>Lokalnych do migracji na platformę Azure

Oprócz odzyskiwania po awarii umożliwia również usługi Azure Site Recovery [migracji na platformę Azure](migrate-overview.md). Za pomocą usługi Azure Site Recovery zaawansowane testu trybu failover możliwości, klientów można ocenić wydajność aplikacji na platformie Azure bez wpływu na ich w środowisku lokalnym. A gdy klienci są gotowe do migracji, może wybrać migrowane razem całego obciążenia lub zdecydować się na migrację i stopniowo Skaluj.

Azure Traffic Manager [ważona](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) metody routingu opartego na może służyć do kierowania część ruchu przychodzącego na platformie Azure podczas nakazywania większość do środowiska lokalnego. Takie podejście może pomóc w ocenie skalowanie wydajności, w miarę postępu, zwiększając waga przypisana do platformy Azure, jak migrować coraz większym obciążeń na platformie Azure.

Na przykład **firmy B** wybierze do migracji w fazach, przeniesienie niektórych jego środowisko aplikacji przy zachowaniu rest w środowisku lokalnym. Podczas początkowych etapów w przypadku większości środowiska w środowisku lokalnym, większa waga jest przypisany do środowiska lokalnego. Usługi Traffic manager zwraca punkt końcowy, w oparciu o przypisane do punktów końcowych dostępne wagi.

![Migracja na lokalnym — z platformą Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Podczas migracji zarówno punkty końcowe są aktywne, a większość ruch będzie kierowany do środowiska lokalnego. W trakcie wykonywania migracji większa waga można przypisać do punktu końcowego na platformie Azure, a na koniec punktu końcowego w środowisku lokalnym mogą być wyłączone po migracji.

## <a name="azure-to-azure-failover"></a>Tryb failover do platformy

W tym przykładzie należy wziąć pod uwagę **C firmy** ma swoją infrastrukturę aplikacji uruchomionych na platformie Azure. Ze względów biznesowych ciągłości i zgodności **C firmy** zdecyduje się użyć usługi Azure Site Recovery do ochrony jego aplikacji.

**Firma C** są uruchamiane aplikacje z publicznymi punktami końcowymi i chce możliwość bezproblemowo przekierowywanie ruchu do innego regionu platformy Azure w przypadku awarii. [Priorytet](../traffic-manager/traffic-manager-configure-priority-routing-method.md) Metoda routingu ruchu pozwala **C firmy** łatwo zaimplementować ten wzorzec pracy awaryjnej.

Instalator jest w następujący sposób:
- **Firma C** tworzy [profilu usługi Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Przy użyciu **priorytet** metody routingu **C firmy** tworzy dwa punkty końcowe — **podstawowego** dla regionu źródłowego (Azja Wschodnia platformy Azure) i **trybuFailover** w regionie odzyskiwania (Azja południowo-wschodnia platformy Azure). **Podstawowy** jest przypisany priorytet 1 i **trybu Failover** jest przypisany priorytet 2.
- Ponieważ **podstawowego** punktu końcowego jest hostowany na platformie Azure, punkt końcowy może być jako [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) punktu końcowego.
- Za pomocą usługi Azure Site Recovery usługi Azure site recovery nie ma wszystkie maszyny wirtualne lub aplikacje uruchomione przed trybu failover. Dlatego **trybu Failover** punktu końcowego mogą być tworzone jako [zewnętrznych](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) punktu końcowego.
- Domyślnie użytkownika ruch będzie kierowany do źródłowej aplikacji region (Azja Wschodnia), ponieważ punkt końcowy ma najwyższy priorytet, które są skojarzone z nią. Żaden ruch będzie kierowany do regionu odzyskiwania, jeśli **głównej** punkt końcowy jest w dobrej kondycji.

![Azure – Azure przed włączeniem trybu failover](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

W przypadku awarii **C firmy** może wyzwalać [trybu failover](azure-to-azure-tutorial-failover-failback.md) i odzyskiwanie aplikacji niezbędnych do prowadzenia odzyskiwania region platformy Azure. Gdy usługa Azure Traffic Manager wykryje, że podstawowego punktu końcowego już nie jest w dobrej kondycji, automatycznie używa **trybu Failover** punktu końcowego w odpowiedzi DNS i użytkownicy łączą się odzyskać odzyskiwania (region platformy Azure Azja południowo-wschodnia).

![Azure – Azure po włączeniu trybu failover](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

W zależności od wymagań biznesowych **C firmy** można wybrać wyższe lub niższe [częstotliwość sondowania](../traffic-manager/traffic-manager-monitoring.md) przełączać się między regiony źródłowy i odzyskiwania w celu zapewnienia minimalnym czasem przestojów dla użytkowników.

Gdy znajduje się po awarii, **C firmy** można powrotu po awarii z odzyskiwania region platformy Azure do źródła region platformy Azure przy użyciu usługi Azure Site Recovery. Teraz, gdy Traffic Manager wykryje, że **podstawowego** punkt końcowy jest w dobrej kondycji ponownie, automatycznie wykorzystuje **głównej** punktu końcowego w swojej odpowiedzi DNS.

## <a name="protecting-multi-region-enterprise-applications"></a>Ochrona aplikacji dla przedsiębiorstw w wielu regionach

Często przedsiębiorstw poprawy jakości obsługi klienta, dopasowując swoje aplikacje, aby spełniać potrzeby regionalnego. Lokalizacja i zmniejszenia opóźnień może prowadzić do infrastruktury aplikacji, które podzielone między regionami. Przedsiębiorstwa również są kierowane przez przepisy dane regionalne w niektórych obszarach i możliwość izolowania część infrastruktury aplikacji w granicach regionalnych.  

Rozważmy przykład gdzie **D firmy** został podzielony na jego punktów końcowych aplikacji do udostępniania oddzielnie, Niemcy i reszta świata. **Firma D** korzysta z usługi Azure Traffic Manager [Geographic](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) metody routingu, aby skonfigurować tę funkcjonalność. Cały ruch pochodzący z Niemiec zostaje skierowany do **punkt końcowy 1** i cały ruch pochodzący spoza Niemczech zostaje skierowany do **punkt końcowy 2**.

Problem z tej konfiguracji jest że jeśli **punkt końcowy 1** przestaje działać z jakiegokolwiek powodu, nie ma żadnych przekierowywanie ruchu do **punkt końcowy 2**. Ruch pochodzący z Niemiec w dalszym ciągu być kierowane do **punkt końcowy 1** niezależnie od tego, kondycja punktu końcowego, pozostawiając niemieckiego użytkowników bez dostępu do **D firmy**w aplikacji. Podobnie jeśli **punkt końcowy 2** przechodzi do trybu offline, nie ma żadnych przekierowywanie ruchu do **punkt końcowy 1**.

![Aplikacja w wielu regionach przed](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Aby uniknąć uruchamiania tego problemu i zapewnić odporność aplikacji **D firmy** używa [zagnieżdżone profile usługi Traffic Manager](../traffic-manager/traffic-manager-nested-profiles.md) za pomocą usługi Azure Site Recovery. W przypadku instalacji profilu zagnieżdżone jest nie przekierowywany ruch do poszczególnych punktów końcowych, ale zamiast innych profilów usługi Traffic Manager. Poniżej przedstawiono, jak działa ta konfiguracja:
- Zamiast przy użyciu geograficznego routingu za pomocą poszczególne punkty końcowe, **D firmy** używa geograficznego routingu przy użyciu profilów usługi Traffic Manager.
- Korzysta z każdego elementu podrzędnego profilu usługi Traffic Manager **priorytet** routingu za pomocą podstawowy i punkt końcowy odzyskiwania, dlatego zagnieżdżanie **priorytet** routingu w ramach **Geographic** routingu.
- Aby włączyć odporność aplikacji, każdej dystrybucji obciążenia korzysta z usługi Azure Site Recovery do trybu failover w regionie odzyskiwania na podstawie przypadku zdarzenia awarii.
- Nadrzędny usługi Traffic Manager odbiera zapytanie DNS, są kierowane do odpowiedniego elementu podrzędnego usługi Traffic Manager, który odpowiada na kwerendę z dostępnego punktu końcowego.

![Aplikacja w wielu regionach po](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Na przykład w przypadku awarii punktu końcowego w Niemcy środkowe aplikacji można szybko odzyskać do Niemcy północno-wschodnie. Nowy punkt końcowy obsługuje ruch pochodzący z Niemiec przy minimalnych przestojach dla użytkowników. Podobnie awarii punktu końcowego w regionie Europa Zachodnia może być obsługiwany przez odzyskiwanie obciążenia aplikacji do Europa Północna, z obsługą usługi Azure Traffic Manager, który przekierowuje DNS dostępnego punktu końcowego.

Powyższe ustawienia można rozszerzyć, aby obejmują liczbę kombinacji region i punktu końcowego wymagana. Usługa Traffic Manager umożliwia maksymalnie 10 poziomów zagnieżdżonych profilów i nie zezwala na pętli w ramach konfiguracji zagnieżdżonych.

## <a name="recovery-time-objective-rto-considerations"></a>Zagadnienia dotyczące odzyskiwania cel czasu (RTO)

W większości organizacji Dodawanie lub modyfikowanie rekordów DNS jest obsługiwane przez oddzielny zespół lub osoby spoza organizacji. Dzięki temu zmiany rekordów DNS jest bardzo trudne zadanie. Czas potrzebny do aktualizowania rekordów DNS przez inne zespoły lub organizacje zarządzania infrastrukturą DNS różni się od organizacji, a ma wpływ na cel czasu odzyskiwania aplikacji.

Przy użyciu usługi Traffic Manager, możesz to zrobić frontload pracy wymaganej dla aktualizacji DNS. W czasie rzeczywistego uruchamiania trybu failover jest wymagana żadna akcja ręcznie lub za pomocą skryptu. Takie podejście pomaga szybkie przełączanie (i tym samym obniżenie RTO), a także unikanie kosztownych czasochłonne błędów DNS zmiany w zdarzeniu po awarii. Za pomocą usługi Traffic Manager nawet kroku podczas powrotu po awarii jest zautomatyzowane, które w przeciwnym razie musi odbywać się oddzielnie.

Ustawianie prawidłowych [interwał sondowania](../traffic-manager/traffic-manager-monitoring.md) za pośrednictwem podstawowych lub szybkie kondycji kontroli może znacznie obniżyć czas RTO, podczas trybu failover i ograniczyć czas przestoju dla użytkowników.

Ponadto można optymalizować DNS czas wygaśnięcia (TTL) wartości dla profilu usługi Traffic Manager. Czas wygaśnięcia wynosi wartość, dla której będzie buforowana wpis DNS przez klienta. Dla rekordu DNS nie jest monitowany dwukrotnie w ramach zakresu czasu wygaśnięcia. Każdy rekord DNS ma czasu wygaśnięcia skojarzonych z nim. Zmniejszenie tej wartości powoduje więcej zapytań DNS do usługi Traffic Manager, ale mogą zmniejszyć czas RTO, szybsze wykrywanie awarii.

Czas wygaśnięcia odczuwalnych u klienta również nie zwiększa Jeśli zwiększa się liczba rozpoznawania nazw DNS między klientem i autorytatywny serwer DNS. Rozpoznawania nazw DNS "odliczanie" czas wygaśnięcia i przekazywać tylko na podstawie wartości TTL, która odzwierciedla czas, który upłynął, ponieważ rekord był buforowany. Gwarantuje to, że rekord DNS zostanie odświeżony po stronie klienta po czas wygaśnięcia, niezależnie od liczby rozpoznawania nazw DNS w łańcuchu.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o usłudze Traffic Manager [metod routingu](../traffic-manager/traffic-manager-routing-methods.md).
- Dowiedz się więcej o [zagnieżdżone profile usługi Traffic Manager](../traffic-manager/traffic-manager-nested-profiles.md).
- Dowiedz się więcej o [monitorowania punktu końcowego](../traffic-manager/traffic-manager-monitoring.md).
- Dowiedz się więcej o [planów odzyskiwania](site-recovery-create-recovery-plans.md) do automatyzowania trybu failover w aplikacji.
