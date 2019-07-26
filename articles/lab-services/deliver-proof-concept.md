---
title: Dostarcz potwierdzenie koncepcji — Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak dostarczać weryfikację koncepcji, aby Azure DevTest Labs można było pomyślnie włączyć w środowisku przedsiębiorstwa.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: 6739679b1687393737dda1ded4265a95c4fce169
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501548"
---
# <a name="delivering-a-proof-of-concept"></a>Dostarczenie dowodu koncepcji 

Jednym z kluczowych scenariuszy dla Azure DevTest Labs jest włączenie środowisk deweloperskich i testowych w chmurze. Przykłady:

* Tworzenie pulpitów deweloperów w chmurze,
* Konfigurowanie środowisk do testowania,
* Włączanie dostępu do maszyn wirtualnych i innych zasobów platformy Azure,
* Konfigurowanie obszaru piaskownicy dla deweloperów do uczenia się i eksperymentowania.

DevTest Labs również udostępniają guardrails zasad i kontrolę kosztów, aby zapewnić przedsiębiorstwom możliwość dostarczania samoobsługowego systemu Azure do deweloperów, którzy stosują się do firmowych zasad związanych z bezpieczeństwem, regulacją i zgodnością. 

Każde przedsiębiorstwo ma inne wymagania dotyczące sposobu, w jaki Azure DevTest Labs można pomyślnie włączyć do ich środowiska. W tym artykule opisano najczęściej spotykane kroki, które zwykle muszą zostać wykonane, aby zapewnić pomyślne potwierdzenie koncepcji, czyli pierwszy krok w kierunku pomyślnego zakończenia wdrożenia. 

## <a name="getting-started"></a>Wprowadzenie 

Aby rozpocząć świadczenie, zapoznaj się z weryfikacją koncepcji. Ważne jest, aby poświęcać trochę czasu na zapoznanie się z platformą Azure i DevTest Labs.  Oto kilka zasobów początkowych: 

