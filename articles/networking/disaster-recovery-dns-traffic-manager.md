---
title: Odzyskiwanie po awarii przy użyciu usługi Azure DNS i usługi Traffic Manager | Dokumentacja firmy Microsoft
description: Omówienie rozwiązania odzyskiwania po awarii przy użyciu usługi Azure DNS i usługi Traffic Manager.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: a560cc526e73f3ce7e851f2a545f9b16fa53b423
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65501687"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Odzyskiwanie po awarii przy użyciu usług Azure DNS i Traffic Manager

Odzyskiwanie po awarii powinno być skoncentrowane na odzyskiwaniu po poważnej utracie funkcjonalności aplikacji. Aby można było wybrać rozwiązanie odzyskiwania po awarii, właściciele biznesowych i technologicznych należy najpierw ustalić poziom funkcjonalności, wymagany podczas awarii, takie jak - niedostępne, częściowo dostępny za pośrednictwem ograniczoną funkcjonalność lub opóźnione dostępności lub w pełni dostępne.
Większość klientów korporacyjnych wybierają architektura obejmująca wiele regionów w celu zapewnienia odporności na aplikację lub infrastrukturę poziomu trybu failover. Klienci mogą wybrać kilka metod w quest do osiągnięcia trybu failover i wysokiej dostępności poprzez nadmiarowej architekturze. Oto niektóre z popularnych metod:

