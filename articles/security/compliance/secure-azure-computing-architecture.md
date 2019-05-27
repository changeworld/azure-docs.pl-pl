---
title: Zabezpieczanie architektury obliczeniowej platformy Azure
description: Ta architektura referencyjna dla architektury sieci Obwodowej klasy korporacyjnej korzysta z wirtualnych urządzeń sieciowych oraz innych narzędzi. Ta architektura została zaprojektowana pod kątem wymagań Departamentu Obrony Secure Cloud Computing architektury funkcjonalności. Również może służyć dla każdej organizacji. Ta dokumentacja zawiera dwie opcje automatycznego, korzystających z urządzeń Citrix lub F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 017a26d5672f666d4d8eaf629a0f53fe0cfe517f
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963239"
---
# <a name="secure-azure-computing-architecture"></a>Zabezpieczanie architektury obliczeniowej platformy Azure

Federalna Klienci Departament Obrony (DoD), którzy wdrażają obciążenia na platformie Azure poprosiło o opis wskazówki dotyczące konfigurowania bezpiecznej sieci wirtualnych i konfigurowania usług, które są określone przez Departament Obrony standardów i praktyki i narzędziom zabezpieczającym. 

Opublikowane Defense Information System Agency (DISA) [interfejsu Secure Cloud Computing architektury (SCCA) dokumentu wymagania funkcjonalne (FRD)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) w 2017 r. SCCA opisano cele funkcjonalności do zabezpieczania Defense Information System sieci (DISN) i chmury komercyjnej punkty połączenia dostawcy. SCCA opisano również, jak właścicieli misji bezpieczne w chmurze aplikacje na granicy połączenia. Każda jednostka DoD, który nawiązuje połączenie z komercyjnej chmury, należy wykonać wytycznych określonych w SCCA FRD.
 
SCCA ma cztery składniki:
 
- Punkt dostępu granic w chmurze (BCAP)
- Wirtualne centrum danych zabezpieczeń stosu (VDSS)
- Wirtualne centrum danych Managed Service (procesy VDM)
- Trusted Cloud Credential Manager (TCCM) 

Firma Microsoft opracowała rozwiązanie spełniające wymagania SCCA IL4 i IL5 obciążeń, które działają na platformie Azure. To rozwiązanie specyficzne dla platformy Azure jest nazywany Secure Azure obliczeń architektury (algorytmu SACA). Klienci, którzy wdrażają algorytmu SACA architektonicznymi SCCA FRD. Umożliwiają one DoD klientom przenoszenie obciążeń na platformę Azure, po połączeniu.

Wskazówki dotyczące SCCA i architektur są specyficzne dla klientów DoD, ale najnowszych poprawek klientom cywilnego algorytmu SACA pomocy są zgodne z zaufanych wskazówki połączenia (tablica) internet. Najnowsze wersje są również pomóc w komercyjnych klientów, którzy potrzebują do implementowania bezpiecznej sieci Obwodowej, do ochrony swoich środowisk platformy Azure.


## <a name="secure-cloud-computing-architecture-components"></a>Zabezpieczanie składników architektury obliczeń w chmurze

### <a name="bcap"></a>BCAP

BCAP ma na celu ochronę DISN przed atakami w środowisku chmury. BCAP przeprowadza wykrywanie włamań i zapobiegania. odfiltrowuje również nieautoryzowanego ruchu. Ten składnik można wspólnie z innymi składnikami SCCA. Firma Microsoft zaleca wdrażanie tego składnika przy użyciu sprzętu fizycznego. Wymagania dotyczące zabezpieczeń BCAP są wymienione w poniższej tabeli.

#### <a name="bcap-security-requirements"></a>Wymagania dotyczące zabezpieczeń BCAP

