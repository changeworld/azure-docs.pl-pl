---
title: Zabezpieczanie architektury obliczeniowej platformy Azure
description: Ta architektura referencyjna dla architektury DMZ na poziomie przedsiębiorstwa używa sieciowych urządzeń wirtualnych i innych narzędzi. Ta architektura została zaprojektowana w taki sposób, aby spełniała wymagania funkcjonalne dotyczące architektury przetwarzania w chmurze zabezpieczeń. Można go również użyć dla każdej organizacji. To odwołanie obejmuje dwie opcje zautomatyzowane korzystające z urządzeń Citrix lub F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: a12f5643c96b855d07bd038fcc96100a87f1252d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001877"
---
# <a name="secure-azure-computing-architecture"></a>Zabezpieczanie architektury obliczeniowej platformy Azure

Federalna Klienci działu obrony (DoD), którzy wdrażają obciążenia na platformie Azure, zażądali wskazówek dotyczących konfigurowania zabezpieczonych sieci wirtualnych oraz konfigurowania narzędzi i usług zabezpieczeń, które są określone przez standardy DoD i praktyczne. 

Agencja DISA (obrony informacji) opublikowała [dokument wymagań funkcjonalnych (FRD)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) w systemie 2017. SCCA opisuje cele funkcjonalne w zakresie zabezpieczania sieci systemu informacji o obrony (DISN) i komercyjnych punktów połączenia z dostawcą usług w chmurze. SCCA również opisuje, jak właściciele misja zabezpieczają aplikacje w chmurze na granicy połączenia. Każda jednostka DoD, która łączy się z chmurą komercyjną, musi przestrzegać wytycznych określonych w SCCA FRD.
 
SCCA ma cztery składniki:
 
- Punkt dostępu do chmury granicy (BCAP)
- Stos zabezpieczeń wirtualnego centrum danych (VDSS)
- Usługa zarządzana przez wirtualne centrum danych (VDMS)
- Zaufany Menedżer poświadczeń w chmurze (TCCM) 

Firma Microsoft opracowała rozwiązanie spełniające wymagania SCCA dla obciążeń IL4 i IL5, które działają na platformie Azure. To rozwiązanie specyficzne dla platformy Azure nosi nazwę Secure Azure Computing Architecture (SACA). Klienci, którzy wdrażają SACA, są w zgodności z SCCA FRD. Mogą umożliwić klientom usługi DoD przeniesienie obciążeń do platformy Azure po ich połączeniu.

Wskazówki i architektury SCCA są specyficzne dla klientów DoD, ale najnowsze wersje SACA pomagają klientom z zachowaniem zgodności z zaufanym połączeniem internetowym (kółko I krzyżyk). Najnowsze wersje pomagają również klientom komercyjnym, którzy chcą wdrożyć bezpieczną strefę DMZ, aby chronić swoje środowiska platformy Azure.


## <a name="secure-cloud-computing-architecture-components"></a>Bezpieczne składniki architektury przetwarzania w chmurze

### <a name="bcap"></a>BCAP

Celem BCAP jest ochrona DISN przed atakami, które pochodzą ze środowiska chmury. BCAP wykonuje wykrywanie i Zapobieganie włamaniom. filtruje również nieautoryzowany ruch. Ten składnik może być umieszczony wspólnie z innymi składnikami SCCA. Zalecamy wdrożenie tego składnika przy użyciu sprzętu fizycznego. Wymagania dotyczące zabezpieczeń BCAP są wymienione w poniższej tabeli.

#### <a name="bcap-security-requirements"></a>BCAP wymagania dotyczące zabezpieczeń