- **Aktywny / pasywny z zimnym**: W tym rozwiązaniu trybu failover maszyny wirtualne i inne urządzenia, które działają w regionie wstrzymania nie są aktywne, dopóki nie jest wymagane dla trybu failover. Jednak w środowisku produkcyjnym są replikowane w postaci kopii zapasowych, obrazy maszyn wirtualnych lub szablonów usługi Resource Manager, w innym regionie. Ten mechanizm pracy awaryjnej jest to ekonomiczne rozwiązanie, ale zajmuje więcej czasu podjęcia pełną przejścia w tryb failover.
 
    ![Aktywny/pasywny z zimnym](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Rysunek — aktywny/pasywny z konfiguracji odzyskiwania zimny wstrzymania po awarii*

- **Aktywny/pasywny z pilota światła**: W tym rozwiązaniu trybu failover wstrzymania środowiska jest skonfigurowany z minimalną konfiguracją. Instalator ma tylko niezbędne usługi do obsługuje tylko minimalne i krytycznych zbiór aplikacji. W formacie macierzystym w tym scenariuszu można tylko wykonania minimalną liczbę funkcji, ale można skalować w górę i zduplikować dodatkowych usług do zbiorczego obciążenia produkcyjnego w sytuacji, gdy do pracy awaryjnej.
    
    ![Aktywny/pasywny z pilota światła](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Rysunek: Aktywny/pasywny z konfiguracji odzyskiwania po awarii światła pilotażu*

- **Aktywny/pasywny z gotowości ciepło**: W tym rozwiązaniu trybu failover wstrzymania region jest wstępnie warmed i jest gotowy do podstawowego obciążenia, automatycznego skalowania jest włączona i wszystkie wystąpienia zostały włączone i uruchomione. To rozwiązanie nie jest skalowana do podjęcia obciążenia pełnym środowisku produkcyjnym, ale będzie działać, a wszystkie usługi są uruchomione. To rozwiązanie jest rozszerzone wersję podejście światła pilotażu.
    
    ![Aktywny/pasywny z gotowości bez wyłączania zasilania](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Rysunek: Aktywny/pasywny z konfiguracji odzyskiwania po awarii na wstrzymania bez wyłączania zasilania*
    
Aby dowiedzieć się więcej na temat trybu failover i wysokiej dostępności, zobacz [odzyskiwanie po awarii dla aplikacji platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planowanie architektury odzyskiwania po awarii

Istnieją dwa aspekty techniczne do konfigurowania architektury odzyskiwania po awarii:
-  Przy użyciu mechanizmu wdrażania do replikowania wystąpień, danych i konfiguracji między środowiskami podstawowy i zapasowy. Ten typ odzyskiwania po awarii może odbywać się natywnie za pośrednictwem usługi Azure Site-Recovery za pośrednictwem usług Microsoft Azure partner urządzenia/Veritas lub NetApp. 
- Opracowywanie rozwiązań na kierowanie ruchu sieci/sieci web z lokacji głównej do lokacji wstrzymania. Ten typ odzyskiwania po awarii można osiągnąć za pomocą usługi Azure DNS, Manager(DNS) ruchu platformy Azure lub usługi równoważenia obciążenia globalne innych firm.

W tym artykule jest ograniczona do metody za pomocą przekierowania ruchu sieci i sieci Web. Aby uzyskać instrukcje dotyczące konfigurowania usługi Azure Site Recovery, zobacz [dokumentację usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
Serwer DNS jest jeden z najefektywniejszych mechanizmów na kierowanie ruchu sieciowego, ponieważ DNS jest często globalnych i zewnętrzne w stosunku do centrum danych i jest izolowane od błędów poziomu strefy (AZ) regionalne lub dostępności. Umożliwia korzystanie z mechanizmu oparte na systemie DNS trybu failover i na platformie Azure, dwóch usług DNS można wykonać takie same w dowolny sposób — w usłudze Azure DNS (DNS, autorytatywny) i usługi Azure Traffic Manager (oparte na systemie DNS inteligentny routing ruchu w). 

Warto poznać kilka koncepcji w systemie DNS, które są często używane w celu omówienia rozwiązania, zawarte w tym artykule:
- **DNS rekordu A** — rekordy są wskaźnikami, które wskazują domenę na adres IPv4. 
- **Nazwa CNAME lub Canonical** — tego typu rekordu jest używany, aby wskazywał inny rekord DNS. CNAME nie odpowiada za pomocą adresu IP, ale raczej wskaźnik do rekordu, który zawiera adres IP. 
- **Ważona średnia Routing** — jeden można skojarzyć wagę do punktów końcowych usługi, a następnie dystrybucję ruchu, w oparciu o przypisane wagi. Ta metoda routingu jest jednego z czterech ruchu routingu mechanizmów dostępnych w ramach usługi Traffic Manager. Aby uzyskać więcej informacji, zobacz [metody routingu ważonego](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Routing priorytet** — priorytet routingu opiera się na kontrole kondycji punktów końcowych. Domyślnie usługi Azure Traffic manager wysyła cały ruch do najwyższy priorytet punktu końcowego, a po awarii lub po awarii, usługa Traffic Manager kieruje ruchem do pomocniczego punktu końcowego. Aby uzyskać więcej informacji, zobacz [metody routingu opartego na priorytecie](../traffic-manager/traffic-manager-routing-methods.md#priority).

## <a name="manual-failover-using-azure-dns"></a>Ręczna praca awaryjna przy użyciu usługi Azure DNS
Rozwiązanie ręcznej pracy awaryjnej usługi Azure DNS do odzyskiwania po awarii używa standardowego mechanizmu DNS w tryb failover do tworzenia kopii zapasowej lokacji. Opcji ręcznej za pośrednictwem usługi Azure DNS sprawdza się najlepiej, gdy jest używana w połączeniu z zimnym lub podejście światła pilotażu. 

![Ręczna praca awaryjna przy użyciu usługi Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Rysunek - ręcznej pracy awaryjnej przy użyciu usługi Azure DNS*

Dostępne są następujące założenia dotyczące rozwiązania:
- Punkty końcowe podstawowych i pomocniczych mają statyczne adresy IP, które nie zmieniają się często. Załóżmy, że dla lokacji głównej 100.168.124.44 jest adres IP i adres IP dla lokacji dodatkowej 100.168.124.43.
- Strefy DNS platformy Azure nie istnieje dla lokacji głównej i dodatkowej. Załóżmy, że dla lokacji głównej punkt końcowy jest prod.contoso.com i tworzenia kopii zapasowej lokacji jest dr.contoso.com. Rekord DNS dla głównej aplikacji, które są znane jako www\.istnieje również contoso.com.   
- Czas wygaśnięcia jest większa niż określona przez umowę SLA cel czasu odzyskiwania w organizacji. Jeśli na przykład przedsiębiorstwo ustawia RTO odpowiedzi po awarii aplikacji to 60 minut, a następnie czasem wygaśnięcia powinny być mniejsze niż 60 minut, najlepiej małe, tym lepsze. 
  Możesz skonfigurować usługę Azure DNS do ręcznego przełączania trybu failover w następujący sposób:
- Tworzenie strefy DNS
- Tworzenie rekordów strefy DNS
- Zaktualizuj rekord CNAME

### <a name="step-1-create-a-dns"></a>Krok 1: Tworzenie systemu DNS
Tworzenie strefy DNS (na przykład www\.contoso.com) jak pokazano poniżej:

![Tworzenie strefy DNS na platformie Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Rysunek — tworzenie strefy DNS na platformie Azure*

### <a name="step-2-create-dns-zone-records"></a>Krok 2: Tworzenie rekordów strefy DNS

W tej strefie Utwórz trzy rekordy (na przykład - www\.contoso.com i prod.contoso.com dr.consoto.com) jako wyświetlane poniżej.

![Tworzenie rekordów strefy DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Rysunek — tworzenie rekordów w strefie DNS na platformie Azure*

W tym scenariuszu, witryny, www\.contoso.com ma TTL 30 minut, co jest znacznie poniżej podane cel czasu odzyskiwania i wskazuje prod.contoso.com lokacji w środowisku produkcyjnym. Ta konfiguracja jest podczas zwykłych operacjach biznesowych. Czas wygaśnięcia prod.contoso.com i dr.contoso.com ustawiono 300 sekund lub 5 minut. Można użyć monitorowania platformy Azure, usługi, takiej jak Azure Monitor lub usługi Azure App Insights lub partnerskich rozwiązań, takich jak Dynatrace monitorowania, można również użyć głównego organicznie rozwiązania, które można monitorować lub wykrywanie awarii poziomu infrastruktury wirtualnej lub aplikacji.

### <a name="step-3-update-the-cname-record"></a>Krok 3: Zaktualizuj rekord CNAME

Gdy zostanie wykryta awaria, zmień wartość rekordu, aby wskazywał dr.contoso.com, jak pokazano poniżej:
       
![Zaktualizuj rekord CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Rysunek — Zaktualizuj rekord CNAME w systemie Azure*

W ciągu 30 minut, podczas których większość mechanizmów rozpoznawania odświeży pliku pamięci podręcznej strefy dowolnego zapytania www\.contoso.com nastąpi przekierowanie do dr.contoso.com.
Można również uruchomić następujące polecenie interfejsu wiersza polecenia platformy Azure, aby zmienić wartość rekordu CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Ten krok można wykonać ręcznie lub za pośrednictwem usługi automation. Możesz ręcznie za pomocą konsoli lub wiersza polecenia platformy Azure. Zestaw Azure SDK i interfejsu API może służyć do zautomatyzowania aktualizacji rekordu CNAME, dlatego, że jest wymagana żadna interwencja ręczna. Automatyzacja może zostać wbudowana za pośrednictwem usługi Azure functions lub w ramach monitorowania aplikacji innych firm lub nawet ze środowiska lokalnego.

### <a name="how-manual-failover-works-using-azure-dns"></a>Jak ręczne działania trybu failover przy użyciu usługi Azure DNS
Ponieważ serwer DNS jest spoza strefy w trybie failover lub po awarii, jest izolację żadnych przestojów. Umożliwia użytkownikowi architektury scenariusza prosty tryb failover, który jest rozwiązaniem tanim i będzie działać, wszystkie godziny przy założeniu, że operator ma połączenie sieciowe podczas awarii i może być przerzucania. Jeśli rozwiązanie jest uwzględnione w skryptach, następnie jednym upewnij się, że serwera lub usługi, uruchamiając skrypt powinny być izolowane przed problemem mające wpływ na środowisko produkcyjne. Ponadto należy pamiętać niski TTL, która została ustawiona względem strefy, dzięki czemu elementu rozwiązującego świata zachowuje punkt końcowy pamięci podręcznej dla długotrwałych i klienci mogą uzyskiwać dostęp do witryny w ramach czas RTO. Zimnym i projekt pilotażowy światła ponieważ niektóre prewarming i innych działań administracyjnych może być wymagane — jeden należy również podać wystarczającą ilość czasu przed wprowadzeniem przerzucania.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatyczny tryb failover przy użyciu usługi Azure Traffic Manager
W przypadku złożone architektury i wiele zestawów zasobów, które może wykonać tę samą funkcję, można skonfigurować usługi Azure Traffic Manager (oparte na DNS) w celu sprawdzenia kondycji zasobów i kierowanie ruchu z innych dobrej kondycji zasobu do dobrej kondycji zasób. W poniższym przykładzie zarówno w regionie podstawowym, jak i w regionie pomocniczym masz pełne wdrożenie. To wdrożenie obejmuje usługi w chmurze i synchronizacja bazy danych. 

![Automatyczny tryb failover przy użyciu usługi Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Rysunek - automatycznej pracy awaryjnej przy użyciu usługi Azure Traffic Manager*

Jednak region podstawowy jest aktywnie obsługi sieci żądań od użytkowników. Region pomocniczy staje się aktywny tylko wtedy, gdy region podstawowy napotyka przerw w działaniu usługi. W takim przypadku wszystkie nowe żądania sieciowego trasy do regionu pomocniczego. Po wykonaniu kopii zapasowej bazy danych jest niemal natychmiastowe, równoważenia obciążenia mają adresy IP, które mogą być zaznaczone, kondycji i wystąpienia są zawsze aktualne i uruchomiona, ta topologia udostępnia opcję pracę w niski wskaźnik RTO i trybu failover bez żadnej interwencji ręcznej. Region pomocniczy trybu failover musi być gotowy do gotowej do pracy natychmiast po awarii do regionu podstawowego.
Ten scenariusz jest idealne do użycia z usługi Azure Traffic Manager zawierający wbudowanych sondy dla różnych typów kontroli kondycji, w tym http / https i TCP. Usługa Azure Traffic manager ma także aparat reguł, które mogą być skonfigurowane dla trybu failover, gdy wystąpi błąd, zgodnie z poniższym opisem. Rozważmy następujące rozwiązanie przy użyciu usługi Traffic Manager:
- Klient ma punkt końcowy Region nr 1, znane jako prod.contoso.com za pomocą statycznego adresu IP jako 100.168.124.44 i punktem końcowym Region nr 2, znane jako dr.contoso.com za pomocą statycznego adresu IP jako 100.168.124.43. 
-   Każda z tych środowisk jest fronted za pośrednictwem publicznego właściwość umożliwiający dostęp do Internetu, jak moduł równoważenia obciążenia. Moduł równoważenia obciążenia można skonfigurować do ma punkt końcowy oparte na systemie DNS lub w pełni kwalifikowaną nazwę domeny (FQDN), jak pokazano powyżej.
-   Wszystkie wystąpienia w regionie 2 znajdują się w pobliżu replikacji w czasie rzeczywistym z regionu 1. Ponadto obrazy maszyn są aktualne, a wszystkie dane oprogramowania i konfiguracji jest poprawek i są zgodne z regionu 1.  
-   Automatyczne skalowanie jest wstępnie skonfigurowany z wyprzedzeniem. 

Kroki podjęte w celu konfigurowania trybu failover za pomocą usługi Azure Traffic Manager są następujące:
1. Utwórz nowy profil usługi Azure Traffic Manager
2. Tworzenie punktów końcowych w profilu usługi Traffic Manager
3. Ustaw konfigurację kondycji wyboru i trybu failover

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Krok 1: Utwórz nowy profil usługi Azure Traffic Manager
Tworzenie nowego profilu usługi Azure Traffic manager za pomocą contoso123 nazwę i wybierz metodę routingu priorytet. Jeśli masz już istniejącą grupę zasobów, którą chcesz skojarzyć z, a następnie wybrać istniejącą grupę zasobów, w przeciwnym razie utwórz nową grupę zasobów.

![Tworzenie profilu usługi Traffic Manager](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Rysunek — Tworzenie profilu usługi Traffic Manager*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Krok 2: Tworzenie punktów końcowych w profilu usługi Traffic Manager

W tym kroku utworzysz punktów końcowych, które wskazują na produkcyjne i lokacjach odzyskiwania po awarii. W tym miejscu wybierz **typu** jako zewnętrzny punkt końcowy, ale jeśli zasób jest hostowana na platformie Azure, umożliwiając wybranie **punkt końcowy platformy Azure** także. Jeśli wybierzesz **punkt końcowy platformy Azure**, a następnie wybierz **zasób docelowy** oznacza to jedną **usługi App Service** lub **publiczny adres IP** przydzielony przez Azure. Priorytet został ustawiony jako **1** , ponieważ jest ona podstawowym dla regionu 1.
Podobnie można utworzyć awaryjnego odzyskiwania punkt końcowy w ramach usługi Traffic Manager również.

![Tworzenie punktów końcowych odzyskiwania po awarii](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Rysunek — tworzenie punktów końcowych odzyskiwania po awarii*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Krok 3: Ustaw konfigurację kondycji wyboru i trybu failover

W tym kroku ustawisz czasu wygaśnięcia DNS to 10 sekund, które są uznawane przez większość mechanizmów rozpoznawania cyklicznego dostępnego z Internetu. Ta konfiguracja oznacza, że nie programu rozpoznawania nazw DNS spowoduje buforowanie tych informacji przez więcej niż 10 sekund. Dla ustawienia monitora punktu końcowego, ścieżka jest bieżący zestaw w / lub katalogu głównego, ale można dostosować ustawienia punktu końcowego można obliczyć ścieżki, na przykład prod.contoso.com/index. Poniżej przedstawiono przykład **https** protokół badania. Jednakże, możesz wybrać **http** lub **tcp** także. Zakończ aplikację zależy od wybranego protokołu. Interwał sondowania jest ustawiony na 10 sekund, co pozwala na szybkie badanie i ponowienie próby jest równa 3. Co w efekcie usługi Traffic Manager przejdzie w tryb failover do drugiego punktu końcowego Jeśli kolejne trzy w odstępach czasu zarejestrować błąd. Następująca formuła definiuje łączny czas do automatycznego trybu failover: Czas pracy w trybie Failover = TTL i ponów próbę wykonania * Probing interwał, a w tym przypadku wartość jest 10 + 3 * 10 = 40 sekund (maks.).
Jeśli ponowienie próby jest ustawiona na 1, a czas wygaśnięcia wynosi 10 sekund, następnie godzinę dla trybu failover 10 + 1 * 10 = 20 sekund. Ustawić ponownych prób na wartość większą niż **1** , aby wyeliminować ryzyko przejścia w tryb failover ze względu na wyniki fałszywie dodatnie lub dowolnym blips pomocniczych sieci. 


![Konfigurowanie kontroli kondycji](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Rysunek — Ustawianie kondycji konfiguracji wyboru i trybu failover*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Sposób automatycznego działania trybu failover przy użyciu usługi Traffic Manager

Podczas awarii, pobiera sondowany podstawowego punktu końcowego i stan zmieni się na **negatywny wpływ na dostępność** i lokacji odzyskiwania po awarii pozostaje **Online**. Domyślnie usługa Traffic Manager wysyła cały ruch do podstawowego punktu końcowego (najwyższy priorytet). Jeśli podstawowy punkt końcowy zostanie wyświetlone obniżonej wydajności, usługa Traffic Manager kieruje ruchem do drugiego punktu końcowego, tak długo, jak długo pozostaje on w dobrej kondycji. Jedna ma opcję, aby skonfigurować dodatkowe punkty końcowe w ramach usługi Traffic Manager, które mogą służyć jako punkty końcowe dodatkowej pracy awaryjnej lub, jako obciążenia równoważenia obciążenia między punktami końcowymi udostępniania.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Dowiedz się więcej o [system DNS Azure](../dns/dns-overview.md).









