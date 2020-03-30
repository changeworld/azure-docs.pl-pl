---
title: Dostarczanie weryfikacji koncepcji — Laboratorium DevTest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak dostarczyć dowód koncepcji, dzięki czemu usługa Azure DevTest Labs może zostać pomyślnie włączona do środowiska korporacyjnego.
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
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75643291"
---
# <a name="deliver-a-proof-of-concept"></a>Dostarczenie weryfikacji koncepcji 

Jednym z kluczowych scenariuszy dla platformy Azure DevTest Labs jest włączenie środowiska programistyczne i testowe w chmurze. Przykłady:

* Tworzenie pulpitów dla deweloperów w chmurze.
* Konfigurowanie środowisk do testowania.
* Włączanie dostępu do maszyn wirtualnych i innych zasobów platformy Azure.
* Konfigurowanie obszaru piaskownicy dla deweloperów do nauki i eksperymentowania.

DevTest Labs udostępnia również barierki zasad i kontrolę kosztów, aby umożliwić przedsiębiorstwu zapewnienie "samoobsługowej platformy Azure" deweloperom przestrzegam zasad zabezpieczeń firmowych, regulacji i zgodności. 

Każde przedsiębiorstwo ma różne wymagania dotyczące sposobu, w jaki można pomyślnie włączyć laboratorium usługi Azure DevTest Labs do środowiska. W tym artykule opisano najczęstsze kroki, które przedsiębiorstwa muszą wykonać, aby zapewnić pomyślny dowód koncepcji. Dowód koncepcji jest pierwszym krokiem w kierunku pomyślnego wdrożenia end-to-end. 

## <a name="getting-started"></a>Wprowadzenie 

Aby rozpocząć dostarczanie dowodu koncepcji. Ważne jest, aby poświęcić trochę czasu, aby dowiedzieć się więcej o platformie Azure i laboratoriach DevTest.  Oto kilka zasobów początkowych: 