* [Informacje o Azure Portal](https://azure.microsoft.com/features/azure-portal/)
* [Podstawy DevTest Labs](devtest-lab-overview.md)
* [Scenariusze obsługiwane przez DevTest Labs](devtest-lab-guidance-get-started.md)
* [Dokumentacja DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Wprowadzenie do sieci platformy Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne 

Aby pomyślnie ukończyć pilotaż lub weryfikację koncepcji z DevTest Labs, wymagane są pewne wymagania wstępne: 

* **Subskrypcja platformy Azure**: Przedsiębiorstwa często mają istniejące [Umowa Enterprise](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) , które umożliwiają dostęp do platformy Azure, a istniejącej lub nowej subskrypcji można używać na potrzeby DevTest Labs. Alternatywnie, [Visual Studio Subscription](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) może być używany podczas pilotażu (wykorzystując bezpłatne środki na korzystanie z platformy Azure). Jeśli żadna z tych opcji nie jest dostępna, można utworzyć i użyć [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) . Jeśli istnieje Umowa Enterprise, przy użyciu subskrypcji tworzenia i [testowania](https://azure.microsoft.com/offers/ms-azr-0148p/) w przedsiębiorstwie jest świetną opcją uzyskania dostępu do systemów operacyjnych klienta Windows 10/Windows 8.1 i obniżonych stawek za tworzenie i testowanie obciążeń. 
* **Dzierżawa Azure Active Directory**:  Aby umożliwić zarządzanie użytkownikami (na przykład dodawania użytkowników lub dodawania właścicieli laboratorium), Ci użytkownicy muszą być częścią [dzierżawy Azure Active Directory](https://azure.microsoft.com/services/active-directory/) używanej w ramach subskrypcji platformy Azure dla pilotażu. Często przedsiębiorstwa skonfigurują [tożsamość hybrydową](../active-directory/hybrid/whatis-hybrid-identity.md) , aby umożliwić użytkownikom korzystanie z lokalnej tożsamości w chmurze, ale nie jest to wymagane w przypadku pilotażu DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Określanie zakresu pilotażu 

Bardzo ważne jest, aby zaplanować właściwy program pilotażowy przed rozpoczęciem wdrażania. Poznanie z wyprzedzeniem, że zasoby nie pozostaną przez czas nieokreślony, ustawia odpowiednie oczekiwania dla użytkowników pilotażu. 

> [!IMPORTANT]
> Nie możemy wyróżnić wystarczająco dużo znaczenia dla pilotażu i ustawienia oczekiwane.

Przed rozpoczęciem pilotażu należy odpowiedzieć na kluczowe pytania: 

* Co chcesz dowiedzieć się, jak wygląda sukces dla pilotażu? 
* Jakie obciążenia lub scenariusze zostaną omówione w programie pilotażowym? Ważne jest, aby zdefiniować tylko niewielki zestaw, aby upewnić się, że pilotaż może być w zasięgu i szybko zakończony. 
* Jakie zasoby muszą być dostępne w środowisku laboratoryjnym? Na przykład: obrazy niestandardowe, obrazy z witryny Marketplace, zasady, topologia sieci itp. 
* Kto jest użytkownikami końcowymi/zespołami, którzy będą uczestniczyć w pilotażu do sprawdzenia środowiska?  
* Jaki jest czas trwania pilotażu? Wybierz przedział czasu, który jest wyrównany do planowanego zakresu, takiego jak dwa tygodnie lub jeden miesiąc. 
* Co się stanie z przydzielonymi zasobami, które były używane podczas pilotażu, gdy pilotaż zostanie ukończony? Czy planujesz odrzucanie zasobów pilotażowych?
   
   Jeśli możemy zaplanować Przerzucanie maszyn wirtualnych i laboratoriów na końcu pilotażu, można po prostu skonfigurować jedną subskrypcję dla pilotażu i wykonać wszystkie nasze prace w tym czasie. 

Istnieje ogólna tendencja, która zapewnia pilotaż "idealny", tak samo jak w przypadku, gdy usługa zostanie wycofana w firmie. Jest to fałszywe założenie. Bliżej "idealny" jeszcze więcej czynności, które należy wykonać *przed* rozpoczęciem pracy na pilotażu. Celem pilotażu jest podjęcie odpowiednich decyzji dotyczących skalowania w górę i wdrożenia ostatniej usługi. 

Zespół pilotażowy powinien mieć możliwość wyboru bezwzględnych minimalnych wymaganych obciążeń i zależności, aby odpowiedzieć na pytanie, czy Azure DevTest Labs to odpowiednia usługa dla danego przedsiębiorstwa. Zaleca się wybór najprostszych obciążeń z najniższymi zależnościami, aby zapewnić szybsze i czyste sukcesy. Jeśli nie jest to możliwe, następna najlepsza opcja polega na wybraniu najbardziej reprezentatywnego obciążenia, które ujawnia potencjalną złożoność, aby sukces w fazie pilotażowej mógł zostać zreplikowany w fazie skalowania w poziomie. 

Poniższy przykład ilustruje dobrze określony zakres weryfikacji koncepcji. 

## <a name="example-proof-of-concept-plan"></a>Przykład: Weryfikacja planu koncepcji 

W tej sekcji przedstawiono przykład służący do określania zakresu weryfikacji koncepcji pilotażowej dla DevTest Labs. 

> [!TIP]
> Aby zminimalizować możliwość skonfigurowania projektu pod kątem awarii, zdecydowanie zalecamy, aby nie pominąć przykładu opisanego w tej sekcji. 

### <a name="overview"></a>Przegląd 

Planujemy Tworzenie nowego środowiska na platformie Azure w oparciu o DevTest Labs for Vendors, aby używać go jako środowiska izolowanego z sieci firmowej. Aby określić, czy rozwiązanie będzie spełniało wymagania, zostanie opracowana Weryfikacja koncepcji w celu zweryfikowania kompleksowego rozwiązania i dołączenia kilku dostawców do wypróbowania i sprawdzenia środowiska. 

### <a name="outcomes"></a>Wyników 

Podczas tworzenia dowodu koncepcji należy skoncentrować się najpierw na rezultatach (co próbujemy osiągnąć) — są one wymienione tutaj.  Na koniec weryfikacji koncepcji Oczekujemy: 

* Kompleksowe rozwiązanie dla dostawców korzystających z kont Gości w usłudze Azure Active Directory (Azure AD) w celu uzyskania dostępu do środowiska izolowanego na platformie Azure z zasobami wymaganymi do produktywności. 
* Wszelkie potencjalne problemy z blokowaniem wpływające na szersze użycie i wdrożenie są wyliczane i zrozumiałe.
* Osoby mające na celu rozwój koncepcji sprawdzają poprawność wszystkich kodów. Są one również zrozumiałe i są świadome szerszego zastosowania.

### <a name="open-questions--prerequisites"></a>Otwarte pytania & wymagania wstępne 

* Czy mamy utworzoną subskrypcję, której możemy użyć dla tego projektu? 
* Czy mamy dzierżawę usługi Azure AD i zidentyfikowano administratora globalnego usługi Azure AD, który może zapewnić pomoc & Wskazówki dotyczące pytań związanych z usługą Azure AD? 
* Miejsce do współpracy dla osób pracujących nad projektem: 

   * Kod źródłowy i skrypty (takie jak Azure Repos) 
   * Dokumenty (na przykład zespoły lub SharePoint)  
   * Konwersacje (takie jak Microsoft Teams) 
   * Elementy robocze (takie jak Azure Boards) 
* Jakie zasoby są wymagane dla dostawców? Dotyczy to zarówno lokalnie na maszynach wirtualnych, jak i innych wymaganych serwerów, aplikacji dostępnych w sieci. 
* Czy maszyny wirtualne będą przyłączone do domeny na platformie Azure? Jeśli tak, czy będzie to Azure AD Domain Services czy coś innego? 
* Czy mamy zidentyfikowane zespoły/dostawcy, którzy będą celem weryfikacji koncepcji? Kto będzie klientami środowiska?
* Którego regionu świadczenia usługi Azure będziemy używać do weryfikacji koncepcji? 
* Czy mamy listę usług, których mogą używać dostawcy za pośrednictwem DevTest Labs poza IaaS (maszyny wirtualne)? 
* Jak planujemy szkolenia dostawców/użytkowników przy użyciu laboratorium? 

### <a name="proof-of-concept-solution-components"></a>Weryfikacja składników rozwiązań koncepcji 

Oczekujemy, że rozwiązanie będzie miało następujące składniki: 

* Zestaw DevTest Labs na platformie Azure dla różnych zespołów dostawców.
* Laboratoria są połączeni z infrastrukturą sieciową, która spełnia wymagania.
* Dostawcy mają dostęp do laboratoriów za pośrednictwem usługi Azure AD i udzielania przypisań ról do laboratorium.
* Sposób, w jaki dostawcy mogą pomyślnie połączyć się ze swoimi zasobami. Sieć VPN typu lokacja-lokacja umożliwia dostęp do maszyn wirtualnych bezpośrednio bez publicznych adresów IP.
* Zestaw artefaktów, które obejmują wymagane oprogramowanie wymagane przez dostawców na maszynach wirtualnych.

## <a name="additional-planning-and-design-decisions"></a>Dodatkowe decyzje dotyczące planowania i projektowania 

Przed wydaniem pełnego rozwiązania DevTest Labs należy podjąć pewne ważne decyzje dotyczące planowania i projektowania. Środowisko pracy z weryfikacją koncepcji może ułatwić podejmowanie tych decyzji. Dalsze zagadnienia obejmują: 

* **Topologia subskrypcji**: Wymagania dotyczące zasobów platformy Azure na poziomie przedsiębiorstwa mogą wykraczać poza [dostępne przydziały w ramach jednej subskrypcji](https://docs.microsoft.com/azure/azure-subscription-service-limits), co wymaga wielu subskrypcji platformy Azure i/lub żądań obsługi zwiększających początkowe limity subskrypcji. Ważne jest, aby podjąć decyzję o sposobie dystrybucji zasobów między subskrypcjami, a jednym cennym zasobem jest [Przewodnik po decyzji o subskrypcji](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) , ponieważ trudno jest później przenieść zasoby do innej subskrypcji. Na przykład nie można przenieść laboratorium DevTest do innej subskrypcji po jej utworzeniu.  
* **Topologia sieci**: [Domyślna infrastruktura sieci](../app-service/networking-features.md) tworzona automatycznie przez DevTest Labs może być niewystarczająca do spełnienia wymagań i ograniczeń dla użytkowników w przedsiębiorstwie. Często można zobaczyć [ExpressRoute połączone sieci wirtualnych](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/), [Hub i szprych](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) na potrzeby łączności między subskrypcjami, a nawet wymuszone [Routing](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) zapewniający tylko łączność lokalną. DevTest Labs umożliwia podłączenie istniejących sieci wirtualnych do laboratorium, aby umożliwić ich użycie podczas tworzenia nowych maszyn wirtualnych w laboratorium. 
* **Zdalny dostęp do Virtual Machines**: Istnieje wiele opcji zdalnego dostępu do maszyn wirtualnych znajdujących się w DevTest Labs. Najłatwiejszym rozwiązaniem jest użycie publicznych adresów IP lub udostępnionych publicznych adresów IP. są to [Ustawienia dostępne w środowisku laboratoryjnym](devtest-lab-shared-ip.md). Jeśli te opcje nie są wystarczające, korzystanie z bramy dostępu zdalnego jest również opcją, jak pokazano na [architekturze referencyjnej DevTest Labs Enterprise](devtest-lab-reference-architecture.md) i opisano ją dokładniej w [dokumentacji bramy usług pulpitu zdalnego DevTest Labs](configure-lab-remote-desktop-gateway.md). Przedsiębiorstwa mogą również używać trasy Express lub sieci VPN typu lokacja-lokacja do łączenia ich laboratoriów DevTest z siecią lokalną. Umożliwia to bezpośrednie połączenia pulpitu zdalnego lub SSH z Virtual Machines na podstawie ich prywatnego adresu IP bez narażania się na Internet. 
* **Obsługa uprawnień**: Dwa kluczowe uprawnienia często używane w DevTest Labs to ["właściciel" i "Użytkownik laboratorium"](devtest-lab-add-devtest-user.md). Ważne jest, aby podjąć decyzję przed przeprowadzeniem DevTestych laboratoriów w szerokim stopniu, którzy będą powierzać każdy poziom dostępu w laboratorium. Typowym modelem jest właściciel budżetu (na przykład lider zespołu) jako właściciel laboratorium i członkowie zespołu jako użytkownicy laboratorium. Dzięki temu osoba (lider zespołu) odpowiada za budżet na dostosowanie ustawień zasad w celu zachowania zespołu w ramach budżetu.  

## <a name="completing-the-proof-of-concept"></a>Ukończenie weryfikacji koncepcji 

Gdy przewidywane informacje zostały omówione, czas na zawinięcie i zakończenie pilotażu. Jest to czas, w którym można zbierać opinie użytkowników, ustalić, czy pilotaż zakończył się powodzeniem, i zdecydować, czy organizacja przejdzie w przód do DevTest laboratoriów w przedsiębiorstwie. Jest to świetny moment, aby rozważyć automatyzację wdrażania programu DevTest Labs i skojarzonych zasobów w celu zapewnienia spójności w całym skalowaniu. 

## <a name="next-steps"></a>Następne kroki 

* [Dokumentacja DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Architektura referencyjna dla przedsiębiorstwa](devtest-lab-reference-architecture.md)
* [Skalowanie w górę wdrożenia DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Organizuj implementację Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
