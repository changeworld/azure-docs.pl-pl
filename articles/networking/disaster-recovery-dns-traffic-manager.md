---
title: Odzyskiwanie po awarii przy użyciu usługi Azure DNS i Usługi Traffic Manager | Dokumenty firmy Microsoft
description: Omówienie rozwiązań odzyskiwania po awarii przy użyciu usługi Azure DNS i usługi Traffic Manager.
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
ms.openlocfilehash: 6eab1803bf5adab42be87b5f8567682c6d75947e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483535"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Odzyskiwanie po awarii przy użyciu usług Azure DNS i Traffic Manager

Odzyskiwanie po awarii powinno być skoncentrowane na odzyskiwaniu po poważnej utracie funkcjonalności aplikacji. Aby wybrać rozwiązanie do odzyskiwania po awarii, właściciele firm i technologii muszą najpierw określić poziom funkcjonalności wymaganych podczas awarii, takich jak - niedostępne, częściowo dostępne za pośrednictwem zmniejszonej funkcjonalności lub opóźnionej dostępności, lub w pełni dostępne.
Większość klientów korporacyjnych wybiera architekturę wieloregionową, aby zapewnić odporność na tryb failover na poziomie aplikacji lub infrastruktury. Klienci mogą wybrać kilka podejść w dążeniu do osiągnięcia pracy awaryjnej i wysokiej dostępności za pośrednictwem nadmiarowej architektury. Oto niektóre z popularnych podejść:

- **Aktywny pasywny z zimnym trybem czuwania:** W tym rozwiązaniu trybu failover maszyny wirtualne i inne urządzenia, które są uruchomione w regionie wstrzymania, nie są aktywne, dopóki nie będzie potrzebne do pracy awaryjnej. Jednak środowisko produkcyjne jest replikowane w postaci kopii zapasowych, obrazów maszyn wirtualnych lub szablonów Menedżera zasobów do innego regionu. Ten mechanizm pracy awaryjnej jest opłacalne, ale trwa dłużej, aby podjąć pełną pracy awaryjnej.
 
    ![Aktywny/pasywny z zimnym czuwaniem](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Rysunek — aktywny/pasywny z konfiguracją odzyskiwania po awarii w trybie czuwania w trybie chłodnym*

- **Aktywne/pasywne ze światłem pilota:** W tym rozwiązaniu trybu failover środowisko czuwania jest skonfigurowane przy minimalnej konfiguracji. Instalator ma tylko niezbędne usługi uruchomione do obsługi tylko minimalny i krytyczny zestaw aplikacji. W swojej formie macierzystej w tym scenariuszu można wykonać tylko minimalne funkcje, ale można skalować w górę i spawn dodatkowych usług do podjęcia większość obciążenia produkcyjnego, jeśli nastąpi praca awaryjna.
    
    ![Aktywny/pasywny ze światłem pilota](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Rysunek: Aktywny /Pasywny z konfiguracją odzyskiwania po awarii światła pilota*

- **Aktywny/pasywny z ciepłym trybem czuwania:** W tym rozwiązaniu trybu failover obszar czuwania jest wstępnie rozgrzany i jest gotowy do obciążenia podstawowego, automatyczne skalowanie jest włączone, a wszystkie wystąpienia są uruchomione. To rozwiązanie nie jest skalowane w celu pełnego obciążenia produkcyjnego, ale jest funkcjonalne, a wszystkie usługi są uruchomione. To rozwiązanie jest rozszerzoną wersją podejścia światła pilota.
    
    ![Aktywny/pasywny z ciepłym trybem czuwania](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Rysunek: Aktywny/Pasywny z konfiguracją odzyskiwania po awarii w trybie gotowości*
    
Aby dowiedzieć się więcej o pracy awaryjnej i wysokiej dostępności, zobacz [Odzyskiwanie po awarii dla aplikacji platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planowanie architektury odzyskiwania po awarii

Istnieją dwa techniczne aspekty konfigurowania architektury odzyskiwania po awarii:
-  Za pomocą mechanizmu wdrażania do replikowania wystąpień, danych i konfiguracji między środowiskami podstawowymi i rezerwowymi. Ten typ odzyskiwania po awarii można wykonać natywnie za pośrednictwem usługi Azure Site-Recovery za pośrednictwem urządzeń/usług partnerskich platformy Microsoft Azure, takich jak Veritas lub NetApp. 
- Opracowanie rozwiązania do przekazywania ruchu sieciowego/sieciowego z lokacji głównej do witryny rezerwowej. Ten typ odzyskiwania po awarii można osiągnąć za pomocą usługi Azure DNS, usługi Azure Traffic Manager(DNS) lub globalnych modułów równoważenia obciążenia innych firm.

Ten artykuł jest ograniczony do podejść za pośrednictwem przekierowania ruchu sieciowego i sieci Web. Aby uzyskać instrukcje konfigurowania usługi Azure Site Recovery, zobacz [Dokumentacja odzyskiwania witryny platformy Azure](https://docs.microsoft.com/azure/site-recovery/).
DNS jest jednym z najbardziej wydajnych mechanizmów przekazywania ruchu sieciowego, ponieważ system DNS jest często globalny i zewnętrzny dla centrum danych i jest izolowany od awarii poziomu strefy regionalnej lub dostępności (AZ). Można użyć mechanizmu pracy awaryjnej opartego na systemie DNS i na platformie Azure dwie usługi DNS można osiągnąć to samo w pewnym uczynkiem — Azure DNS (autorytatywny DNS) i Azure Traffic Manager (routing inteligentnego ruchu opartego na systemie DNS). 

Ważne jest, aby zrozumieć kilka pojęć w systemie DNS, które są szeroko stosowane w celu omówienia rozwiązań przewidzianych w tym artykule:
- **REKORD DNS —** rekordy to wskaźniki wskazujące domenę na adres IPv4. 
- **Nazwa CNAME lub Kanoniczny** — ten typ rekordu jest używany do wskażenia innego rekordu DNS. CNAME nie odpowiada za pomocą adresu IP, ale raczej wskaźnik do rekordu, który zawiera adres IP. 
- **Routing ważony** — można skojarzyć wagę z punktami końcowymi usługi, a następnie dystrybuować ruch na podstawie przypisanych wag. Ta metoda routingu jest jednym z czterech mechanizmów routingu ruchu dostępnych w usłudze Traffic Manager. Aby uzyskać więcej informacji, zobacz [Metoda ważona routingu](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Routing priorytetu** — routing priorytetu opiera się na kontroli kondycji punktów końcowych. Domyślnie usługa Azure Traffic Manager wysyła cały ruch do punktu końcowego o najwyższym priorytecie, a po awarii lub awarii usługa Traffic Manager kieruje ruch do pomocniczego punktu końcowego. Aby uzyskać więcej informacji, zobacz [Metoda routingu priorytetowego](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Ręczne przewijowanie awaryjne przy użyciu usługi Azure DNS
Ręczne rozwiązanie trybu failover usługi Azure DNS do odzyskiwania po awarii używa standardowego mechanizmu DNS do pracy awaryjnej w lokacji kopii zapasowej. Opcja ręczna za pośrednictwem usługi Azure DNS działa najlepiej w połączeniu z zimnym trybem gotowości lub podejściem światła pilotażowego. 

![Ręczne przewijowanie awaryjne przy użyciu usługi Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Rysunek — ręczne przebłaganie awaryjne przy użyciu usługi Azure DNS*

Założenia rozwiązania są następujące:
- Zarówno podstawowe, jak i pomocnicze punkty końcowe mają statyczne punkty IP, które nie zmieniają się często. Powiedzmy, że dla lokacji głównej adres IP to 100.168.124.44, a adres IP dla lokacji dodatkowej to 100.168.124.43.
- Istnieje strefa DNS platformy Azure dla lokacji podstawowej i dodatkowej. Powiedzmy, że dla lokacji głównej punkt końcowy jest prod.contoso.com, a dla lokacji kopii zapasowej jest dr.contoso.com. Istnieje również rekord DNS dla\.głównej aplikacji znanej jako www contoso.com.   
- Czas wygaśnięcia znajduje się na poziomie lub poniżej umowy SLA RTO ustawionej w organizacji. Na przykład jeśli przedsiębiorstwo ustawia RTO odpowiedzi na awarie aplikacji na 60 minut, a następnie czas wygaśnięcia powinien być mniejszy niż 60 minut, najlepiej im niższe, tym lepiej. 
  Usługę Azure DNS można skonfigurować dla ręcznej pracy awaryjnej w następujący sposób:
- Tworzenie strefy DNS
- Tworzenie rekordów strefy DNS
- Aktualizuj rekord CNAME

### <a name="step-1-create-a-dns"></a>Krok 1: Tworzenie systemu DNS
Utwórz strefę DNS (na przykład www\.contoso.com), jak pokazano poniżej:

![Tworzenie strefy DNS na platformie Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Rysunek — tworzenie strefy DNS na platformie Azure*

### <a name="step-2-create-dns-zone-records"></a>Krok 2: Tworzenie rekordów strefy DNS

W tej strefie utwórz trzy\.rekordy (na przykład - www contoso.com, prod.contoso.com i dr.consoto.com), jak pokazano poniżej.

![Tworzenie rekordów strefy DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Rysunek — tworzenie rekordów strefy DNS na platformie Azure*

W tym scenariuszu\.witryna www contoso.com ma TL 30 minut, który jest znacznie poniżej podanego celu rto i wskazuje na prod.contoso.com miejsca produkcji. Ta konfiguracja jest podczas normalnych operacji biznesowych. Czas wygaśnięcia prod.contoso.com i dr.contoso.com został ustawiony na 300 sekund lub 5 minut. Można użyć usługi monitorowania platformy Azure, takich jak Azure Monitor lub Usługi Azure App Insights, lub, wszelkie rozwiązania monitorowania partnerów, takich jak Dynatrace, Można nawet użyć rozwiązań domowych, które mogą monitorować lub wykrywać błędy poziomu aplikacji lub infrastruktury wirtualnej.

### <a name="step-3-update-the-cname-record"></a>Krok 3: Aktualizowanie rekordu CNAME

Po wykryciu awarii zmień wartość rekordu na dr.contoso.com jak pokazano poniżej:
       
![Aktualizuj rekord CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Rysunek — aktualizowanie rekordu CNAME na platformie Azure*

W ciągu 30 minut, podczas których większość programów rozpoznawania nazw\.odświeży plik strefy w pamięci podręcznej, każde zapytanie do contoso.com www zostanie przekierowane do dr.contoso.com.
Można również uruchomić następujące polecenie interfejsu wiersza polecenia platformy Azure, aby zmienić wartość CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Ten krok można wykonać ręcznie lub za pośrednictwem automatyzacji. Można to zrobić ręcznie za pośrednictwem konsoli lub interfejsu wiersza polecenia platformy Azure. Zestaw Azure SDK i interfejs API może służyć do automatyzacji aktualizacji CNAME, tak aby nie ręczna interwencja jest wymagana. Automatyzacja może być budowana za pomocą funkcji platformy Azure lub w ramach aplikacji do monitorowania innej firmy, a nawet z lokalnego.

### <a name="how-manual-failover-works-using-azure-dns"></a>Jak działa ręczna praca awaryjna przy użyciu usługi Azure DNS
Ponieważ serwer DNS znajduje się poza strefą pracy awaryjnej lub awarii, jest izolowany przed wszelkimi przestojami. Dzięki temu użytkownik do kosztów prosty scenariusz pracy awaryjnej, który jest opłacalny i będzie działać przez cały czas przy założeniu, że operator ma łączność sieciową podczas awarii i może dokonać przerzucania. Jeśli rozwiązanie jest skryptowane, należy upewnić się, że serwer lub usługa uruchamiana skrypt powinny być izolowane przed problemem wpływającym na środowisko produkcyjne. Ponadto należy pamiętać o niskim czasie wygaśnięcia, który został ustawiony względem strefy, tak aby żaden program rozpoznawania nazw na całym świecie nie przechowywał punktu końcowego w pamięci podręcznej przez długi czas, a klienci mogą uzyskać dostęp do lokacji w ramach obiektu RTO. W przypadku zimnego światła czuwania i pilota, ponieważ może być wymagane pewne przedogrzewanie i inne działania administracyjne - należy również dać wystarczająco dużo czasu przed dokonaniem klapki.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatyczne przewijanie w tryb failover przy użyciu usługi Azure Traffic Manager
Jeśli masz złożone architektury i wiele zestawów zasobów zdolnych do wykonywania tej samej funkcji, można skonfigurować usługę Azure Traffic Manager (na podstawie systemu DNS), aby sprawdzić kondycję zasobów i kierować ruch z zasobu niezdrogo do dobrej kondycji Zasobów. W poniższym przykładzie zarówno regionu podstawowego, jak i pomocniczego regionu mają pełne wdrożenie. To wdrożenie obejmuje usługi w chmurze i zsynchronizowana baza danych. 

![Automatyczne przewijanie w tryb failover przy użyciu usługi Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Rysunek — automatyczna funkcja pracy awaryjnej przy użyciu usługi Azure Traffic Manager*

Jednak tylko region podstawowy aktywnie obsługuje żądania sieciowe od użytkowników. Region pomocniczy staje się aktywny tylko wtedy, gdy region podstawowy wystąpi zakłócenie usługi. W takim przypadku wszystkie nowe żądania sieci trasy do regionu pomocniczego. Ponieważ kopia zapasowa bazy danych jest w pobliżu chwilowe, zarówno moduły równoważenia obciążenia mają adresy IP, które mogą być sprawdzane kondycji, a wystąpienia są zawsze uruchomione, ta topologia zapewnia opcję przechodzenia na niskie RTO i pracy awaryjnej bez ręcznej interwencji. Pomocniczy region trybu failover musi być gotowy do przejścia na żywo natychmiast po awarii regionu podstawowego.
Ten scenariusz jest idealny do korzystania z usługi Azure Traffic Manager, który ma wbudowane sondy dla różnych typów kontroli kondycji, w tym http / https i TCP. Usługa Azure Traffic Manager ma również aparat reguł, który można skonfigurować do pracy awaryjnej, gdy wystąpi błąd, jak opisano poniżej. Rozważmy następujące rozwiązanie przy użyciu usługi Traffic Manager:
- Klient ma punkt końcowy regionu #1 znany jako prod.contoso.com ze statycznym adresem IP jako 100.168.124.44 i punktem końcowym regionu #2 znanym jako dr.contoso.com ze statycznym adresem IP jako 100.168.124.43. 
-   Każde z tych środowisk jest fronted za pośrednictwem właściwości publicznej stoi jak moduł równoważenia obciążenia. Moduł równoważenia obciążenia można skonfigurować tak, aby miał punkt końcowy oparty na systemie DNS lub w pełni kwalifikowaną nazwę domeny (FQDN), jak pokazano powyżej.
-   Wszystkie wystąpienia w regionie 2 są w pobliżu replikacji w czasie rzeczywistym z regionu 1. Ponadto obrazy maszyn są aktualne, a wszystkie dane oprogramowania/konfiguracji są załatane i są zgodne z regionem 1.  
-   Skalowanie automatyczne jest wstępnie skonfigurowane z wyprzedzeniem. 

Kroki podjęte w celu skonfigurowania pracy awaryjnej za pomocą usługi Azure Traffic Manager są następujące:
1. Tworzenie nowego profilu usługi Azure Traffic Manager
2. Tworzenie punktów końcowych w profilu usługi Traffic Manager
3. Konfigurowanie sprawdzania kondycji i konfiguracji trybu failover

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Krok 1: Tworzenie nowego profilu usługi Azure Traffic Manager
Utwórz nowy profil usługi Azure Traffic Manager o nazwie contoso123 i wybierz metodę Routing jako priorytet. Jeśli masz wcześniej istniejącą grupę zasobów, z którą chcesz skojarzyć, możesz wybrać istniejącą grupę zasobów, w przeciwnym razie utwórz nową grupę zasobów.

![Utwórz profil usługi Traffic Manager](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Rysunek — tworzenie profilu usługi Traffic Manager*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Krok 2: Tworzenie punktów końcowych w profilu usługi Traffic Manager

W tym kroku utworzysz punkty końcowe, które wskazują na lokacje produkcyjne i odzyskiwania po awarii. W tym miejscu wybierz **typ** jako zewnętrzny punkt końcowy, ale jeśli zasób jest obsługiwany na platformie Azure, możesz również wybrać **punkt końcowy platformy Azure.** Jeśli wybierzesz **punkt końcowy platformy Azure,** wybierz **zasób docelowy,** który jest **usługą App Service** lub publicznym adresem **IP** przydzielonym przez platformę Azure. Priorytet jest ustawiony jako **1,** ponieważ jest to usługa podstawowa dla regionu 1.
Podobnie utworzyć punkt końcowy odzyskiwania po awarii w usłudze Traffic Manager, jak również.

![Tworzenie punktów końcowych odzyskiwania po awarii](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Rysunek — tworzenie punktów końcowych odzyskiwania po awarii*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Krok 3: Konfigurowanie sprawdzania kondycji i konfiguracji trybu failover

W tym kroku ustawisz czas wygaśnięcia DNS na 10 sekund, który jest honorowany przez większość cyklicznych programów rozpoznawania nazw w Internecie. Ta konfiguracja oznacza, że żaden program rozpoznawania nazw DNS nie będzie buforować informacji przez więcej niż 10 sekund. W przypadku ustawień monitora punktu końcowego ścieżka jest aktualna ustawiona na / lub katalogu głównego, ale można dostosować ustawienia punktu końcowego, aby ocenić ścieżkę, na przykład prod.contoso.com/index. Poniższy przykład przedstawia **https** jako protokół sondowania. Można jednak wybrać **również http** lub **tcp.** Wybór protokołu zależy od aplikacji końcowej. Interwał sondowania jest ustawiony na 10 sekund, co umożliwia szybkie sondowanie, a ponowna próba jest ustawiona na 3. W rezultacie usługa Traffic Manager przejdzie w stan failover do drugiego punktu końcowego, jeśli trzy kolejne interwały zarejestrują błąd. Następująca formuła definiuje całkowity czas automatycznego trybu failover: Czas pracy awaryjnej = TTL + Ponów próbę * Interwał sondowania I w tym przypadku wartość wynosi 10 + 3 * 10 = 40 sekund (Maks.).
Jeśli ponowienie próby jest ustawiona na 1 i TTL jest ustawiona na 10 sekund, a następnie czas pracy awaryjnej 10 + 1 * 10 = 20 sekund. Ustaw ponów próbę na wartość większą niż **1,** aby wyeliminować szanse na przekroczenie stanu failover z powodu fałszywych alarmów lub nieznacznych blips sieci. 


![Konfigurowanie kontroli kondycji](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Rysunek — konfigurowanie sprawdzania kondycji i konfiguracji trybu failover*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Jak działa automatyczne praca awaryjna przy użyciu usługi Traffic Manager

Podczas awarii podstawowy punkt końcowy zostanie sondowany, a stan zmieni się na **zdegradowany,** a witryna odzyskiwania po awarii pozostaje **w trybie online.** Domyślnie usługa Traffic Manager kieruje cały ruch do podstawowego punktu końcowego (punktu końcowego o najwyższym priorytecie). Jeśli podstawowy punkt końcowy wydaje się obniżona, Usługa Traffic Manager kieruje ruch do drugiego punktu końcowego, o ile pozostaje w dobrej kondycji. Jeden ma możliwość skonfigurowania większej liczby punktów końcowych w usłudze Traffic Manager, które mogą służyć jako dodatkowe punkty końcowe trybu failover lub jako moduły równoważenia obciążenia dzielące obciążenie między punktami końcowymi.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Dowiedz się więcej o [usłudze Azure DNS](../dns/dns-overview.md).









