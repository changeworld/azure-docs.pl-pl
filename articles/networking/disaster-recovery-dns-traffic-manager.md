---
title: Odzyskiwanie po awarii przy użyciu usługi Azure DNS i usługi Traffic Manager | Dokumentacja firmy Microsoft
description: Przegląd rozwiązań odzyskiwania po awarii przy użyciu usługi Azure DNS i usługi Traffic Manager.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
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
ms.openlocfilehash: d608378f9b3ff3179f9e37ef13f88c65a645d018
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112990"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Odzyskiwanie po awarii przy użyciu usług Azure DNS i Traffic Manager

Odzyskiwanie po awarii koncentruje się na odzyskanie poważny utratę funkcjonalności aplikacji. Aby można było wybrać rozwiązanie odzyskiwania po awarii, właściciele biznesowych i technologicznych najpierw ustalić poziom funkcji wymaganych podczas awarii, takich jak - niedostępne, częściowo dostępnego za pośrednictwem ograniczonej funkcjonalności lub opóźnione dostępności lub pełni dostępna.
Większość klientów korporacyjnych wybierania wielu region architektura odporność aplikacji lub infrastruktury poziomu trybu failover. Klienci mogą wybrać kilka metod w żądanie do osiągnięcia trybu failover i wysokiej dostępności za pomocą architektury nadmiarowe. Oto niektóre z popularnych metod:

- **Aktywny / pasywny ze stanem gotowości zimnych**: W tym rozwiązaniu trybu failover maszyn wirtualnych i innych urządzeń uruchomione w regionie rezerwy nie są aktywne, dopóki nie jest wymagane dla trybu failover. Jednak w środowisku produkcyjnym są replikowane w formie kopii zapasowych, obrazów maszyn wirtualnych lub szablonów Resource Manager w innym regionie. Ten mechanizm pracy awaryjnej jest ekonomiczne, ale trwa dłużej niż podjęcie ukończenia pracy awaryjnej.
 
    ![Aktywny/pasywny ze stanem gotowości zimnych](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Rysunek — aktywny/pasywny z konfiguracji odzyskiwania po awarii na rezerwy zimnych*

- **Aktywny/pasywny z jasny pilotażu**: W tym rozwiązaniu trybu failover skonfigurowano środowisku rezerwy z minimalną konfiguracją. Instalator ma tylko niezbędne usługi działające do obsługi tylko krytycznych i minimalnego zestawu aplikacji. W formacie macierzystym w tym scenariuszu można tylko wykonaj minimalną liczbę funkcji, ale można skalowanie w górę i zduplikować dodatkowych usług do zbiorczego obciążenia produkcji w sytuacji, gdy do pracy awaryjnej.
    
    ![Aktywny/pasywny z jasny pilotażu](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Rysunek: Aktywny/pasywny konfiguracji odzyskiwania po awarii jasny pilotażu*

- **Aktywny/pasywny ze stanem gotowości ciepłych**: W tym rozwiązaniu trybu failover rezerwy region jest wstępnie przygotowaniu i jest gotowa do sporządzenia podstawowej obciążenia, automatyczne skalowanie jest włączona i wszystkie wystąpienia są uruchomione i działają prawidłowo. To rozwiązanie nie jest skalowana do wykonania pełnej produkcji obciążenia, ale działa, a wszystkie usługi są uruchomione i działają prawidłowo. To rozwiązanie jest zwiększonej wersji podejścia jasny pilotażu.
    
    ![Aktywny/pasywny ze stanem gotowości ciepłych](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Rysunek: Aktywny/pasywny z konfiguracji odzyskiwania po awarii na rezerwy ciepłych*
    
Aby dowiedzieć się więcej na temat trybu failover i wysokiej dostępności, zobacz [odzyskiwania po awarii dla aplikacji Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planowanie architektury odzyskiwania po awarii

Istnieją dwa aspekty techniczne do konfigurowania architektury odzyskiwania po awarii:
-  Przy użyciu mechanizmu wdrażania do replikowania wystąpień, danych i konfiguracji między środowiskami podstawowego, jak i będące w gotowości. Ten typ odzyskiwania po awarii może odbywać się natywnie za pośrednictwem usługi Azure Site-Recovery za pośrednictwem usługi Microsoft Azure partnera urządzenia/Veritas lub NetApp. 
- Projektowanie rozwiązania do odwrócenia ruchu sieci/sieci web z lokacji podstawowej do lokacji wstrzymania. Ten typ odzyskiwania po awarii można osiągnąć za pomocą usługi Azure DNS, Manager(DNS) ruchu Azure lub usługi równoważenia obciążenia globalne innych firm.

W tym artykule jest ograniczona do metod za pomocą przekierowania ruchu w sieci i sieci Web. Aby uzyskać instrukcje dotyczące konfigurowania usługi Azure Site Recovery, zobacz [dokumentacją dotyczącą odzyskiwania lokacji Azure](https://docs.microsoft.com/azure/site-recovery/).
DNS jest jednym z najbardziej efektywny mechanizmy kierowanie ruchu sieciowego, ponieważ DNS jest często globalne i zewnętrznych do centrum danych i jest izolowane od niepowodzenia poziomu strefy (AZ) regionalnych lub dostępności. Co można użyć mechanizm pracy awaryjnej z systemem DNS oraz na platformie Azure, dwie usługi DNS można wykonywać takie same, w dowolny sposób — usługi Azure DNS (autorytatywnych DNS) i usługi Azure Traffic Manager (routing ruchu DNS inteligentnych). 

Jest ważne poznać kilka koncepcji w systemie DNS, które są często używane do rozwiązania zawarte w tym artykule omówiono:
- **Rekord DNS** — rekordy A są wskaźnikami, które wskazują domenę adres IPv4. 
- **Nazwa CNAME lub Canonical** — tego typu rekordu służy do wskaż inny rekord DNS. CNAME nie odpowiadać, podając adres IP, ale raczej wskaźnik do rekordu, który zawiera adres IP. 
- **Ważone Routing** — można do skojarzenia wagi do punktów końcowych usługi, a następnie rozpowszechnić ruchu w oparciu wagi przypisanej. Ta metoda routingu jest jednym z czterech ruchu routingu mechanizmy dostępnych w Menedżerze ruchu. Aby uzyskać więcej informacji, zobacz [ważone metody routingu](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Routing priorytet** — priorytet routingu jest oparta na sprawdzenie kondycji punktów końcowych. Domyślnie usługi Azure Traffic manager wysyła cały ruch do najwyższy priorytet punktu końcowego, a po awarii lub poważnej awarii, Traffic Manager kieruje ruch do dodatkowej punktu końcowego. Aby uzyskać więcej informacji, zobacz [metody routingu priorytet](../traffic-manager/traffic-manager-routing-methods.md#priority).

## <a name="manual-failover-using-azure-dns"></a>Ręcznego przełączania trybu failover przy użyciu usługi Azure DNS
Rozwiązanie ręcznej pracy awaryjnej usługi Azure DNS do odzyskiwania po awarii używa standardowego mechanizmu DNS do trybu failover do tworzenia kopii zapasowej lokacji. Opcja ręcznego odbierania przy użyciu usługi Azure DNS działa najlepiej, gdy używane w połączeniu z zimnych stan wstrzymania lub podejście jasny pilotażu. 

![Ręcznego przełączania trybu failover przy użyciu usługi Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Rysunek - ręcznego przełączania trybu failover przy użyciu usługi Azure DNS*

Założeniom dla rozwiązania są:
-   Podstawowe i dodatkowe punkty końcowe mają statyczne adresy IP, które nie zmieniają się często. Adres IP jest 100.168.124.44 i adres IP dla lokacji dodatkowej jest 100.168.124.43, że dla lokacji głównej.
-   Strefa DNS platformy Azure istnieje dla lokacji głównych i dodatkowych. Powiedz dla lokacji głównej punkt końcowy jest prod.contoso.com i tworzenia kopii zapasowej lokacji jest dr.contoso.com. Istnieje również rekordu DNS dla aplikacji głównej, znany jako www.contoso.com.   
-   Czas wygaśnięcia wynosi poziomie lub poniżej SLA RTO w organizacji. Na przykład, jeśli jednostka ustawia RTO odpowiedzi po awarii aplikacji jako 60 minutach czas wygaśnięcia musi być mniejsze niż 60 min, najlepiej niższa tym lepiej. Można ustawić usługę Azure DNS ręcznej pracy awaryjnej w następujący sposób:
1. Tworzenie strefy DNS
2. Utwórz rekordy strefy DNS
3. Zaktualizuj rekord CNAME

### <a name="step-1-create-a-dns"></a>Krok 1: Tworzenie systemu DNS
Tworzenie strefy DNS (na przykład www.contoso.com), jak pokazano poniżej:

![Tworzenie strefy DNS na platformie Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Rysunek — tworzenie strefy DNS na platformie Azure*

### <a name="step-2-create-dns-zone-records"></a>Krok 2: Tworzenie rekordów strefy DNS

W tej strefie Utwórz trzy rekordy (na przykład - www.contoso.com, prod.contoso.com i dr.consoto.com) jako wyświetlane poniżej.

![Utwórz rekordy strefy DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Rysunek — tworzenie rekordów strefy DNS na platformie Azure*

W tym scenariuszu, witryny, www.contoso.com ma TTL 30 minut, co jest znacznie poniżej podane RTO i wskazuje prod.contoso.com lokacji produkcji. Ta konfiguracja jest podczas operacji zwykłą działalnością. Wartość TTL prod.contoso.com i dr.contoso.com została ustawiona jako 300 sekund lub w ciągu 5 minut. Umożliwia monitorowanie usługi, takie jak Azure Monitor lub usługi Azure App Insights Azure lub partnerskich monitorowanie rozwiązań, takich jak Dynatrace, można również użyć macierzystego parametrach wymaganych rozwiązania, które można monitorować lub wykrywania aplikacji lub awarie poziomu infrastruktury wirtualnego.

### <a name="step-3-update-the-cname-record"></a>Krok 3: Zaktualizuj rekord CNAME

Gdy zostaje wykryta awaria, zmień wartości rekordu, aby wskazywał dr.contoso.com, jak pokazano poniżej:
       
![Zaktualizuj rekord CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Rysunek — aktualizacja rekord CNAME w systemie Azure*

W ciągu 30 minut, w których większość rozwiązujący odświeży pliku pamięci podręcznej strefy, wszystkie zapytania do www.contoso.com nastąpi przekierowanie do dr.contoso.com.
Można też uruchomić następujące polecenie wiersza polecenia platformy Azure, aby zmienić wartość CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Ten krok można wykonać ręcznie lub za pomocą automatyzacji. Go można przeprowadzić ręcznie za pomocą konsoli lub wiersza polecenia platformy Azure. Zestaw Azure SDK i interfejsu API może służyć do automatyzowania aktualizacji CNAME, dzięki czemu ręczna interwencja nie jest wymagane. Automatyzacja mogą być tworzone przez usługę Azure functions lub w ramach monitorowania aplikacji innych firm lub nawet z lokalnymi.

### <a name="how-manual-failover-works-using-azure-dns"></a>Jak ręczne działania trybu failover przy użyciu usługi Azure DNS
Ponieważ serwer DNS jest poza strefą pracy awaryjnej lub poważnej awarii, jest izolację żadnych przestojów. Umożliwia użytkownikowi zaprojektować trybu failover Prosty scenariusz, w którym kosztach i będzie działać wszystkie czasu przy założeniu, że operator ma połączenie sieciowe podczas awarii i może wykonać przerzucania. Jeśli rozwiązanie jest uwzględnione w skryptach, następnie jedną musi upewnij się, że serwera lub usługi uruchamiania skryptu powinny być izolowane przed problem wpływu na środowisko produkcyjne. Ponadto należy pamiętać, niski czas TTL, który został ustawiony dla strefy, aby rozwiązującego na świecie przechowuje w pamięci podręcznej długo punktu końcowego i klientom dostęp do witryny w celu. Zimnych wstrzymania i jasny pilotażu ponieważ niektóre prewarming i inne działanie administracyjne mogą być wymagane — jeden należy również podać wystarczająco dużo czasu, przed wprowadzeniem przerzucania.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatycznej pracy awaryjnej przy użyciu usługi Azure Traffic Manager
W przypadku złożonych architektury i wiele zestawów zasobów może wykonać taką samą funkcję, można sprawdzić kondycji zasobów i kierowania ruchu z innych niż dobra kondycja zasobu do dobrej kondycji, można skonfigurować usługi Azure Traffic Manager (w oparciu DNS) zasób. W poniższym przykładzie zarówno w regionie podstawowym, jak i w regionie pomocniczym ma pełne wdrożenie. To wdrożenie obejmuje usługi w chmurze i zsynchronizowane bazy danych. 

![Automatycznej pracy awaryjnej przy użyciu usługi Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Rysunek - automatycznej pracy awaryjnej przy użyciu usługi Azure Traffic Manager*

Jednak tylko regionu podstawowego jest aktywnie obsługi żądań sieci po stronie użytkownika. Region pomocniczy staje się aktywny tylko wtedy, gdy w regionie podstawowym napotyka przerw w działaniu usługi. W takim przypadku wszystkie nowe żądania sieci trasy w regionie pomocniczym. Po wykonaniu kopii zapasowej bazy danych jest niemal natychmiastowe, równoważenia obciążenia mają adresy IP, które mogą być zaznaczone, kondycji i wystąpienia są zawsze aktualne i uruchomiony, ta topologia udostępnia opcję do przechodzenia niskiej RTO i trybu failover bez ręcznej interwencji. Region dodatkowej pracy awaryjnej musi być gotowy do aktywnego przejdź od razu po awarii regionu podstawowego.
Jest to idealne rozwiązanie w przypadku korzystanie z usługi Azure Traffic Manager zawierający wbudowane sondy dla różnych typów kontroli kondycji, w tym http / https i TCP. Menedżer ruchu Azure ma również aparat reguł, które mogą być skonfigurowane do pracy awaryjnej, gdy wystąpi błąd, zgodnie z poniższym opisem. Teraz należy wziąć pod uwagę następujące rozwiązanie przy użyciu Menedżera ruchu:
- Klient ma znane jako prod.contoso.com ze statycznym adresem IP 100.168.124.44 i punktu końcowego Region #2 znany jako dr.contoso.com ze statycznym adresem IP jako 100.168.124.43 punktu końcowego regionu #1. 
-   Każdy z tych środowisk jest fronted za pośrednictwem właściwości publicznej połączonej takich jak usługi równoważenia obciążenia. Można skonfigurować usługi równoważenia obciążenia mają punktu końcowego usługi DNS lub w pełni kwalifikowaną nazwą domeny (FQDN), jak pokazano powyżej.
-   Wszystkie wystąpienia w regionie 2 znajdują się w pobliżu replikacji w czasie rzeczywistym z regionu 1. Ponadto obrazy maszyny są aktualne, a wszystkie dane oprogramowania i konfiguracji jest poprawiono i są zgodne z regionu 1.  
-   Skalowanie automatyczne są wstępnie skonfigurowane wcześniej. 

Etapy, aby skonfigurować tryb failover z usługi Azure Traffic Manager są następujące:
1. Utwórz nowy profil usługi Azure Traffic Manager
2. Utwórz punkty końcowe w ramach profilu Menedżera ruchu
3. Ustawianie konfiguracji kondycji wyboru i trybu failover

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Krok 1: Utwórz nowy profil usługi Azure Traffic Manager
Utwórz nowy profil Menedżera ruchu Azure z contoso123 nazwę i wybierz metodę routingu priorytet. Jeśli masz już istniejącą grupę zasobów, której chcesz skojarzyć z, a następnie można wybrać istniejącą grupę zasobów, w przeciwnym razie utwórz nową grupę zasobów.

![Tworzenie profilu Menedżera ruchu](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)
*rysunek — Utwórz profil Menedżera ruchu*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Krok 2: Tworzenie punktów końcowych w profilu Menedżera ruchu

W tym kroku utworzysz punktów końcowych, które wskazują produkcyjnych i w lokacjach odzyskiwania po awarii. W tym miejscu, wybierz **typu** jako zewnętrznego punktu końcowego, ale jeśli zasób jest hostowana na platformie Azure, możesz zdecydować, **punktu końcowego platformy Azure** również. Jeśli wybierzesz **punktu końcowego platformy Azure**, a następnie wybierz pozycję **zasób docelowy** czyli albo **usługi aplikacji** lub **publicznego adresu IP** przydzielony przez Azure. Priorytet został ustawiony jako **1** ponieważ głównej usługi dla regionu 1.
Podobnie można utworzyć awaryjnego odzyskiwania punkt końcowy w ramach usługi Traffic Manager również.

![Tworzenie punktów końcowych odzyskiwania po awarii](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Rysunek — tworzenie punktów końcowych odzyskiwania po awarii*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Krok 3: Ustawianie konfiguracji kondycji wyboru i trybu failover

W tym kroku należy ustawić czas wygaśnięcia DNS na 10 sekund, która jest honorowane przez większość rozwiązujący cykliczne internetowy. Ta konfiguracja oznacza, że nie program rozpoznawania nazw DNS będzie buforować informacje dla więcej niż 10 sekund. Dla ustawienia monitora punktu końcowego, ścieżka jest bieżącego zestawu w / lub katalogu głównego, ale można dostosować ustawienia punktu końcowego można obliczyć ścieżki, na przykład prod.contoso.com/index. Poniżej przedstawiono przykład **https** jako protokół sondowania. Jednak można wybrać **http** lub **tcp** również. Wybór protokół zależy od aplikacji zakończenia. Interwał sondowania jest ustawiony na 10 sekund, co pozwala na szybkie sondowanie i retry wynosi 3. W związku z tym Menedżer ruchu przejdzie w tryb failover drugiego punktu końcowego Jeśli trzech kolejnych interwałów rejestrują awarii. Łączny czas do automatycznego przełączania awaryjnego definiuje następującej formuły: czasu dla trybu failover = TTL i spróbuj ponownie * interwał sondowanie w takim przypadku wartość to 10 + 3 * 10 = 40 sekund (maks.).
Jeśli ponowna ma ustawioną wartość 1 i czas wygaśnięcia wynosi 10 s, następnie czasu dla trybu failover 10 + 1 * 10 = 20 sekund. Wartość Retry wartość większą od **1** do eliminowanie szanse przechodzenia w tryb failover z powodu fałszywych alarmów lub dowolnego blips pomocnicza sieci. 


![Konfigurowanie sprawdzenie kondycji](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Rysunek — kondycji konfiguracji wyboru i trybu failover*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Sposób automatycznego działania trybu failover przy użyciu Menedżera ruchu

Podczas awarii, pobiera sondowany podstawowy punkt końcowy i stan zmienia się na **ze spadkiem** i lokacji odzyskiwania po awarii pozostaje **Online**. Domyślnie usługi Traffic Manager wysyła cały ruch do podstawowego punktu końcowego (najwyższy priorytet). Jeśli podstawowy punkt końcowy zostanie wyświetlone obniżeniem, Menedżera ruchu kieruje ruch do drugiego punktu końcowego tak długo, jak pozostaje dobrej kondycji. Ma jedną opcję, aby skonfigurować więcej punktów końcowych w ramach usługi Traffic Manager, które mogą służyć jako punkty końcowe dodatkowe pracy awaryjnej lub, jako usługi równoważenia obciążenia między punktami końcowymi udostępnianie obciążenia.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Dowiedz się więcej o [usługi Azure DNS](../dns/dns-overview.md).









