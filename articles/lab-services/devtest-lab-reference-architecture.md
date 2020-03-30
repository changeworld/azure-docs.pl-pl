---
title: Architektura referencyjna przedsiębiorstwa dla laboratoriów DevTest Azure
description: Ten artykuł zawiera wskazówki dotyczące architektury referencyjnej dla laboratoriów Azure DevTest Labs w przedsiębiorstwie.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132854"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Architektura referencyjna laboratoriów devtest labs platformy Azure dla przedsiębiorstw
Ten artykuł zawiera architekturę odwołań ułatwiającego wdrażanie rozwiązania opartego na platformie Azure DevTest Labs w przedsiębiorstwie. Obejmuje ona następujące elementy:
- Łączność lokalna za pośrednictwem usługi Azure ExpressRoute
- Brama pulpitu zdalnego do zdalnego logowania się do maszyn wirtualnych
- Łączność z repozytorium artefaktów dla prywatnych artefaktów
- Inne usługi PaaS, które są używane w laboratoriach

![Diagram architektury referencyjnej](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektura
Są to kluczowe elementy architektury referencyjnej:

- **Usługa Azure Active Directory (Azure AD)**: DevTest Labs używa [usługi Azure AD do zarządzania tożsamościami.](../active-directory/fundamentals/active-directory-whatis.md) Należy wziąć pod uwagę te dwa kluczowe aspekty, gdy dać użytkownikom dostęp do środowiska opartego na DevTest Labs:
    - **Zarządzanie zasobami:** Zapewnia dostęp do witryny Azure Portal do zarządzania zasobami (tworzenie maszyn wirtualnych; tworzenie środowisk; uruchamianie, zatrzymywać, restartować, usuwać i stosować artefakty i tak dalej). Zarządzanie zasobami odbywa się na platformie Azure przy użyciu kontroli dostępu opartej na rolach (RBAC). Przypisywanie ról do użytkowników i ustawianie uprawnień na poziomie zasobów i dostępu.
    - **Maszyny wirtualne (na poziomie sieci)**: W konfiguracji domyślnej maszyny wirtualne używają lokalnego konta administratora. Jeśli dostępna jest domena[(usługi domenowe usługi Azure AD,](../active-directory-domain-services/overview.md)domena lokalna lub domena w chmurze), maszyny mogą być dołączane do domeny. Użytkownicy mogą następnie używać swoich tożsamości opartych na domenie, aby połączyć się z maszynami wirtualnymi.
- **Łączność lokalna:** W naszym diagramie architektury jest używany [program ExpressRoute.](../expressroute/expressroute-introduction.md) Ale możesz również użyć [sieci VPN typu lokacja lokacja.](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) Mimo że usługa ExpressRoute nie jest wymagana dla devtest labs, jest powszechnie używana w przedsiębiorstwach. Usługa ExpressRoute jest wymagana tylko wtedy, gdy potrzebujesz dostępu do zasobów firmowych. Typowe scenariusze to:
    - Masz dane lokalne, których nie można przenieść do chmury.
    - Wolisz dołączyć do maszyn wirtualnych laboratorium do domeny lokalnej.
    - Chcesz wymusić cały ruch sieciowy do i z środowiska chmury za pośrednictwem zapory lokalnej w celu zapewnienia zabezpieczeń/zgodności.
- **Sieciowe grupy zabezpieczeń:** Typowym sposobem ograniczenia ruchu do środowiska w chmurze (lub w środowisku chmury) na podstawie źródłowych i docelowych adresów IP jest użycie [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md). Na przykład chcesz zezwolić tylko na ruch pochodzący z sieci firmowej do sieci laboratorium.
- **Brama pulpitu zdalnego:** Przedsiębiorstwa zazwyczaj blokują wychodzące połączenia pulpitu zdalnego w zaporze firmowej. Istnieje kilka opcji umożliwiających łączność ze środowiskiem chmurowym w DevTest Labs, w tym:
  - Użyj [bramy pulpitu zdalnego](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)i zezwól na statyczny adres IP modułu równoważenia obciążenia bramy.
  - [Kieruj cały przychodzący ruch RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) za pośrednictwem połączenia sieci VPN usługi ExpressRoute/site-to-site. Ta funkcja jest częstym zagadnieniem, gdy przedsiębiorstwa planują wdrożenie DevTest Labs.
- **Usługi sieciowe (sieci wirtualne, podsieci)**: Topologia [sieci platformy Azure](../networking/networking-overview.md) jest kolejnym kluczowym elementem architektury DevTest Labs. Określa, czy zasoby z laboratorium mogą komunikować się i mieć dostęp do lokalnego i internetowego. Nasz diagram architektury zawiera najbardziej typowe sposoby, w jakie klienci korzystają z laboratoriów DevTest: wszystkie laboratoria łączą się za pośrednictwem [komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md) przy użyciu [modelu z szprychą koncentratora](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) z połączeniem sieci VPN z witryną w lokalizacji. Ale DevTest Labs używa usługi Azure Virtual Network bezpośrednio, więc nie ma żadnych ograniczeń dotyczących sposobu konfigurowania infrastruktury sieciowej.
- **DevTest Labs:** DevTest Labs jest kluczową częścią ogólnej architektury. Aby dowiedzieć się więcej o usłudze, zobacz [Informacje o laboratoriach DevTest Labs](devtest-lab-overview.md).
- **Maszyny wirtualne i inne zasoby (SaaS, PaaS, IaaS)**: Maszyny wirtualne są kluczowym obciążeniem, które DevTest Labs obsługuje wraz z innymi zasobami platformy Azure. Laboratoria devtest ułatwia i szybkie zapewnianie przez przedsiębiorstwo dostępu do zasobów platformy Azure (w tym maszyn wirtualnych i innych zasobów platformy Azure). Dowiedz się więcej o dostępie do platformy Azure dla [deweloperów](devtest-lab-developer-lab.md) i [testerów.](devtest-lab-test-env.md)

## <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności
Chociaż DevTest Labs nie ma wbudowanych przydziałów lub limitów, inne zasoby platformy Azure, które są używane w typowej operacji laboratorium mają [przydziały na poziomie subskrypcji.](../azure-resource-manager/management/azure-subscription-service-limits.md) Tak więc w typowym wdrożeniu przedsiębiorstwa potrzebujesz wielu subskrypcji platformy Azure, aby objąć duże wdrożenie laboratoriów DevTest. Przydziały, do których przedsiębiorstwa najczęściej docierają, to:

- **Grupy zasobów:** W konfiguracji domyślnej DevTest Labs tworzy grupę zasobów dla każdej nowej maszyny wirtualnej lub użytkownik tworzy środowisko przy użyciu usługi. Subskrypcje mogą zawierać [maksymalnie 980 grup zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). Tak, to jest limit maszyn wirtualnych i środowisk w subskrypcji. Istnieją dwie inne konfiguracje, które należy wziąć pod uwagę:
    - **[Wszystkie maszyny wirtualne przechodzą do tej samej grupy zasobów:](resource-group-control.md)** Mimo że ta konfiguracja pomaga spełnić limit grupy zasobów, wpływa na limit typu zasobu na grupę zasobów.
    - **Przy użyciu udostępnionych publicznych usług IP:** Wszystkie maszyny wirtualne tego samego rozmiaru i regionu przejść do tej samej grupy zasobów. Ta konfiguracja jest "środkowym terenem" między przydziałami grupy zasobów a przydziałami typu zasobu na grupę zasobów, jeśli maszyny wirtualne mogą mieć publiczne adresy IP.
- **Zasoby na grupę zasobów na typ zasobu:** Domyślny limit zasobów dla [zasobów na grupę zasobów na typ zasobu wynosi 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Korzystając ze *wszystkich maszyn wirtualnych przejdź do tej samej* konfiguracji grupy zasobów, użytkownicy osiągnąć ten limit subskrypcji znacznie wcześniej, zwłaszcza jeśli maszyny wirtualne mają wiele dodatkowych dysków.
- **Konta magazynu:** laboratorium w DevTest Labs jest dostarczane z kontem magazynu. Przydział platformy Azure dla [liczby kont magazynu na region na subskrypcję wynosi 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Maksymalna liczba Laboratoriów DevTest w tym samym regionie jest również 250.
- **Przypisania ról:** Przypisanie roli to sposób, w jaki użytkownik lub główny dostęp do zasobu (właściciela, zasobu, poziomu uprawnień). Na platformie Azure istnieje [limit 2000 przypisań ról na subskrypcję.](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits) Domyślnie usługa DevTest Labs tworzy grupę zasobów dla każdej maszyny Wirtualnej. Właściciel ma uprawnienia *właściciela* dla devtest labs vm i uprawnienia *czytelnika* do grupy zasobów. W ten sposób każda nowa nowo utworzona maszyna wirtualna używa dwóch przypisań ról oprócz przypisań, które są używane, gdy użytkownik udziela użytkownikom uprawnień do laboratorium.
- **Odczyty/zapisy interfejsu API:** Istnieją różne sposoby automatyzacji platformy Azure i devtest labs, w tym interfejsy API REST, PowerShell, interfejs interfejsu wiersza polecenia platformy Azure i zestaw SDK platformy Azure. Dzięki automatyzacji możesz osiągnąć inny limit żądań interfejsu API: Każda subskrypcja umożliwia maksymalnie [12 000 żądań odczytu i 1200 żądań zapisu na godzinę.](../azure-resource-manager/management/request-limits-and-throttling.md) Należy pamiętać o tym limicie podczas automatyzacji DevTest Labs.

## <a name="manageability-considerations"></a>Zagadnienia dotyczące możliwości zarządzania
DevTest Labs ma świetny interfejs użytkownika administracyjnego do pracy z jednym laboratorium. Ale w przedsiębiorstwie prawdopodobnie masz wiele subskrypcji platformy Azure i wiele laboratoriów. Konsekwentne wprowadzanie zmian we wszystkich laboratoriach wymaga skryptów/automatyzacji. Oto kilka przykładów i najlepszych rozwiązań w zakresie zarządzania wdrożenia DevTest Labs:

- **Zmiany w ustawieniach laboratorium:** Typowym scenariuszem jest zaktualizowanie określonego ustawienia laboratorium we wszystkich laboratoriach we wdrożeniu. Na przykład nowy rozmiar wystąpienia maszyny Wirtualnej jest dostępny i wszystkie laboratoria muszą zostać zaktualizowane, aby zezwolić na to. Najlepiej zautomatyzować te zmiany przy użyciu skryptów programu PowerShell, interfejsu wiersza polecenia lub interfejsów API REST.  
- **Osobisty token dostępu do repozytorium artefaktów:** Zazwyczaj osobiste tokeny dostępu do repozytorium Git wygasają za 90 dni, rok lub dwa lata. Aby zapewnić ciągłość, należy rozszerzyć token dostępu osobistego lub utworzyć nowy. Następnie użyj automatyzacji, aby zastosować nowy token dostępu osobistego do wszystkich laboratoriów.
- **Ogranicz zmiany do ustawienia laboratorium:** często określone ustawienie musi być ograniczone (na przykład zezwalające na korzystanie z obrazów z portalu Marketplace). Zasady platformy Azure można użyć, aby zapobiec zmianom typu zasobu. Można też utworzyć rolę niestandardową i udzielić użytkownikom tej roli zamiast roli *właściciela* w laboratorium. Można to zrobić dla większości ustawień w laboratorium (wsparcie wewnętrzne, anons laboratorium, dozwolone rozmiary maszyn wirtualnych i tak dalej).
- **Wymagaj maszyn wirtualnych do przestrzegania konwencji nazewnictwa:** Menedżerowie często chcą łatwo identyfikować maszyny wirtualne, które są częścią środowiska deweloperskiego i testowego opartego na chmurze. Można to zrobić za pomocą [usługi Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Należy pamiętać, że DevTest Labs używa podstawowych zasobów platformy Azure, które są zarządzane w ten sam sposób: sieci, dysków, obliczeń i tak dalej. Na przykład usługa Azure Policy ma zastosowanie do maszyn wirtualnych, które są tworzone w laboratorium. Usługa Azure Security Center może raportować zgodność z maszynami wirtualne. Usługa Azure Backup może zapewnić regularne kopie zapasowe maszyn wirtualnych w laboratorium.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa
Usługa Azure DevTest Labs używa istniejących zasobów na platformie Azure (obliczeń, sieci itd.). Dzięki nim automatycznie korzysta z funkcji zabezpieczeń wbudowanych w platformę. Na przykład, aby wymagać, aby przychodzące połączenia pulpitu zdalnego pochodziły tylko z sieci firmowej, wystarczy dodać grupę zabezpieczeń sieci do sieci wirtualnej na bramie pulpitu zdalnego. Jedyną dodatkową uwagę bezpieczeństwa jest poziom uprawnień, które można przyznać członkom zespołu, którzy korzystają z laboratoriów na co dzień. Najczęstsze uprawnienia to [ *właściciel* i *użytkownik*](devtest-lab-add-devtest-user.md). Aby uzyskać więcej informacji na temat tych ról, zobacz [Dodawanie właścicieli i użytkowników w laboratoriach DevTest Platformy Azure.](devtest-lab-add-devtest-user.md)

## <a name="next-steps"></a>Następne kroki
Zobacz następny artykuł z tej serii: [Skalowanie w górę infrastruktury laboratoriów Azure DevTest](devtest-lab-guidance-scale.md).