![Macierz wymagań BCAP](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

Celem VDSS jest ochrona aplikacji typu DoD-Owner, które są hostowane na platformie Azure. VDSS wykonuje zbiorcze operacje związane z zabezpieczeniami w SCCA. Przeprowadza inspekcję ruchu, aby zabezpieczyć aplikacje działające na platformie Azure. Ten składnik można zapewnić w środowisku platformy Azure.

#### <a name="vdss-security-requirements"></a>VDSS wymagania dotyczące zabezpieczeń

![Macierz wymagań VDSS](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

Celem VDMS jest zapewnienie bezpieczeństwa hosta i udostępnionych usług centrum danych. Funkcje VDMS mogą być uruchamiane w centrum SCCAu lub właścicielowi misja może wdrożyć fragmenty IT w ramach własnej subskrypcji platformy Azure. Ten składnik można zapewnić w środowisku platformy Azure.

#### <a name="vdms-security-requirements"></a>VDMS wymagania dotyczące zabezpieczeń

![Macierz wymagań VDMS](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM jest rolą biznesową. Osoba ta jest odpowiedzialna za zarządzanie SCCA. Ich obowiązki są następujące: 

- Ustanów plany i zasady dotyczące dostępu do środowiska chmury. 
- Upewnij się, że zarządzanie tożsamościami i dostępem działa prawidłowo. 
- Obsługuj plan zarządzania poświadczeniami w chmurze. 

Osoba ta została wyznaczona przez autoryzowanego urzędnika. BCAP, VDSS i VDMS zapewniają możliwości, które TCCM potrzebuje do wykonania swoich zadań.

#### <a name="tccm-security-requirements"></a>TCCM wymagania dotyczące zabezpieczeń

![Macierz wymagań TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Zagadnienia dotyczące SACA i planowania 

Architektura referencyjna SACA została zaprojektowana tak, aby wdrażać składniki VDSS i VDMS na platformie Azure oraz włączyć TCCM. Ta architektura jest modularna. Wszystkie elementy VDSS i VDMS mogą być aktywne w scentralizowanym centrum. Niektóre formanty mogą być spełnione w obszarze właściciela lub nawet lokalnie. Firma Microsoft zaleca, aby umieścić składniki VDSS i VDMS w centralnej sieci wirtualnej, za pomocą których wszyscy właściciele mogą się łączyć. Na poniższym diagramie przedstawiono tę architekturę: 


![Diagram architektury referencyjnej SACA](media/sacav2generic.png)

Planując strategię SCCA zgodności i architekturę techniczną, weź pod uwagę następujące tematy od początku, ponieważ wpływają one na każdego klienta. Następujące problemy zostały udostępnione w wysokości DoD i umożliwiają spowolnienie planowania i wykonywania. 

#### <a name="which-bcap-will-your-organization-use"></a>Których BCAP Twoja organizacja będzie używać?
   - DISA BCAP:
        - DISA ma dwa operacyjne BCAPs na Pentagon, a w przypadku Camp Roberts, CA. Trzecia została zaplanowana wkrótce w trybie online. 
        - DISA BCAPs wszystkie obwody platformy Azure ExpressRoute z platformą Azure, które mogą być używane przez klientów DoD na potrzeby łączności. 
        - Usługa DISA ma sesję komunikacji równorzędnej firmy Microsoft dla klientów DoD, którzy chcą subskrybować narzędzia firmy Microsoft jako usługi (SaaS), takie jak Office 365. Korzystając z DISA BCAP, można włączyć łączność i komunikację równorzędną z wystąpieniem SACA. 
    - Tworzenie własnych BCAP:
        - Ta opcja wymaga przeprowadzenia dzierżawy w ramach wspólnego centrum danych i skonfigurowania obwodu usługi ExpressRoute na platformie Azure. 
        - Ta opcja wymaga dodatkowego zatwierdzenia. 
        - Ze względu na dodatkowe zatwierdzenie i fizyczną kompilację, ta opcja zajmuje najwięcej czasu. 
    - Zalecamy użycie DISA BCAP. Ta opcja jest łatwo dostępna, ma wbudowaną nadmiarowość i ma klientów, którzy obecnie działają w środowisku produkcyjnym.
- Przestrzeń adresów IP z obsługą DoD:
    - Na krawędzi należy używać przestrzeni adresów IP o wysokości DoD. Dostępna jest opcja użycia translatora adresów sieciowych do łączenia tych miejsc z prywatną przestrzenią adresów IP na platformie Azure.
    - Aby uzyskać przestrzeń ADRESową, skontaktuj się z centrum informacji o sieci (NIC). Jest to konieczne w ramach procesu zatwierdzania systemu/sieci (SNAP) za pomocą DISA. 
    - Jeśli planujesz używać translatora adresów sieciowych do łączenia prywatnych przestrzeni adresowych na platformie Azure, potrzebujesz co najmniej 24 podsieci przestrzeni adresowej przypisanej z karty sieciowej dla każdego regionu, w którym planujesz wdrożyć SACA.
- Nadmiarowość:
    - Wdróż wystąpienie SACA w co najmniej dwóch regionach. W chmurze DoD należy wdrożyć ją w obydwu dostępnych regionach DoD.
    - Połącz się z co najmniej dwoma BCAPs za pomocą oddzielnych obwodów usługi ExpressRoute. Oba połączenia ExpressRoute można następnie połączyć z wystąpieniem SACA każdego regionu. 
- DoD wymagania specyficzne dla składnika:
    - Czy Twoja organizacja ma określone wymagania poza wymaganiami SCCA? Niektóre organizacje mają określone wymagania dotyczące adresów IP.
- SACA jest architekturą modularną:
    - Używaj tylko składników potrzebnych dla danego środowiska. 
        - Wdróż sieciowe urządzenia wirtualne w jednej warstwie lub wielowarstwowej.
        - Korzystaj ze zintegrowanych adresów IP lub Przenieś własne adresy IP.
- Poziom wpływu aplikacji i danych:
    - Jeśli istnieje możliwość, że aplikacje działają w regionach firmy Microsoft IL5, Utwórz wystąpienie SACA w IL5. Wystąpienie może być używane przed aplikacjami IL4 i IL5. Wystąpienie IL4 SACA przed aplikacją IL5 prawdopodobnie nie otrzyma akredytacji.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Który dostawca wirtualnego urządzenia sieciowego będzie używany na potrzeby usługi VDSS?
Jak wspomniano wcześniej, można skompilować to odwołanie SACA przy użyciu różnych urządzeń i usług platformy Azure. Firma Microsoft dysponuje zautomatyzowanymi szablonami rozwiązań w celu wdrożenia architektury SACA z programem F5 i Citrix. Te rozwiązania zostały omówione w poniższej sekcji.

#### <a name="which-azure-services-will-you-use"></a>Które usługi platformy Azure będą używane?
- Istnieją usługi platformy Azure, które mogą spełniać wymagania dotyczące funkcji log Analytics, ochrony opartej na hoście i identyfikatorów. Niektóre usługi nie są ogólnie dostępne w regionach Microsoft IL5. W takim przypadku może być konieczne użycie narzędzi innych firm, jeśli te usługi platformy Azure nie spełnią Twoich wymagań. Zapoznaj się z pożądanymi narzędziami i możliwością korzystania z narzędzi natywnych platformy Azure.
- Zalecamy używanie jak największej liczby natywnych narzędzi platformy Azure. Są one stworzone z myślą o zabezpieczeniach chmury i zapewniają bezproblemowe integrację z resztą platformy Azure. Użyj narzędzi macierzystych platformy Azure na poniższej liście, aby spełnić różne wymagania programu SCCA:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) 
    - [Moje drzwi platformy Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Grupy zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/overview)
- Zmiany rozmiaru
    - Należy ukończyć ćwiczenie ustalania rozmiarów. Sprawdź liczbę współbieżnych połączeń, które mogą być wykonywane przez wystąpienie SACA i wymagania dotyczące przepływności sieci. 
    - Ten krok ma krytyczne znaczenie. Pomaga w zmianie rozmiaru maszyn wirtualnych i zidentyfikowaniu licencji wymaganych od różnych dostawców używanych w wystąpieniu usługi SACA. 
    - Nie można przeprowadzić analizy kosztów z dobrymi kosztami. Poprawne ustalanie rozmiarów pozwala również na najlepszą wydajność. 


## <a name="most-common-deployment-scenario"></a>Najpopularniejszy scenariusz wdrażania

 Kilku klientów firmy Microsoft przeszedł w ramach pełnego wdrożenia lub co najmniej na etapie planowania środowiska SACA. Ich doświadczenia zostały ujawnione w najbardziej typowym scenariuszu wdrażania. Na poniższym diagramie przedstawiono najbardziej typową architekturę: 


![Diagram architektury referencyjnej SACA](media/sacav2commonscenario.png) 


Jak widać na diagramie, klienci DoD zazwyczaj subskrybują dwa z DISA BCAPs. Jeden z tych mieszka w zachodnich wybrzeżach i drugi mieszka na wschodnim wybrzeżu. Prywatny element równorzędny ExpressRoute jest włączony na platformie Azure w każdej lokalizacji BCAP DISA. Te elementy równorzędne ExpressRoute są następnie połączone z bramą sieci wirtualnej w regionach usługi DoD i DoD Central. Wystąpienie SACA jest wdrażane w regionach platformy Azure o DoD i DoD. Cały ruch przychodzący i ruchu wychodzącego odbywa się za pośrednictwem go do i z połączenia ExpressRoute z DISA BCAP.

Aplikacje będące właścicielami i wybierają regiony platformy Azure, w których planują wdrożyć swoje aplikacje. Korzystają one z wirtualnej sieci równorzędnej do łączenia sieci wirtualnej aplikacji z siecią wirtualną SACA. Następnie wymusić tunelowanie całego ruchu przez wystąpienie VDSS.

Zalecamy, aby ta architektura spełniała wymagania SCCA. Jest wysoce dostępny i łatwo skalowalny. Upraszcza to również wdrażanie i zarządzanie.

## <a name="automated-saca-deployment-options"></a>Zautomatyzowane opcje wdrażania SACA

 Jak wspomniano wcześniej, firma Microsoft połączyła się z dwoma dostawcami, aby utworzyć zautomatyzowany szablon infrastruktury SACA. Oba szablony wdrażają następujące składniki platformy Azure: 

- Sieć wirtualna SACA
    - Podsieć zarządzania
        - Ta podsieć polega na tym, że maszyny wirtualne i usługi zarządzania są wdrażane, znane także jako pola skoku.
        - Podsieć VDMS
            - Ta podsieć to miejsce, w którym są wdrażane maszyny wirtualne i usługi używane na potrzeby VDMS.
        - Niezaufane i zaufane podsieci
            - W tych podsieciach są wdrażane urządzenia wirtualne.
        - Podsieć bramy
            - W tej podsieci jest wdrożona Brama ExpressRoute.
- Zarządzanie maszynami wirtualnymi w polu skoku
    - Są one używane do zarządzania poza pasmem środowiska.
- Wirtualne urządzenia sieciowe
    - Używasz programu Citrix lub F5 na podstawie wdrażanego szablonu.
- Publiczne adresy IP
    - Są one używane na potrzeby frontonu do momentu przełączenia ExpressRoute do trybu online. Te adresy IP są tłumaczone na prywatną przestrzeń adresową platformy Azure.
- Tabele tras 
    - Stosowane podczas automatyzacji te tabele są używane w celu wymuszania tunelowania całego ruchu przez urządzenie wirtualne.
- Moduły równoważenia obciążenia platformy Azure — standardowa jednostka SKU
    - Są one używane do równoważenia obciążenia ruchu między urządzeniami.
- Grupy zabezpieczeń sieci
    - Są one używane do kontrolowania, które typy ruchu mogą przechodzić do określonych punktów końcowych.


### <a name="citrix-saca-deployment"></a>Wdrożenie Citrix SACA

Szablon wdrożenia Citrix wdraża dwie warstwy urządzeń ADC Citrix o wysokiej dostępności. Ta architektura spełnia wymagania VDSS. 

![Diagram Citrix SACA](media/citrixsaca.png)


Aby zapoznać się z dokumentacją i skryptem wdrażania programu Citrix, zobacz link do usługi [GitHub](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>Wdrożenie F5 SACA

Dwa oddzielne szablony wdrożenia F5 obejmują dwie różne architektury. Pierwszy szablon zawiera tylko jedną warstwę urządzeń F5 w konfiguracji usługi Active-Active o wysokiej dostępności. Ta architektura spełnia wymagania VDSS. Drugi szablon dodaje drugą warstwę F5s o wysokiej dostępności. Ta druga warstwa umożliwia klientom dodanie własnych adresów IP niezależnie od F5 między warstwami F5. Nie wszystkie składniki DoD mają określone adresy IP do użycia. W takim przypadku pojedyncza warstwa urządzeń F5 działa w większości, ponieważ ta architektura obejmuje adresy IP na urządzeniach F5.

![Diagram F5 SACA](media/f5saca.png)

Aby zapoznać się z dokumentacją i skryptem wdrażania, zobacz [ten link](https://github.com/f5devcentral/f5-azure-saca)w witrynie GitHub.












