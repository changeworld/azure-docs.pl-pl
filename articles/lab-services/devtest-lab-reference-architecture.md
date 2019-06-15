---
title: Architektura referencyjna Enterprise dla usługi Azure DevTest Labs
description: Ten artykuł zawiera wskazówki dotyczące architektury odwołanie do usługi Azure DevTest Labs w przedsiębiorstwie.
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
ms.openlocfilehash: 1bfd1b5b4b7febd98499e338fcb62e339867aef4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244717"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Architektura odwołań usługi Azure DevTest Labs dla przedsiębiorstw
Ten artykuł zawiera architektury referencyjnej, aby wdrożyć rozwiązanie oparte na usłudze Azure DevTest Labs w przedsiębiorstwie. Obejmuje następujące czynności:
- Połączeń lokalnych za pomocą usługi Azure ExpressRoute
- Brama usług pulpitu zdalnego do zdalnego logowania się do maszyn wirtualnych
- Łączność z repozytorium artefaktów dla prywatnych artefaktów
- Inne usługi PaaS, które są używane w laboratorium

![Referencyjny diagram architektury](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektura
Poniżej przedstawiono kluczowe elementy architektura referencyjna:

- **Azure Active Directory (Azure AD)** : Korzysta z usługi DevTest Labs [usługi Azure AD do zarządzania tożsamościami](../active-directory/fundamentals/active-directory-whatis.md). Po zapewnieniu użytkownikom dostępu do środowiska, w oparciu o usłudze DevTest Labs, należy wziąć pod uwagę te dwa kluczowe aspekty:
    - **Zarządzanie zasobami**: Zapewnia dostęp do witryny Azure portal do zarządzania zasobami (Tworzenie maszyn wirtualnych; tworzenie środowisk; uruchamianie, zatrzymywanie, ponowne uruchomienie, usunąć i Zastosuj artefaktów; i tak dalej). Zarządzanie zasobami odbywa się na platformie Azure przy użyciu kontroli dostępu opartej na rolach (RBAC). Przypisywanie ról do użytkowników i ustawić uprawnienia poziomu dostępu i zasobów.
    - **Maszyny wirtualne (w poziomie sieci)** : W domyślnej konfiguracji maszyn wirtualnych użyć konta administratora lokalnego. Jeśli istnieje domena ([usług domenowych Azure AD](../active-directory-domain-services/overview.md), domeny środowiska lokalnego lub domeny oparte na chmurze), maszyn może być przyłączony do domeny. Następnie użytkownicy mogą używać ich tożsamości opartych na domenie, połączyć się z maszynami wirtualnymi.
- **Połączeniami lokalnymi**: W naszym diagram architektury [ExpressRoute](../expressroute/expressroute-introduction.md) jest używany. Ale można również użyć [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Mimo że usługa ExpressRoute nie jest wymagane dla usługi DevTest Labs, często są one wykorzystywane w przedsiębiorstwach. Usługa ExpressRoute jest wymagane tylko wtedy, gdy będziesz potrzebować dostępu do zasobów firmy. Typowe scenariusze są następujące:
    - Masz dane w środowisku lokalnym, którego nie można przenieść do chmury.
    - Wolisz umożliwiają przyłączanie maszyn wirtualnych laboratorium do domeny w środowisku lokalnym.
    - Chcesz wymusić cały ruch sieciowy i w środowisku chmury, za pośrednictwem zapory lokalnych pod kątem zabezpieczeń i zgodności.
- **Sieciowe grupy zabezpieczeń**: Typowym sposobem ograniczania ruchu do środowiska chmury (lub w środowisku chmury) na podstawie źródła i docelowych adresów IP jest użycie [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md). Na przykład chcesz zezwolić tylko na ruch pochodzący z sieci firmowej w sieciach laboratorium.
- **Brama usług pulpitu zdalnego**: Przedsiębiorstwom Blokuj zazwyczaj wychodzące połączenia pulpitu zdalnego na zaporze firmowej. Dostępnych jest kilka opcji, aby umożliwić łączność z środowisko oparte na chmurze w usłudze DevTest Labs, w tym:
  - Użyj [bramy usług pulpitu zdalnego](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)i dozwolonych statyczny adres IP bramy, moduł równoważenia obciążenia.
  - [Kierować cały ruch przychodzący protokołu RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) za pośrednictwem połączenia VPN ExpressRoute/lokacja lokacja. Ta funkcja jest wspólne brany pod uwagę w przypadku przedsiębiorstw Planowanie wdrożenia usługi DevTest Labs.
- **Usługi (sieci wirtualne, podsieci) sieci**: [Sieci platformy Azure](../networking/networking-overview.md) topologia jest innym kluczowym elementem w architekturze usługi DevTest Labs. Kontroluje, czy zasoby z laboratorium można komunikować się i mieć dostęp do Internetu i w środowisku lokalnym. Nasze diagram architektury obejmuje najbardziej typowych sposobów, aby klienci używali usługi DevTest Labs: Laboratoria wszystkie połączenia za pomocą [wirtualne sieci równorzędne](../virtual-network/virtual-network-peering-overview.md) przy użyciu [modelu piasty i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) połączenia usługi ExpressRoute/lokacja lokacja sieci VPN do sieci lokalnej. Jednak usługa DevTest Labs korzysta z usługi Azure Virtual Network bezpośrednio, więc nie ma żadnych ograniczeń dotyczących sposobu skonfigurowania infrastruktury sieciowej.
- **DevTest Labs**:  DevTest Labs to kluczowa część ogólna architektura. Aby dowiedzieć się więcej o usłudze, zobacz [DevTest Labs](devtest-lab-overview.md).
- **Maszyny wirtualne i inne zasoby (SaaS, PaaS, IaaS)** :  Maszyny wirtualne są kluczowe obciążenia, które obsługuje DevTest Labs, wraz z innymi zasobami platformy Azure. DevTest Labs umożliwia łatwe i szybkie dla przedsiębiorstw zapewnić dostęp do zasobów platformy Azure (w tym maszyn wirtualnych i innych zasobów platformy Azure). Dowiedz się więcej o dostęp do platformy Azure dla [deweloperów](devtest-lab-developer-lab.md) i [testerów](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności
Mimo że usługa DevTest Labs nie ma wbudowanych przydziały i limity, innych zasobów platformy Azure, które są używane w typowych operacji laboratorium ma [przydziały na poziomie subskrypcji](../azure-subscription-service-limits.md). Dlatego w przypadku typowego przedsiębiorstwa wdrożenia należy wiele subskrypcji platformy Azure do pokrycia dużych wdrożeniach usługi DevTest Labs. Limity przydziału, w których przedsiębiorstwa najczęściej są:

- **Grupy zasobów**: W domyślnej konfiguracji usługa DevTest Labs tworzy grupę zasobów dla każdej nowej maszyny wirtualnej lub użytkownik tworzy środowisko przy użyciu usługi. Subskrypcje mogą zawierać [grup zasobów do 980](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). To był limit maszyn wirtualnych i środowisk, w ramach subskrypcji. Istnieją dwie konfiguracje, które należy wziąć pod uwagę:
    - **[Wszystkie maszyny wirtualne, przejdź do tej samej grupie zasobów](resource-group-control.md)** : Mimo że ten Instalator przyczynia się do limitu grupy zasobów, wpływa na limit zasobów typu zasobu grupy na.
    - **Publiczne adresy IP używana jest udostępniona**: Wszystkie maszyny wirtualne o takiej samej wielkości i region, przejdź do tej samej grupie zasobów. Ta konfiguracja jest "podstaw middle" między limity przydziałów grupy zasobów i przydziały zasobów typu zasobu grupy na, jeśli maszyny wirtualne są dozwolone w publicznych adresów IP.
- **Zasoby dla każdego zasobu Grupuj według typu zasobu**: Domyślny limit [zasoby dla grupy zasobów dla typu zasobu jest 800](../azure-subscription-service-limits.md#resource-group-limits).  Kiedy używasz *wszystkich maszyn wirtualnych, przejdź do tej samej grupie zasobów* konfiguracji, trafień użytkowników tej subskrypcji limit dużo wcześniej, zwłaszcza, jeśli maszyny wirtualne mają wiele dodatkowych dysków.
- **Konta magazynu**: Laboratorium w usłudze DevTest Labs jest powiązana z kontem magazynu. Przydział platformy Azure [liczba kont magazynu na region na subskrypcję jest 250](../azure-subscription-service-limits.md#storage-limits). Maksymalna liczba DevTest Labs, w tym samym regionie jest również 250.
- **Przypisania ról**: Przypisanie roli jest jak dawać użytkownik lub ta jednostka dostęp do zasobów (właściciela, zasobów, poziom uprawnień). Na platformie Azure ma [limit wynoszący 2000 przypisań ról na subskrypcję](../azure-subscription-service-limits.md#role-based-access-control-limits). Domyślnie usługa DevTest Labs tworzy grupę zasobów dla każdej maszyny Wirtualnej. Właściciel jest udzielany *właściciela* uprawnienia dla maszyny Wirtualnej usługi DevTest Labs i *czytnika* uprawnienia do grupy zasobów. W ten sposób każdy nową maszynę Wirtualną, którą tworzysz używa dwóch przypisań ról oprócz przypisania, które są używane podczas nadaj użytkownikom uprawnienia do laboratorium.
- **Interfejs API operacji odczytu/zapisu**: Istnieją różne sposoby automatyzacji platformy Azure i usłudze DevTest Labs, w tym interfejsów API REST, programu PowerShell, interfejsu wiersza polecenia platformy Azure i zestawu Azure SDK. Dzięki automatyzacji może napotkać inny limit żądań interfejsu API: Każda subskrypcja zezwala maksymalnie [12 000 odczytu, żądań i 1200 zapisu żądania na godzinę](../azure-resource-manager/resource-manager-request-limits.md). Należy pamiętać o tego limitu podczas automatyzacji DevTest Labs.

## <a name="manageability-considerations"></a>Zagadnienia dotyczące możliwości zarządzania
DevTest Labs ma interfejs doskonałe użytkownika administracyjnego do pracy z jednym laboratorium. Jednak w przedsiębiorstwie, prawdopodobnie masz wiele subskrypcji platformy Azure i wiele labs. Spójne wprowadzania zmian do wszystkich laboratoriów wymaga obsługi skryptów automatyzacji. Poniżej przedstawiono kilka przykładów i najlepsze praktyki zarządzania do wdrożenia usługi DevTest Labs:

- **Zmiany ustawień laboratorium**: Typowy scenariusz to można zaktualizować ustawienia laboratorium określonych we wszystkich labs we wdrożeniu. Na przykład nowy rozmiar wystąpienia maszyny Wirtualnej jest dostępny, a wszystkie labs należy zaktualizować tak, aby zezwalała. Zaleca się zautomatyzować te zmiany za pomocą skryptów programu PowerShell, interfejsu wiersza polecenia lub interfejsów API REST.  
- **Token pat repozytorium artefaktów**:  Zazwyczaj osobiste tokeny dostępu dla repozytorium Git wygaśnie za 90 dni, jeden rok lub dwa lata. W celu zapewnienia ciągłości jest ważne, aby rozszerzyć osobisty token dostępu lub utworzyć nowy. Następnie Zastosuj nowe osobisty token dostępu do wszystkich laboratoriów przy użyciu usługi automation.
- **Ograniczanie zmian w środowisku laboratoryjnym**: Często konkretnego ustawienia, musi być ograniczony (na przykład umożliwiając korzystanie z obrazów z portalu marketplace). Aby uniemożliwić zmiany typu zasobu, można użyć usługi Azure Policy. Lub możesz utworzyć rolę niestandardową i nadawać użytkownikom tej roli, a nie *właściciela* roli dla laboratorium. Można to zrobić w przypadku większości ustawień w laboratorium (wewnętrznej pomocy technicznej, anonsu w laboratorium, dozwolone rozmiary maszyn wirtualnych i tak dalej).
- **Wymagaj maszyn wirtualnych, aby postępować zgodnie z konwencją nazewnictwa**: Menedżerowie często mają być łatwo zidentyfikować maszyny wirtualne, które są częścią programowania w chmurze i środowisku testowym. Można to zrobić za pomocą [usługi Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Ważne jest, aby Pamiętaj, że usługa DevTest Labs używa podstawowych zasobów platformy Azure, które są zarządzane tak samo: sieć, dysków, obliczeń i tak dalej. Na przykład Azure zasada ma zastosowanie do maszyn wirtualnych, które zostały utworzone w laboratorium. Usługa Azure Security Center może zgłaszać na temat zgodności z maszyny Wirtualnej. A usługa Azure Backup może zapewnić regularnie Twórz kopie zapasowe maszyn wirtualnych w środowisku laboratoryjnym.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa
Usługa Azure DevTest Labs korzysta z istniejących zasobów na platformie Azure (obliczeniowych, sieci i tak dalej). Dlatego automatycznie korzysta z funkcji zabezpieczeń, które są wbudowane w platformę. Na przykład, aby wymagać przychodzących połączeń pulpitu zdalnego pochodzić tylko z sieci firmowej, po prostu Dodaj sieciową grupę zabezpieczeń do sieci wirtualnej na Brama usług pulpitu zdalnego. Tylko dodatkowa kwestia związana zabezpieczeniami jest poziom uprawnień, których możesz udzielić członkom zespołu, którzy korzystają z laboratoriów na podstawie codziennych. Najbardziej typowe uprawnienia są [ *właściciela* i *użytkownika*](devtest-lab-add-devtest-user.md). Aby uzyskać więcej informacji o tych rolach, zobacz [Dodawanie właścicieli i użytkowników w usłudze Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Kolejne kroki
Zobacz następny artykuł w tej serii: [Skalowanie infrastruktury usługi Azure DevTest Labs](devtest-lab-guidance-scale.md).
