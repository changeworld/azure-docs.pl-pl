---
title: Zabezpieczanie usługi Azure architektury przetwarzania
description: Jest to architektura referencyjna dla architektury sieci Obwodowej klasy korporacyjnej, korzystanie z wirtualnych urządzeń sieciowych oraz innych narzędzi. Ta architektura została zaprojektowana pod kątem wymagań Departamentu Obrony Secure Cloud Computing architektury funkcjonalności. Jednak można użyć dla każdej organizacji. To odniesienie zawiera dwie opcje automatycznego za pomocą urządzenia Citrix lub F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: f2e3d72db3f29dbc6d03b3259acb18daf684fb12
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917590"
---
# <a name="secure-azure-computing-architecture"></a>Zabezpieczanie usługi Azure architektury przetwarzania

Wskazówki dotyczące bezpiecznego sieci wirtualnych i konfigurowania usług określonych przez Departament Obrony standardów i praktyki i narzędziom zabezpieczającym prosili szybko rosnącej liczby klientów DoD, wdrażania obciążeń na platformie Azure. DISA opublikowane [funkcjonalności dokumentu wymagania interfejsu Secure Cloud Computing architektury (SCCA)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) w 2017 r. SCCA opisuje funkcjonalności cele dotyczące zabezpieczania Defense Information System sieci (DISN) i połączenia dostawcy chmury komercyjnej wskazuje i jak o znaczeniu strategicznym, właścicieli aplikacji bezpiecznej chmury na granicy połączenia. Jest upoważnione, że każda jednostka DoD, który nawiązuje połączenie z komercyjnej chmury następuje wytycznych określonych w SCCA FRD.
 
Istnieją cztery składniki SCCA. Punkt dostępu granic w chmurze (BCAP), stosu Security wirtualnego centrum danych (VDSS), wirtualnego centrum danych (procesy VDM) usług zarządzanych i zaufane Menedżer poświadczeń w chmurze (TCCM). Firma Microsoft opracowała rozwiązanie, które będzie spełniać wymagania SCCA IL4 i IL5 obciążeń działających na platformie Azure. To rozwiązanie platformy Azure dla określonych nosi nazwę Secure Azure obliczeń architektury (algorytmu SACA). Klienci, którzy wdrażają algorytmu SACA będzie miało miejsce SCCA FRD i umożliwi klientom DoD przenoszenie obciążeń do platformy Azure po połączeniu. 

Mimo że wskazówki SCCA i architektur specyficzne dla klientów DoD, najnowszych poprawek do algorytmu SACA ułatwi również cywilnego klientów, które są zgodne z zaufanych wskazówki połączenia (tablica) internet, a także komercyjnych klientów, którzy chcą zaimplementować strefę DMZ bezpieczny do Chroń swoje środowiska platformy azure.


## <a name="secure-cloud-computing-architecture-components"></a>Zabezpieczanie składników architektury obliczeń w chmurze

**BCAP**

BCAP ma na celu ochronę DISN przed atakami w środowisku chmury. BCAP będzie wykonywać wykrywania włamań i zapobiegania im oraz jak również odfiltrować nieautoryzowanego ruchu. Ten składnik można wspólnie z innymi składnikami SCCA. Zdecydowanie zaleca się, że ten składnik zostanie wdrożony przy użyciu sprzętu fizycznego. Poniżej znajdziesz listę wymagań dotyczących zabezpieczeń BCAP.

***Wymagania dotyczące zabezpieczeń BCAP***

![Tabela wymagań dotyczących BCAP](media/bcapreqs.png)


**VDSS**

VDSS ma na celu ochronę aplikacji właściciela misji DoD, które są hostowane na platformie Azure. VDSS wykonuje większość operacji zabezpieczeń SCCA. Przeprowadzi inspekcję ruchu w celu zabezpieczenia aplikacji działających na platformie Azure. Ten składnik można uwzględnić w danym środowisku platformy Azure.

***Wymagania dotyczące zabezpieczeń VDSS***

![Tabela wymagań dotyczących VDSS](media/vdssreqs.png)

**VDMS**

Celem maszyny VDM ma na celu dostarczenie zabezpieczenia hosta oraz udostępnionych usług centrum danych. Funkcje maszyny VDM może zostać uruchomiony w Centrum usługi SCCA lub właściciela misji można wdrażać fragmentów w ich własnej subskrypcji platformy Azure. Ten składnik można uwzględnić w danym środowisku platformy Azure.