![Tabela wymagań dotyczących BCAP](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

VDSS ma na celu ochronę DoD misji właściciela aplikacji, które są hostowane na platformie Azure. VDSS wykonuje większość operacji zabezpieczeń SCCA. System przeprowadza inspekcję ruchu w celu zabezpieczenia aplikacji, które działają na platformie Azure. Ten składnik można uwzględnić w danym środowisku platformy Azure.

#### <a name="vdss-security-requirements"></a>Wymagania dotyczące zabezpieczeń VDSS

![Tabela wymagań dotyczących VDSS](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

Celem maszyny VDM ma na celu dostarczenie zabezpieczenia hosta i udostępnionych usług centrum danych. Funkcje maszyny VDM może zostać uruchomiony w Centrum usługi SCCA lub właściciela misji można wdrażać fragmentów w ich własnej subskrypcji platformy Azure. Ten składnik można uwzględnić w danym środowisku platformy Azure.

#### <a name="vdms-security-requirements"></a>Wymagania dotyczące zabezpieczeń maszyny VDM

![Tabela wymagań dotyczących procesów VDM](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM jest roli biznesowej. Osoba ta będzie odpowiedzialny za zarządzanie SCCA. Ich obowiązków są: 

- Ustanów planów i zasady dotyczące konta dostępu do środowiska chmury. 
- Upewnij się, czy Zarządzanie tożsamościami i dostępem działa prawidłowo. 
- Obsługa planu zarządzania poświadczeń w chmurze. 

Osoba ta jest wyznaczony przez official będzie przydatna autoryzacji. BCAP VDSS i procesy VDM zapewniają możliwości, które TCCM potrzebnych do wykonania swojej pracy.

#### <a name="tccm-security-requirements"></a>Wymagania dotyczące zabezpieczeń TCCM

![Tabela wymagań dotyczących TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Składniki algorytmu SACA i zagadnienia dotyczące planowania 

Architektura referencyjna algorytmu SACA zaprojektowano, aby wdrożyć składniki VDSS i procesy VDM na platformie Azure i umożliwić TCCM. Ta architektura jest moduły. Wszystkie elementy VDSS i procesy VDM znajdować się w Centrum. Niektóre kontrolki mogą zostać spełnione w przestrzeni misji właściciela lub nawet lokalnie. Firma Microsoft zaleca wspólne umieszczanie składniki VDSS i procesy VDM w centralnej sieci wirtualnej, który wszyscy właściciele misji może nawiązać połączenie za pośrednictwem. Na poniższym diagramie przedstawiono tej architektury: 


![Diagram architektury odwołanie algorytmu SACA](media/sacav2generic.png)

Podczas planowania usługi SCCA zgodności strategii i architekturą techniczną, rozważ następujące tematy od samego początku, ponieważ mają one wpływ na każdy klient korzystający z. Następujące problemy mają stworzyć DoD klientów i zwykle spowolnienia planowania i wykonywania. 

#### <a name="which-bcap-will-your-organization-use"></a>Które BCAP będą korzystać z Twojej organizacji?
   - DISA BCAP:
        - DISA ma dwa operacyjne BCAPs Pentagon i Robertsa spotkanie Camp, urząd certyfikacji. Trzecia jest planowane, aby szybko przejść w tryb online. 
        - Wszystkie mają obwodów usługi ExpressRoute platformy Azure na platformę Azure, która może służyć przez klientów DoD łączności BCAPs DISA firmy. 
        - DISA ma sesję komunikacji równorzędnej firmy Microsoft klasy korporacyjnej dla klientów DoD, którzy chcesz subskrybować oprogramowania firmy Microsoft jako narzędzia do usługi (SaaS), takich jak Office 365. Za pomocą DISA BCAP, aby umożliwić łączność i komunikacji równorzędnej z wystąpieniem algorytmu SACA. 
    - Tworzenie własnych BCAP:
        - Ta opcja wymaga dzierżawy miejsca w centrum danych ze wspólną lokalizacją i konfigurowanie obwodu usługi ExpressRoute na platformie Azure. 
        - Ta opcja wymaga dodatkowego zatwierdzenia. 
        - Z powodu dodatkowego zatwierdzenia i fizycznych poza kompilacji opcja ta zajmuje najwięcej czasu. 
    - Zaleca się, że używasz DISA BCAP. Ta opcja jest łatwo dostępne, ma wbudowane funkcje nadmiarowości i ma klientów, które działają na niej obecnie w środowisku produkcyjnym.
- DoD routingowi przestrzeń adresów IP:
    - Należy użyć DoD Routing IP miejsca na krawędzi sieci. Opcja Połącz te spacje do prywatnej przestrzeni adresów IP na platformie Azure przy użyciu translatora adresów Sieciowych jest dostępna.
    - Skontaktuj się z Centrum informacji sieciowych (NIC) DoD, można uzyskać przestrzeni adresów IP. Należy go jako część procesu zatwierdzania systemu i sieci (PRZYSTAWKI) przesłanych informacji z DISA. 
    - Planując łączenie z prywatnej przestrzeni adresowej na platformie Azure przy użyciu translatora adresów Sieciowych, potrzebujesz co najmniej prefiksie/24 podsieci przypisane z karty Sieciowej dla każdego regionu, w którym planujesz wdrożyć algorytmu SACA przestrzeń adresowa.
- Nadmiarowość:
    - Wdróż wystąpienie algorytmu SACA na co najmniej dwóch regionach. W chmurze DoD możesz wdrożyć oba dostępne regiony DoD.
    - Połączyć się z co najmniej dwóch BCAPs za pośrednictwem osobnych obwodów usługi ExpressRoute. Można następnie połączyć obu połączeń ExpressRoute do każdego regionu algorytmu SACA wystąpienia. 
- DoD wymagań specyficznych dla składnika:
    - Czy Twoja organizacja ma szczególne wymagania poza wymagania SCCA? Niektóre organizacje mają szczególne wymagania adresów IP.
- Algorytmu SACA jest modularnej architekturze:
    - Użyj tylko składniki potrzebne dla danego środowiska. 
        - Wdrażanie wirtualnych urządzeń sieciowych w jednej warstwy lub wielowarstwowych.
        - Użyj zintegrowanych, bring your own adresy IP lub adresów IP.
- DoD poziom wpływu na aplikacje i dane:
    - W przypadku możliwości aplikacji działających w regionach IL5 firmy Microsoft, tworzyć wystąpienia algorytmu SACA w IL5. Wystąpienie może służyć przed IL4 aplikacji i IL5. Wystąpienie algorytmu SACA IL4 przed aplikacją IL5 najprawdopodobniej nie będą wysyłane akredytacji.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Którego dostawcy wirtualnego urządzenia sieciowego będzie używana VDSS?
Jak wspomniano wcześniej, możesz tworzyć odwołanie do tego algorytmu SACA przy użyciu różnych urządzeń i usług platformy Azure. Microsoft ma zautomatyzowane szablony rozwiązań, aby wdrożyć architekturę algorytmu SACA zarówno F5 i Citrix. Rozwiązania te zostały uwzględnione w poniższej sekcji.

#### <a name="which-azure-services-will-you-use"></a>Które usługi platformy Azure będą korzystać?
- Istnieją usług platformy Azure spełniających wymagania dotyczące usługi log analytics, ochrona oparta na hoście i Identyfikatory funkcji. Istnieje możliwość, że niektóre usługi są ogólnie dostępne w regionach IL5 firmy Microsoft. W takim przypadku może być konieczne użycie narzędzi innych firm, jeśli te usługi platformy Azure nie spełnia Twoich wymagań. Przyjrzyj się komfortowo, jednocześnie narzędzia i możliwości wykorzystania Azure natywnych narzędzi.
- Zalecamy użycie tyle natywnych narzędzi platformy Azure jak to możliwe. One jest zbudowany z myślą o bezpieczeństwie chmury oraz bezproblemowo integrują się z pozostałą częścią platformy Azure. Używają natywnych narzędzi platformy Azure na poniższej liście, aby zaspokoić różne wymagania SCCA:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Zaporę platformy Azure](https://docs.microsoft.com/azure/firewall/overview) 
    - [Drzwi wejściowe platformy Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Grupy zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Zmiana rozmiaru
    - Wykonywanie zmiany rozmiaru, należy wykonać. Spójrz na liczbę jednoczesnych połączeń, może być za pośrednictwem wystąpienia algorytmu SACA i wymagania dotyczące przepustowości sieci. 
    - Ten krok ma kluczowe znaczenie. Pomaga ono rozmiar maszyn wirtualnych i zidentyfikować licencji, które są wymagane pochodzących od różnych dostawców, używanej w ramach wystąpienia algorytmu SACA. 
    - Nie można wykonać analizy kosztów dobre bez tego ćwiczenia zmiany rozmiaru. Aby uzyskać najlepszą wydajność umożliwia także poprawne zmiany rozmiaru. 


## <a name="most-common-deployment-scenario"></a>Najbardziej typowym scenariuszem wdrażania

 Wykonano kilka klientów firmy Microsoft, za pomocą pełnego wdrożenia lub w co najmniej etapy planowania środowisk algorytmu SACA. Doświadczeniem ujawniło wgląd w najbardziej typowych scenariuszy wdrożenia. Na poniższym diagramie przedstawiono najbardziej popularną architekturę: 


![Diagram architektury odwołanie algorytmu SACA](media/sacav2commonscenario.png) 


Jak widać na diagramie, klienci DoD zazwyczaj subskrybować dwa DISA BCAPs. Jeden z nich znajduje się na zachodnim wybrzeżu i innych życie na wschodnim wybrzeżu. Usługi ExpressRoute, prywatne, równorzędna jest włączona na platformie Azure w poszczególnych lokalizacjach DISA BCAP. Tych elementów równorzędnych usługi ExpressRoute są następnie łączone z bramy sieci wirtualnej w regionach dod — wschodnie Stany i Azure centralny Departament Obrony. Wystąpienie algorytmu SACA jest wdrażany w regionach dod — wschodnie Stany i Azure centralny Departament Obrony. Cały ruch przychodzący i wychodzący przepływa przez nią do i z połączeniami ExpressRoute DISA BCAP.

Aplikacje o znaczeniu właściciela Wybierz regiony platformy Azure, w których planujesz wdrożyć swoje aplikacje. Oni korzystać z sieci wirtualnej komunikacji równorzędnej sieci wirtualnej algorytmu SACA nawiązać połączenia sieć wirtualna swoich aplikacji. A następnie ich wymuszonego tunelowania ich ruchu za pośrednictwem wystąpienia VDSS.

Firma Microsoft zaleca tej architektury, ponieważ spełnia on wymagania SCCA. Jest wysoce dostępny i łatwe skalowanie. Upraszcza również wdrażanie i zarządzanie nimi.

## <a name="automated-saca-deployment-options"></a>Automatyczne opcje wdrażania algorytmu SACA

 Jak wcześniej wspomniano, firma Microsoft nawiązała współpracę z dwóch dostawcom tworzenie zautomatyzowanego szablonu infrastruktury algorytmu SACA. Oba szablony wdrażają następujące składniki platformy Azure: 

- Sieć wirtualna algorytmu SACA
    - Podsieć zarządzania
        - Ta podsieć jest, gdzie są wdrożone zarządzania maszynami wirtualnymi i usługami, tzw. okna przeskoku.
        - Procesy VDM podsieci
            - Ta podsieć jest, gdzie są wdrożone maszyny wirtualne i usługi używane przez procesy VDM.
        - Podsieci niezaufanych i zaufanych
            - Te podsieci są, których urządzenia wirtualne są wdrażane.
        - Podsieć bramy
            - Ta podsieć jest wdrożonym bramę usługi ExpressRoute.
- Maszyny wirtualne zarządzania okno przeskoku
    - Służą one do zarządzania poza pasmem środowiska.
- Wirtualne urządzenia sieciowe
    - Możesz użyć albo Citrix lub F5 oparte na szablon, który można wdrożyć.
- Publiczne adresy IP
    - Służą one do frontonu do momentu usługi ExpressRoute jest przełączany w tryb online. Tłumaczenie tych adresów IP do zaplecza platformy Azure prywatnej przestrzeni adresowej.
- Tabele tras 
    - Zastosowana podczas automatyzacji, te trasy tunelowania wymuszonego tabel cały ruch przez urządzenie wirtualne.
- Moduły równoważenia obciążenia platformy Azure — standardowa jednostka SKU
    - Służą one do Równoważenie obciążenia ruchem do urządzeń.
- Sieciowe grupy zabezpieczeń
    - Służą one do formantu, który typów ruchu mogą przechodzić do określonych punktów końcowych.


### <a name="citrix-saca-deployment"></a>Wdrożenia Citrix algorytmu SACA

Szablon wdrożenia Citrix wdraża dwie warstwy o wysokiej dostępności urządzeń Citrix ADC. Ta architektura spełnia wymagania VDSS. 

![Diagram algorytmu SACA Citrix](media/citrixsaca.png)


Dokumentacja Citrix i skryptu wdrażania, zobacz [ten link GitHub](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>Wdrożenie F5 algorytmu SACA

Dwa oddzielne szablony wdrożenie F5 obejmuje dwie różne architektury. Pierwszy szablon ma tylko jedną warstwę urządzeń F5 w konfiguracji aktywne aktywne o wysokiej dostępności. Ta architektura spełnia wymagania dotyczące VDSS. Drugi szablon dodaje drugą warstwę F5s o wysokiej dostępności aktywny aktywny. Ta druga warstwa umożliwia klientów o dodanie własnych adresów IP, niezależnie od F5 między warstwami F5. Nie wszystkie składniki DoD mają określonych adresów IP określonych do użycia. Jeśli tak jest rzeczywiście, jedna warstwa urządzenia F5 działa w przypadku większości ponieważ danej architekturze zawiera adresy IP na urządzeniach F5.

![Diagram algorytmu SACA F5](media/f5saca.png)

Dokumentacja F5 i skryptu wdrażania, zobacz [ten link GitHub](https://github.com/f5devcentral/f5-azure-saca).