* [Opis portalu platformy Azure](https://azure.microsoft.com/features/azure-portal/)
* [Podstawy DevTest Labs](devtest-lab-overview.md)
* [Obsługiwane scenariusze laboratoriów DevTest Labs](devtest-lab-guidance-get-started.md)
* [Dokumentacja przedsiębiorstwa DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Wprowadzenie do sieci platformy Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne 

Aby pomyślnie ukończyć pilotaż lub dowód koncepcji za pomocą DevTest Labs, istnieje kilka wymagań wstępnych: 

* **Subskrypcja platformy Azure:** Przedsiębiorstwa często mają istniejącą umowę [Enterprise Agreement,](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) która umożliwia dostęp do platformy Azure i mogą korzystać z istniejącej lub nowej subskrypcji dla laboratoriów DevTest. Alternatywnie przedsiębiorstwa mogą korzystać z [subskrypcji programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) podczas pilotażu (korzystając z bezpłatnych kredytów platformy Azure). Jeśli żadna z tych opcji nie jest dostępna, przedsiębiorstwo może utworzyć bezpłatne [konto platformy Azure](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)i korzystać z niego. Jeśli istnieje umowa Enterprise Agreement, korzystanie z [subskrypcji deweloperów/testów dla przedsiębiorstw](https://azure.microsoft.com/offers/ms-azr-0148p/) jest doskonałym rozwiązaniem, aby uzyskać dostęp do klienckich systemów operacyjnych systemu Windows 10/Windows 8.1 i obniżonych stawek dla obciążeń deweloperskich i testowych. 
* **Dzierżawa usługi Azure Active Directory:** Aby włączyć zarządzanie użytkownikami (na przykład dodawanie użytkowników lub dodawanie właścicieli laboratoriów), ci użytkownicy muszą być częścią [dzierżawy usługi Azure Active Directory](https://azure.microsoft.com/services/active-directory/) używanej w subskrypcji platformy Azure dla pilotażu. Często przedsiębiorstwa skonfigurują [tożsamość hybrydową,](../active-directory/hybrid/whatis-hybrid-identity.md) aby umożliwić użytkownikom korzystanie z ich tożsamości lokalnej w chmurze, ale nie jest to wymagane w przypadku programu pilotażowego DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Zakres pilota 

Przed rozpoczęciem wdrażania należy zaplanować pilotaż. Wiedząc z wyprzedzeniem, że zasoby nie pozostaną na czas nieokreślony, wyznacza odpowiednie oczekiwania dla użytkowników pilotażu. 

> [!IMPORTANT]
> Nie możemy wystarczająco podkreślić znaczenia ostrego określania zakresu pilota i stawiania oczekiwań z góry.

Odpowiedz na te kluczowe pytania przed rozpoczęciem pilota: 

* Czego chcesz się nauczyć i jak wygląda sukces pilota? 
* Jakie obciążenia lub scenariusze zostaną uwzględnione w ramach programu pilotażowego? Ważne jest, aby zdefiniować tylko mały zestaw, aby zapewnić, że pilot może być szybko objęty zakresem i ukończony. 
* Jakie zasoby muszą być dostępne w laboratorium? Na przykład: obrazy niestandardowe, obrazy z rynku, zasady, topologia sieci. 
* Kim są użytkownicy i zespoły, które będą zaangażowane w pilotaż, aby zweryfikować doświadczenie?  
* Jaki jest czas trwania pilota? Wybierz przedział czasu, który dobrze dostosowuje się do planowanego zakresu, na przykład dwa tygodnie lub jeden miesiąc. 
* Co się stanie z przydzielonymi zasobami, które zostały wykorzystane podczas pilotażu? Czy planujesz odrzucić zasoby pilotażowe? Można by pomyśleć:
   
   "Jeśli możemy zaplanować wyrzucenie maszyn wirtualnych i laboratoriów na koniec pilotażu, możemy skonfigurować jedną subskrypcję dla pilota i wykonać całą naszą pracę tam, podczas gdy równolegle rozwiązujemy pytania dotyczące wdrażania skali." 

Istnieje ogólna tendencja do tego, aby pilot był "doskonały", więc identycznie odzwierciedla to, jaki będzie ostateczny stan po wytoczyniu usługi w firmie. Jest to fałszywe założenie. Im bliżej "doskonały", tym więcej trzeba wykonać *przed* rozpoczęciem pracy na pilotażu. Celem pilotażu jest podejmowanie właściwych decyzji dotyczących zwiększania skali i wprowadzania ostatecznej usługi. 

Program pilotażowy powinien koncentrować się na wybieraniu minimalnych niezbędnych obciążeń i zależności, aby odpowiedzieć na pytanie, czy usługa Azure DevTest Labs jest właściwą usługą dla danego przedsiębiorstwa. Zaleca się wybranie najprostszego obciążenia z najmniejszymi zależnościami, aby zapewnić szybki i czysty sukces. Jeśli nie jest to możliwe, wybierz najbardziej reprezentatywne obciążenie, które ujawnia potencjalne zawiłości, dzięki czemu sukces w fazie pilotażowej może być replikowany w fazie skalowania w poziomie. 

W poniższym przykładzie przedstawiono dobrze zakres dowód koncepcji. 

## <a name="example-proof-of-concept-plan"></a>Przykład: plan weryfikacji koncepcji 

W tej sekcji przedstawiono przykład do użycia do określania zakresu dowodu koncepcji programu pilotażowego dla DevTest Labs. 

> [!TIP]
> Aby zminimalizować możliwość konfigurowania projektu dla awarii, zdecydowanie zaleca się, aby nie pominąć przykład opisany w tej sekcji. 

### <a name="overview"></a>Omówienie 

Planujemy opracować nowe środowisko na platformie Azure oparte na Laboratoriach DevTest dla dostawców do użycia jako izolowane środowisko z sieci firmowej. Aby ustalić, czy rozwiązanie będzie spełniać wymagania, opracujemy dowód koncepcji, aby zweryfikować kompleksowe rozwiązanie. Zamieściliśmy kilku dostawców, aby wypróbować i zweryfikować środowisko. 

### <a name="outcomes"></a>Wyniki 

Budując dowód koncepcji, skupiamy się przede wszystkim na wynikach (co staramy się osiągnąć). Do końca weryfikacji koncepcji oczekujemy: 

* Działające kompleksowe rozwiązanie dla dostawców do korzystania z kont gości w usłudze Azure Active Directory (Azure AD) w celu uzyskania dostępu do izolowanego środowiska na platformie Azure. Środowisko ma zasoby wymagane do ich produktywnej. 
* Wszelkie potencjalne problemy z blokowaniem, które wpływają na użycie i przyjęcie na szerszą skalę, są wyliczane i rozumiane.
* Osoby zaangażowane w rozwój dowodu koncepcji mają dobre zrozumienie całego kodu. Rozumieją również związane z tym zabezpieczenia i są pewni szerszego przyjęcia.

### <a name="open-questions-and-prerequisites"></a>Pytania otwarte i wymagania wstępne 

* Czy mamy subskrypcję utworzoną, której możemy użyć dla tego projektu? 
* Czy mamy dzierżawę usługi Azure AD i globalnego administratora usługi Azure AD, który zidentyfikował, kto może udzielić pomocy i wskazówek dotyczących pytań związanych z usługą Azure AD? 
* Czy mamy miejsce do współpracy dla osób pracujących nad projektem? 

   * Kod źródłowy i skrypty (takie jak repozytoria platformy Azure) 
   * Dokumenty (takie jak Microsoft Teams lub SharePoint)  
   * Konwersacje (takie jak Microsoft Teams) 
   * Elementy robocze (takie jak tablice platformy Azure) 
* Jakie są wymagane zasoby dla dostawców? Obejmuje to aplikacje dostępne w sieci, zarówno lokalnie na maszynach wirtualnych, jak i na innych wymaganych serwerach. 
* Czy maszyny wirtualne zostaną przyłączone do domeny na platformie Azure? Jeśli tak, czy będzie to usługi domenowe Active Directory platformy Azure (Usługi Usługi Azure AD DS) lub coś innego? 
* Czy zidentyfikowaliśmy zespół lub dostawców, którzy będą celem weryfikacji koncepcji? Kto będzie klientem dla środowiska?
* Który region platformy Azure będzie używany do weryfikacji koncepcji? 
* Czy mamy listę usług, z których dostawcy mogą korzystać za pośrednictwem DevTest Labs oprócz IaaS (VM)? 
* Jak planujemy szkolić dostawców / użytkowników w korzystaniu z laboratorium? 

### <a name="components-of-the-proof-of-concept-solution"></a>Elementy rozwiązania proof-of-concept 

Oczekujemy, że rozwiązanie będzie miało następujące składniki: 

* Różne zespoły dostawców będą używać zestawu laboratoriów na platformie Azure.
* Laboratoria są podłączone do infrastruktury sieciowej, która obsługuje wymagania.
* Dostawcy mają dostęp do laboratoriów za pośrednictwem usługi Azure AD i przypisań ról.
* Dostawcy mają sposób, aby pomyślnie połączyć się ze swoimi zasobami. W szczególności sieć VPN typu lokacja lokacja umożliwia dostęp do maszyn wirtualnych bezpośrednio bez publicznych adresów IP.
* Zestaw artefaktów obejmuje wymagane oprogramowanie, które dostawcy potrzebują na maszynach wirtualnych.

## <a name="additional-planning-and-design-decisions"></a>Dodatkowe decyzje dotyczące planowania i projektowania 

Przed wydaniem pełnego rozwiązania DevTest Labs należy podjąć kilka ważnych decyzji dotyczących planowania i projektowania. Doświadczenie w pracy nad dowodem koncepcji może pomóc w podejmowaniu tych decyzji. Dalsze rozważania obejmują: 

* **Topologia subskrypcji:** Wymagania dotyczące zasobów na poziomie przedsiębiorstwa na platformie Azure mogą wykraczać poza [dostępne przydziały w ramach jednej subskrypcji.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) Wymaga to wielu subskrypcji platformy Azure i/lub żądań usługi w celu zwiększenia początkowych limitów subskrypcji. Ważne jest, aby zdecydować z góry, jak rozdzielać zasoby między subskrypcje. Jednym z cennych zasobów jest [przewodnik decyzji subskrypcji,](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) ponieważ trudno jest przenieść zasoby do innej subskrypcji później. Na przykład laboratorium nie można przenieść do innej subskrypcji po jego utworzeniu.  
* **Topologia sieci:** [Domyślna infrastruktura sieciowa,](../app-service/networking-features.md) którą DevTest Labs automatycznie tworzy, może nie być wystarczająca do spełnienia wymagań i ograniczeń dla użytkowników korporacyjnych. Często można zobaczyć [połączone sieci wirtualne usługi Azure ExpressRoute,](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) [koncentrator i szprychy](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) dotyczące łączności między subskrypcjami, a nawet [wymuszone routingu,](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) aby zapewnić tylko łączność lokalną. DevTest Labs umożliwia istniejące sieci wirtualne mają być połączone z laboratorium, aby umożliwić ich użycie podczas tworzenia nowych maszyn wirtualnych w laboratorium. 
* **Dostęp zdalny maszyn wirtualnych:** Istnieje wiele opcji zdalnego dostępu do maszyn wirtualnych znajdujących się w laboratorium DevTest. Najłatwiej jest użyć publicznych adresów IP lub udostępnionych publicznych adresów IP. Są to [ustawienia dostępne w laboratorium](devtest-lab-shared-ip.md). Jeśli te opcje nie są wystarczające, użycie bramy dostępu zdalnego jest również opcją. Ta opcja jest wyświetlana na [architekturze referencyjnej przedsiębiorstwa DevTest Labs](devtest-lab-reference-architecture.md) i opisana w [dalszej dokumentacji bramy pulpitu zdalnego DevTest Labs.](configure-lab-remote-desktop-gateway.md) Przedsiębiorstwa mogą również używać usługi ExpressRoute lub sieci VPN typu lokacja lokacja do łączenia swoich laboratoriów z siecią lokalną. Ta opcja umożliwia bezpośrednie zdalne pulpit lub połączenia SSH z maszynami wirtualnymi na podstawie ich prywatnego adresu IP bez ekspozycji na Internet. 
* **Uprawnienia obsługi:** Dwa uprawnienia klucza powszechnie używane w DevTest Labs są [właściciel i użytkownik laboratorium](devtest-lab-add-devtest-user.md). Ważne jest, aby zdecydować przed wprowadzeniem DevTest Labs szeroko, kto zostanie powierzony z każdym poziomie dostępu w laboratorium. Typowym modelem jest właściciel budżetu (na przykład kierownik zespołu) jako właściciel laboratorium i członkowie zespołu jako użytkownicy laboratorium. Ten model umożliwia osobie (kierownikowi zespołu) odpowiedzialnej za budżet dostosowanie ustawień zasad i utrzymanie zespołu w budżecie.  

## <a name="completing-the-proof-of-concept"></a>Uzupełnienie weryfikacji koncepcji 

Po omówieniu oczekiwanych nauk nadszedł czas, aby ukończyć pilotaż. Jest to czas, aby zebrać opinie od użytkowników, określić, czy pilot zakończył się pomyślnie, i zdecydować, czy organizacja będzie iść do przodu na skali wdrożenia DevTest Labs w przedsiębiorstwie. Jest to również świetny czas, aby rozważyć automatyzację wdrażania DevTest Labs i skojarzonych zasobów, aby zapewnić spójność w całym wdrażaniu skali. 

## <a name="next-steps"></a>Następne kroki 

* [Dokumentacja przedsiębiorstwa DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Architektura referencyjna dla przedsiębiorstwa](devtest-lab-reference-architecture.md)
* [Skalowanie wdrożenia devtest labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Organizowanie implementacji laboratoriów DevTest azure](devtest-lab-guidance-orchestrate-implementation.md)