***Wymagania dotyczące zabezpieczeń maszyny VDM***

![Tabela wymagań dotyczących procesów VDM](media/vdmsreqs.png)


**TCCM**

TCCM jest roli biznesowej. Ta osoba będzie odpowiedzialny za zarządzanie SCCA. Ich obowiązków obejmują planów i zasady dotyczące konta dostępu do środowiska chmury, zapewniając tożsamości i zarządzanie dostępem działa prawidłowo i zachować planu zarządzania poświadczenia chmury. Ta osoba jest wyznaczony przez oficjalne autoryzacji. BCAP VDSS i procesy VDM zapewni możliwości wymagane przez TCCM do wykonywania ich zadań funkcji.

***Wymagania dotyczące zabezpieczeń TCCM***

![Tabela wymagań dotyczących TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Składniki algorytmu SACA i zagadnienia dotyczące planowania 

Architektura referencyjna algorytmu SACA jest przeznaczony do wdrażania składników VDSS i procesy VDM na platformie azure, a także włączyć TCCM. Ta architektura jest modułowe, co oznacza, że wszystkie elementy VDSS i procesy VDM znajdują się w Centrum lub niektóre kontrolki mogą zostać spełnione w misji właściciela miejsca lub nawet lokalnie. Zalecenia z zespołem firmy Microsoft jest na jednej taśmie VDSS i procesy VDM składników do centralnej wirtualnego netto, wszyscy właściciele misji można łączyć za pomocą. Poniższy diagram przedstawia nasze zalecane architektury. 


![Diagram architektury odwołanie algorytmu SACA](media/sacav2generic.png)

Planując architektura technicznych i SCCA zgodności strategii, na których istnieje wiele rzeczy, aby wziąć pod uwagę. Ważne jest, że następujące tematy są brane pod uwagę od początku, ponieważ każdy klient będzie musiał obejmują one. Poniższe tematy zostały problemów, które mają stworzyć rzeczywistych klientów DoD, zwykle spowolnienia planowania i wykonywania. 

- Które BCAP będą korzystać z Twojej organizacji?
    - DISA BCAP
        - DISA ma dwa operacyjne BCAPs Pentagon, a spotkanie Camp urząd certyfikacji Robertsa, za pomocą innego wkrótce online. 
        - Wszystkie mają obwodów usługi ExpressRoute na platformie Azure, które mogą zostać wykorzystane przez klientów DoD łączności BCAPs DISA firmy. 
        - DISA już sesję Peering firmy Microsoft klasy korporacyjnej dla klientów DoD, którzy chcesz subskrybować narzędzi SaaS firmy Microsoft, takich jak usługi Office 365. Za pomocą DISA BCAP, aby umożliwić łączność i komunikacji równorzędnej z wystąpieniem algorytmu SACA. 
    - Tworzenie własnych BCAP
        - To będzie wymagać dzierżawy miejsca w centrum danych ze wspólną lokalizacją i konfigurowanie obwodu usługi ExpressRoute na platformie Azure. 
        - Ta opcja wymaga dodatkowego zatwierdzenia 
        - Z powodu dodatkowego zatwierdzenia i fizycznych kompilacji w poziomie opcja ta zajmuje najwięcej czasu. 
    - Jest firma Microsoft zaleca korzystanie z DISA BCAP. Ta opcja są łatwo dostępne, ma wbudowane nadmiarowości i ma już klientów działających na nim obecnie w środowisku produkcyjnym.
- DoD routingowi przestrzeni adresów IP
    - Będą musieli używać miejsca na DoD Routing IP na krawędzi sieci. Możliwość translatora adresów Sieciowych, te do prywatnej przestrzeni adresów IP na platformie Azure jest dostępna.  
    - Skontaktuj się z pomocą DoD kart interfejsu Sieciowego, aby uzyskać przestrzeni adresów IP będą potrzebne w ramach PRZYSTAWKI przesłanych informacji z DISA. 
    - Jeśli planujesz translatora adresów Sieciowych do prywatnej przestrzeni adresowej na platformie Azure, konieczne będzie co najmniej prefiksie/24 podsieci przypisane z karty Sieciowej dla każdego regionu, którą chcesz wdrożyć algorytmu SACA przestrzeń adresowa. 
- Nadmiarowość 
    - Firma Microsoft sugeruje, Wdróż wystąpienie algorytmu SACA na co najmniej dwóch regionach. W chmurze DoD oznaczałoby to sposób wdrażania oba dostępne regiony DoD. 
    - Zalecane jest również, że łączysz się co najmniej dwóch BCAPs za pośrednictwem osobnych obwodów usługi ExpressRoute. Obydwie trasy Express można następnie połączyć do każdego regionu algorytmu SACA wystąpienia. 
- Wymagań specyficznych dla składnika DoD
    - Czy Twoja organizacja ma szczególne wymagania poza wymagania SCCA? (Niektóre organizacje mają szczególne wymagania adresów IP)
- Algorytmu SACA jest modularnej architekturze  
    - Użyj tylko składniki potrzebne dla danego środowiska. 
        - Wdrażanie urządzenia WUS w jednej warstwy lub wielowarstwowych
        - Zintegrowane adresy IP lub dostarczenie własnych adresów IP
- Poziom wpływu DoD, aplikacji i danych
    - W przypadku możliwości aplikacji działających w innych regionach IL5 zaleca się utworzenie wystąpienia algorytmu SACA w IL5. Wystąpienie może służyć przed IL4 aplikacji, a także IL5. Jednak wystąpienie algorytmu SACA IL4 przed aplikacją IL5 najprawdopodobniej nie będą otrzymywali akredytacji. 
- Dostawcy wirtualnego urządzenia sieciowego, które ma zostać użyty do VDSS?
    - Jak wspomniano wcześniej, to odwołanie algorytmu SACA może być skompilowana przy użyciu różnych urządzeń i usług platformy Azure. Jednak mamy szablony zautomatyzowane rozwiązanie, aby wdrożyć architekturę algorytmu SACA zarówno F5 i Citrix. Rozwiązania te zostały omówione bardziej szczegółowo poniżej. 
- Które usługi platformy Azure będą korzystać?
    - Istnieją usług platformy Azure spełniających wymagania wokół usługi log analytics, ochrona oparta na hoście i Identyfikatory funkcji. Jednak jest możliwe, że niektóre usługi są ogólnie dostępne w innych regionach IL5. Może to prowadzić do konieczność stosowania niektórych 3rd narzędzi innych firm, w przypadku tych usług platformy Azure nie spełnia Twoje wymagania. Należy przyjrzeć się, jakich narzędzi są doświadczenia z oraz możliwości wykorzystania Azure natywnych narzędzi. 
    - Jest firma Microsoft zaleca użycie tyle Azure natywnych narzędzi możliwie są wbudowane z myślą o bezpieczeństwie chmurze i bezproblemowo integrują się z pozostałą część platformy Azure. Poniżej znajduje się lista natywnych narzędzi platformy Azure, które można wykorzystać w celu spełnienia różnych wymagań SCCA. 
        - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
        - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
        - [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
        - [Usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
        - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
        - [Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
        - [Zaporę platformy Azure](https://docs.microsoft.com/azure/firewall/overview) 
        - [Drzwi wejściowe platformy Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
        - [Grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview)
        - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
        - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Ustalanie rozmiaru
    - Ćwiczenie rozmiaru będzie należy wykonać. Konieczne będzie Spójrz na liczbę jednoczesnych połączeń, które mogą wiązać Ciebie za pomocą algorytmu SACA wystąpienia, a także wymagania dotyczące przepustowości sieci. 
    - Jest to niezwykle ważne, ponieważ jej będzie ułatwiają rozmiar maszyn wirtualnych, a także pomóc w ustaleniu, które będą wymagane pochodzących od różnych dostawców, który będzie używany w ramach wystąpienia algorytmu SACA licencji. 
    - Nie można wykonać analizy kosztów dobre bez tego ćwiczenia zmiany rozmiaru, ważne jest również zapewnienie wszystko, co jest użyłeś pojemników umożliwia, aby uzyskać najlepszą wydajność. 


## <a name="most-common-deployment-scenario"></a>Najbardziej typowym scenariuszem wdrażania

Firma Microsoft ma wielu klientów, którzy już przeszli pełną wdrożenia lub co najmniej planowania etapów środowisk algorytmu SACA. Ma to umożliwiło nam uzyskiwanie wglądu w najbardziej typowych scenariuszy wdrożenia. Poniższy diagram przedstawia najbardziej typowe architektury. 


![Diagram architektury odwołanie algorytmu SACA](media/sacav2commonscenario.png) 


Jak widać na diagramie, klienci DoD zazwyczaj subskrybować dwa BCAPs DISA, jeden z tych życie, na zachodnim wybrzeżu i życie na wschodnim wybrzeżu. Równorzędnej usługi ExpressRoute prywatnych jest włączone na platformie Azure w poszczególnych lokalizacjach DISA BCAP. Tych elementów równorzędnych usługi ExpressRoute są następnie łączony z bramy sieci wirtualnej w dod — wschodnie Stany i DoD centralnej regionów platformy Azure. Wystąpienie algorytmu SACA jest wdrażane w regionie dod — wschodnie Stany i Azure centralny Departament Obrony i wszystkie przychodzące i wychodzące przepływy ruchu przez nią do i z połączenia Expressroute DISA BCAP. 

Centrum właściciela aplikacji wybierz, które regiony platformy Azure będzie wdrażanych aplikacji w usłudze i użyj komunikacji równorzędnej sieci wirtualnej do łączenia z aplikacji przez sieć wirtualną z siecią wirtualną algorytmu SACA. Wymuszanie tunelowania całego ruchu ich za pośrednictwem wystąpienia VDSS. 

Ta architektura zdecydowanie zaleca się przez firmę Microsoft, jak będzie on spełniać wymagania SCCA, jest wysoce dostępne, łatwe skalowanie i upraszcza wdrażanie i zarządzanie nimi.

## <a name="automated-saca-deployment-options"></a>Automatyczne opcje wdrażania algorytmu SACA

 Wspomnieliśmy wcześniej, że firma Microsoft nawiązała współpracę z dwóch dostawcom tworzenie zautomatyzowanego szablonu infrastruktury algorytmu SACA. Oba szablony wdroży następujące składniki platformy Azure. 

- SACA Virtual Network
    - Podsieć zarządzania
        - Wdrożonym Zarządzanie maszyny wirtualne i usługi (serwerom przejściowym)
        - Procesy VDM podsieci
            - Gdzie są wdrożone maszyny wirtualne i usługi używane przez procesy VDM
        - Podsieci niezaufanych i zaufanych 
            - Wdrożonym urządzeń wirtualnych
        - Podsieć bramy
            - Wdrożonym bramę usługi ExpressRoute
- Maszyny wirtualne zarządzania okno przeskoku
    - Używany do zarządzania poza pasmem środowiska.
- Wirtualne urządzenia sieciowe
    - Citrix lub F5, w zależności od szablonu wdrożenia.
- Publiczne adresy IP
    - Używane dla frontonu, dopóki usługa ExpressRoute jest przełączany w tryb online. Te adresy IP przekształci się w wewnętrznej bazie danych Azure prywatnej przestrzeni adresowej
- Tabele tras 
    - Stosowane podczas automatyzacji, te trasy tabel tunelowania wymuszonego całego ruchu przez wirtualne urządzenie
- Moduły równoważenia obciążenia platformy Azure — standardowa jednostka SKU
    - Są one używane do równoważenia obciążenia ruchu między urządzenia
- Grupy zabezpieczeń sieci
    - Używane do kontrolowania, jakie rodzaje ruchu mogą przechodzić do określonych punktów końcowych


**Wdrożenia Citrix algorytmu SACA**

Citrix utworzył szablon wdrożenia, który wdraża to dwie warstwy o wysokiej dostępności urządzeń Citrix ADC. Ta architektura spełnia wymagania VDSS. 

![Diagram algorytmu SACA Citrix](media/citrixsaca.png)


Dokumentacja Citrix i wdrożenie skryptu można znaleźć [tutaj.](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)


 **Wdrożenie F5 algorytmu SACA**

F5 zostało utworzone dwa szablony wdrażane pojedynczo, obejmujących dwie różne architektury. Pierwsza z nich ma tylko jedną warstwę urządzeń F5 w konfiguracji aktywne aktywne o wysokiej dostępności. Ta architektura spełnia wymagania dotyczące VDSS. Drugi dodaje drugą warstwę F5s o wysokiej dostępności aktywny aktywny. Celem tej drugiej warstwy jest aby umożliwić klientom dodawanie własnych adresów IP, niezależnie od F5 między warstwami F5. Nie wszystkie składniki DoD mają określonych adresów IP określonych do użycia. Jeśli tak jest rzeczywiście, jedna warstwa urządzenia F5 będzie działać dla najbardziej od czy architektura zawiera adresy IP na urządzeniach F5.  

![Diagram algorytmu SACA Citrix](media/f5saca.png)

Dokumentacja F5 i wdrożenie skryptu można znaleźć [tutaj.](https://github.com/f5devcentral/f5-azure-saca) 












