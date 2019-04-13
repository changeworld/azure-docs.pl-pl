---
title: Architektura referencyjna dla usługi Azure DevTest Labs w przedsiębiorstwie
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
ms.openlocfilehash: 61e76369a4d73bd171c9e5c2462b3f261681ba00
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551385"
---
# <a name="azure-devtest-labs---reference-architecture-for-an-enterprise"></a>Usługa Azure DevTest Labs — architektura referencyjna dla przedsiębiorstwa
Ten artykuł zawiera architektury referencyjnej do wdrożenia rozwiązanie oparte na usłudze Azure DevTest Labs w przedsiębiorstwie. Obejmuje ona połączeń lokalnych za pomocą usługi Express Route, bramy usług pulpitu zdalnego do zdalnego logowania się do maszyn wirtualnych, łączności z repozytorium artefaktów prywatne artefakty i inne usługi PaaS, używane w laboratorium.

![Architektura referencyjna](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektura
Kluczowe elementy w architekturze referencyjnej są:

- **Azure Active Directory (AAD)**: Usługa Azure DevTest Labs używa [usługi Azure Active Directory do zarządzania tożsamościami](../active-directory/fundamentals/active-directory-whatis.md). Istnieją dwie ważne informacje, które należy wziąć pod uwagę, zapewniając dostęp do środowiska opartego na usłudze DevTest Labs dla użytkowników:
    - **Zarządzanie zasobami**:  Zapewnia dostęp do witryny Azure portal do zarządzania zasobami (Tworzenie maszyn wirtualnych, tworzenie środowisk, start/stop/ponowne uruchomienie/delete/applyartifacts i tak dalej). To się robi na platformie Azure przy użyciu kontroli dostępu na podstawie Roble (RBAC), a zastosowanie przypisania roli dla użytkownika i ustawienia zasobów i uprawnienia na poziomie dostępu.
    - **Maszyny wirtualne (w poziomie sieci)**:  W domyślnej konfiguracji maszyn wirtualnych użyć konta administratora lokalnego.  Jeśli istnieje domena ([AAD Domain services](../active-directory-domain-services/active-directory-ds-overview.md), domeny środowiska lokalnego lub domeny oparte na chmurze), maszyn może być przyłączony do domeny. Gdy dołączony, użytkownicy użyje ich tożsamości opartych na domenie nawiązać połączenia z maszynami wirtualnymi.
- **Połączeniami lokalnymi**: W powyższym diagram architektury [Express Route](../expressroute/expressroute-introduction.md) i używane, ale można również użyć [sieci VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Chociaż nie jest to wymagane dla usługi DevTest Labs, jest częsta w przedsiębiorstwach. Jest to wymagane tylko jeśli istnieje powód, aby połączyć się z zasobami firmy. Typowe przyczyny są następujące: 
    - Dane lokalne, którego nie można przenieść do chmury jeszcze
    - Preferencje umożliwiają przyłączanie maszyn wirtualnych laboratorium do domeny w środowisku lokalnym
    - Wymuszenie całego ruchu sieciowego i w środowisku chmury, za pośrednictwem zapory w środowisku lokalnym ze względów bezpieczeństwa lub zgodności
- **Sieciowe grupy zabezpieczeń**: Typowym sposobem ograniczania ruchu do środowiska chmury (lub w środowisku chmury) na podstawie źródła i docelowych adresów IP jest użycie [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md). Na przykład dzięki czemu tylko ruch sieciowy pochodzący z sieci firmowej w sieciach laboratorium.
- **Brama usług pulpitu zdalnego**:  Przedsiębiorstwom Blokuj zazwyczaj wychodzące połączenia pulpitu zdalnego na zaporze firmowej. Aby włączyć łączność środowisko oparte na chmurze w usłudze DevTest Labs, dostępnych jest kilka opcji, takimi jak wymaganie użycia [bramy usług pulpitu zdalnego](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture) (Lista dozwolonych statyczny adres IP dla modułu równoważenia obciążenia bramy) lub [kierowanie cały ruch przychodzący Ruch RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) za pośrednictwem połączenia Express Route/Site-to-Site VPN. To jest wspólne brany pod uwagę podczas planowania wdrożenia usługi DevTest Labs w przedsiębiorstwie.
- **Sieci platformy Azure (sieci wirtualne, podsieci)**:  [Sieci platformy Azure](../networking/networking-overview.md) topologia jest innym kluczowym elementem w ogólna architektura DevTest Labs. Dzięki temu zasoby z laboratoriów do komunikowania się (lub nie), dostęp do sieci lokalnej (lub nie) i uzyskać dostęp do Internetu (lub nie). Diagram architektury obejmuje najbardziej popularny sposób klienci korzystają z usługi DevTest Labs (labs wszystkich połączonych za pośrednictwem [komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md) przy użyciu [modelu piasty i szprych](/architecture/reference-architectures/hybrid-networking/hub-spoke) połączenia Express Route/Site-to-Site VPN Aby lokalnie), ale ponieważ DevTest Labs korzysta z sieci platformy Azure bezpośrednio nie istnieją jakieś ograniczenia dotyczące sposobu konfigurowania infrastruktury sieci.
- **DevTest Labs**:  DevTest Labs to kluczowa część ogólna architektura. Aby dowiedzieć się więcej o usłudze, zobacz [DevTest Labs](devtest-lab-overview.md).
- **Maszyny wirtualne i inne zasoby (SaaS, PaaS, IaaS)**:  Jedną z kluczowych obciążeń obsługiwanych przez usługi DevTest Labs to maszyny wirtualne wraz z innymi zasobami platformy Azure.  DevTest Labs ułatwia szybkie dla przedsiębiorstw udzielić dostępu do zasobów platformy Azure (w tym maszyn wirtualnych i innych zasobów platformy Azure).  Dowiedz się więcej o dostęp do platformy Azure dla [deweloperów](devtest-lab-developer-lab.md) i [testerów](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności
Mimo że usługa DevTest Labs nie ma żadnych wbudowanych przydziały i limity, innych zasobów platformy Azure są używane w typowej operacji laboratorium ma [przydziały na poziomie subskrypcji](../azure-subscription-service-limits.md). W rezultacie w ramach wdrożenia typowego przedsiębiorstwa, musisz mieć wiele subskrypcji platformy Azure do pokrycia dużych wdrożeniach usługi DevTest Labs. Przydziały najczęściej osiąganego przez przedsiębiorstwa są:

- **Grupy zasobów**:  W domyślnej konfiguracji usługa DevTest Labs tworzy grupę zasobów dla każdej nowej maszyny wirtualnej lub środowisku, w którym zostanie utworzony przy użyciu usługi. Subskrypcje mogą zawierać [maksymalna liczba grup zasobów 980](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager), więc to ograniczenie jest górny limit liczby maszyn wirtualnych i środowisk, w ramach subskrypcji. Istnieją dwie konfiguracje, które należy wziąć pod uwagę:
    - **[Wszystkie maszyny wirtualne, przejdź do tej samej grupie zasobów](resource-group-control.md)**:  Mimo że pomaga limit grupy zasobów ma wpływ na typ zasobu na limit grupy zasobów.
    - **Publiczne adresy IP używana jest udostępniona**:  Wszystkie maszyny wirtualne w tej samej wielkości i tym samym regionie, przejdź do tej samej grupie zasobów. Jeśli maszyny wirtualne mogą mieć publiczne adresy IP, jest "podstaw middle" między limity przydziałów grupy zasobów i typu zasobu na limity przydziałów grupy zasobów. 
- **Grupy zasobów dla każdego zasobu, dla każdego typu zasobu**: Domyślny limit [zasoby na zasób grupy na typ zasobu jest 800](../azure-subscription-service-limits.md#resource-group-limits).  Gdy przy użyciu wszystkich maszyn wirtualnych, przejdź do Konfiguracja tej samej grupy zasobów, użytkownicy osiągnie ten limit subskrypcji znacznie szybciej, zwłaszcza, jeśli maszyny wirtualne mają wiele dodatkowych dysków.
- **Konta magazynu**: Laboratorium w usłudze DevTest Labs jest powiązana z kontem magazynu i przydziału usługi Azure dla [liczba kont magazynu na region na subskrypcję jest 250](../azure-subscription-service-limits.md#storage-limits). Oznacza to, że górny limit liczby DevTest Labs, w tym samym regionie jest również 250.
- **Przypisania ról**: Przypisanie roli jest udzielanie dostępu użytkownik lub ta jednostka do zasobów (właściciela, zasobów, poziom uprawnień). Na platformie Azure, ma [limit wynoszący 2000 przypisań ról na subskrypcję](../azure-subscription-service-limits.md#role-based-access-control-limits). Usługa DevTest Labs (w konfiguracji domyślnej) tworzy grupę zasobów dla każdej maszyny Wirtualnej i zostaną przyznane właścicielem **właściciela** uprawnienia dla maszyny Wirtualnej usługi DevTest Labs i **czytnika** uprawnienia do Grupa zasobów.  W ten sposób każda nowa utworzona maszyna wirtualna używa dwóch przypisań ról dodatkowe przypisań roli tworzone podczas udzielanie użytkownikom uprawnienia do laboratorium.
- **Interfejs API operacji odczytu/zapisu**: Istnieją różne sposoby (interfejsy API REST, programu PowerShell, interfejsu wiersza polecenia, zestaw Azure SDK i tak dalej) do automatyzacji platformy Azure i usłudze DevTest Labs, a za pomocą usługi automation jest możliwe trafień inny limit żądań interfejsu API. Każda subskrypcja zezwala maksymalnie [12000 odczytu, żądań i 1200 zapisu żądania na godzinę](../azure-resource-manager/resource-manager-request-limits.md).  Istnieje limit pod uwagę podczas automatyzacji DevTest Labs.

## <a name="manageability-considerations"></a>Zagadnienia dotyczące możliwości zarządzania
DevTest Labs ma interfejs użytkowników administracyjnych, pracując z jednym laboratorium. W przedsiębiorstwie są prawdopodobnie wiele subskrypcji platformy Azure i wiele labs. Oznacza to, że spójne wprowadzania zmian do wszystkich laboratoriów wymaga obsługi skryptów automatyzacji.  Poniżej przedstawiono kilka przykładów i najlepszy sposób na Zarządzanie wdrażaniem laboratorium:

- **Zmiany ustawień laboratorium**: Typowy scenariusz to można zaktualizować ustawienia laboratorium określonych we wszystkich labs we wdrożeniu. Na przykład nowy rozmiar wystąpienia maszyny Wirtualnej jest dostępny, i wszystkich labs należy zaktualizować tak, aby zezwalała.  Zaleca się zautomatyzować te zmiany za pomocą skryptów programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsów API REST.  
- **Token pat repozytorium artefaktów**:  Zazwyczaj osobiste tokeny dostępu do repozytorium Git wygaśnie (90 dni, 1 rok, 2 lata ochrony przed zdarzeniami). Aby zapewnić ciągłość działalności biznesowej, jest ważne, aby rozszerzyć osobisty token dostępu lub Utwórz nową i zastosować nowe osobisty token dostępu do wszystkich laboratoriów przy użyciu usługi automation.
- **Ograniczenie zmian w środowisku laboratoryjnym**:  Sytuacja często dotyczy przypadku konkretnego ustawienia (na przykład umożliwiając obrazów portalu Marketplace do użycia), które muszą być ograniczone. Można to zrobić za pośrednictwem usługi Azure policy (blokowanie zmian do tego typu zasobu) lub tworząc niestandardową rolę i udzielanie tej roli, a nie "właściciel" do laboratorium. Można to zrobić w przypadku większości ustawień w laboratorium (wewnętrznej pomocy technicznej, anonsu w laboratorium, rozmiarów dozwolone maszyn wirtualnych i tak dalej)
- **Wymagania maszyn wirtualnych z konwencją nazewnictwa**: Jest to typowe żądanie, można łatwo zidentyfikować maszyny wirtualne, które są częścią programowania w chmurze i środowisku testowym. Możesz to zrobić [z usługą Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Należy zauważyć, że usługa DevTest Labs używa zasobów na platformie Azure (sieci, dysków, obliczania, użycia itd), które są zarządzane w taki sam sposób na platformie Azure.  Na przykład usługa Azure policy ma zastosowanie do maszyn wirtualnych utworzonych w laboratorium. Centrum zabezpieczeń Azure można raporty dotyczące zgodności maszyny Wirtualnej. W usłudze Azure Backup może zapewnić regularnie Twórz kopie zapasowe maszyn wirtualnych w laboratorium i tak dalej. 

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami
Usługa Azure DevTest Labs korzysta z istniejących zasobów na platformie Azure (obliczeniowych, sieci i tak dalej) i dlatego automatycznie korzysta z wbudowanej funkcji doskonałe zabezpieczenia do korzystania z platformy. Na przykład aby zabezpieczyć wszystkie przychodzące połączenia pulpitu zdalnego tylko tak, jakby pochodził z sieci firmowej, jest tak proste, jak dodać grupę zabezpieczeń sieci z siecią wirtualną na Brama usług pulpitu zdalnego. Tylko dodatkowa kwestia związana zabezpieczeniami dla usługi Azure DevTest Labs jest poziom uprawnień do członków zespołu, którzy będą korzystać z laboratoriów na podstawie dnia na dzień.  Typowe uprawnienia udzielone są ["Owner" i "Użytkownik usługi DevTest Labs"](devtest-lab-add-devtest-user.md). Aby uzyskać więcej informacji o tych rolach, zobacz [Dodawanie właścicieli i użytkowników w usłudze Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Następne kroki
Zobacz następny artykuł w tej serii: [Skalowanie infrastruktury usługi Azure DevTest Labs](devtest-lab-guidance-scale.md)
