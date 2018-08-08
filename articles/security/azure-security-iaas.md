---
title: Najlepsze rozwiązania dotyczące zabezpieczeń dla IaaS obciążeń na platformie Azure | Dokumentacja firmy Microsoft
description: " Migrację obciążeń do usługi Azure IaaS zapewnia możliwości to ponowne ocenienie nasze projekty "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: barclayn
ms.openlocfilehash: 7c28459aa04c67db8abda54d9f14eb417bd8ed60
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618601"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Najlepsze rozwiązania dotyczące obciążeń IaaS na platformie Azure

W większości infrastruktury jako scenariuszy usługi (IaaS) [maszyn wirtualnych (VM)](https://docs.microsoft.com/azure/virtual-machines/) są głównym obciążenia dla organizacji, które korzystają z chmury obliczeniowej. Ten fakt jest szczególnie widoczne w [scenariuszy hybrydowych](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) gdzie organizacji chce powoli migrację obciążeń do chmury. W takich sytuacjach należy wykonać [zabezpieczenia Ogólne zagadnienia dotyczące modelu IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)i Zastosuj najlepsze rozwiązania dotyczące zabezpieczeń na wszystkich maszynach wirtualnych.

Twoja odpowiedzialność za zabezpieczeń zależy od rodzaju usłudze w chmurze. Poniższej tabeli podsumowano saldo odpowiedzialność firmy Microsoft i możesz:

![Zakres odpowiedzialności](./media/azure-security-iaas/sec-cloudstack-new.png)

Wymagania dotyczące zabezpieczeń różnią się w zależności od szeregu czynników, takich jak różne typy obciążeń. Nie w jednym z tych najlepszych rozwiązań przez siebie zabezpieczyć swoje systemy. Podobnie jak niczego więcej w zabezpieczeń musisz wybierz odpowiednie opcje, a następnie zobacz, jak te rozwiązania mogą uzupełniają się wzajemnie, wypełniając luki.

W tym artykule omówiono różne maszyny Wirtualnej najlepszych rozwiązań dotyczących zabezpieczeń, każdy pochodzi od klientów i bezpośredniego środowiska firmy Microsoft, za pomocą maszyn wirtualnych.

Najlepsze rozwiązania są oparte na konsensus opinii i pracować z aktualnymi możliwościami platformy Azure, a zestawy funkcji. Ponieważ opinie i technologie mogą się zmieniać wraz z upływem czasu, w tym artykule zostanie zaktualizowany odzwierciedlać wprowadzone zmiany.

## <a name="use-privileged-access-workstations"></a>Użyj stacji roboczych z dostępem uprzywilejowanym

Organizacje często można podzielić żerują na ataki cybernetyczne ponieważ administratorzy wykonywać akcje podczas korzystania z kont z podwyższonym poziomem uprawnień. Chociaż nie może to być wynikiem złośliwych działań, odbywa się, ponieważ zezwala istniejącej konfiguracji i procesów. Większość tych użytkowników świadomość ryzyka tych działań z punktu widzenia koncepcyjnej, ale nadal zdecydować się na ich realizacji.

Wykonywanie czynności takich jak sprawdzanie poczty e-mail i przeglądania Internetu wydają się wystarczająco nieszkodliwie. Jednak może być eksponowanie z podwyższonym poziomem uprawnień konta, aby naruszyć bezpieczeństwo, uczestników złośliwych działań. Przeglądanie działań, specjalnie przygotowane wiadomości e-mail lub innych technik może służyć do uzyskania dostępu do Twojej organizacji. Zdecydowanie zalecamy korzystanie z stacje robocze bezpiecznego zarządzania (z stacje SAW) przeprowadzania wszystkich zadań administracyjnych usługi Azure. Stacje SAW są sposobem zmniejszenia narażenia na zagrożenia przypadkowe.

Stacji roboczych z dostępem (Paw) zapewniają dedykowany system operacyjny do realizacji zadań poufnych — taki, który jest chroniony przed atakami internetowymi i wektorami zagrożenia. Oddzielenie poufnych zadań i kont z urządzeń i codzienne użycia stacji roboczych zapewnia silną ochronę. Ta separacja ogranicza wpływ wyłudzanie informacji, aplikacji i luk w zabezpieczeniach systemu operacyjnego, różnych ataków personifikacji i ataków z kradzieżą poświadczeń. (klawiszy, rejestrowanie, Pass--Hash i Pass--Ticket)

Podejście PAW stanowi rozszerzenie sprawdzone i zalecane praktyki indywidualnie przypisane konto administracyjne. Konto administracyjne jest niezależna od standardowego konta użytkownika. Rozwiązania PAW zapewnia wiarygodną stację roboczą dla kont poufnych.

Aby uzyskać więcej informacji i wykonania wskazówek, zobacz [stacji roboczych z dostępem uprzywilejowanym](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

W przeszłości sieci obwodowej był używany do kontrolowania dostępu do danych firmowych. W świecie chmury i mobilność tożsamość jest na płaszczyźnie kontroli: możesz użyć do kontrolowania dostępu do usługi IaaS z dowolnego urządzenia. Można go także użyć widoczności i wgląd w gdzie i jak Twoje dane są używane. Ochrona tożsamości cyfrowej użytkowników platformy Azure stanowi podstawę ochrony subskrypcji przed kradzieżą tożsamości i innych cybercrimes.

Jedną z najbardziej korzystne czynności, które można wykonać, aby zabezpieczyć konto jest włączenie uwierzytelniania dwuskładnikowego. Uwierzytelnianie dwuetapowe jest sposób uwierzytelniania przy użyciu coś, co oprócz hasła. Pomaga ograniczyć ryzyko dostępu przez osobę, która zarządza, aby uzyskać hasło innej osoby.

[Usługa Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) pomaga w zabezpieczeniu dostępu do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Zapewnia ona silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji — połączenie telefoniczne, wiadomość tekstowa lub powiadomienie aplikacji mobilnej. Użytkownicy wybrać odpowiadającej im metody.

Najprostszym sposobem na uwierzytelnianie wieloskładnikowe jest aplikacją mobilną Microsoft Authenticator, która może być używany na urządzeniach przenośnych z systemem Windows, iOS i Android. Za pomocą najnowszej wersji zestawu Windows 10 i integracji lokalnej usługi Active Directory z usługą Azure Active Directory (Azure AD) [Windows Hello dla firm](../active-directory/active-directory-azureadjoin-passport-deployment.md) umożliwia bezproblemowe logowanie jednokrotne do zasobów platformy Azure. W takim przypadku urządzenie systemu Windows 10 jest używane jako drugi składnik uwierzytelniania.

Konta, które zarządzają subskrypcją platformy Azure i kont, którzy mogą zalogować się do maszyn wirtualnych za pomocą uwierzytelniania wieloskładnikowego zapewnia znacznie większy poziom zabezpieczeń niż korzystanie tylko z hasła. Inne formy uwierzytelniania dwuskładnikowego może działać równie dobrze, ale wdrożenie ich może być skomplikowane, jeśli nie są już w środowisku produkcyjnym.

Poniższy zrzut ekranu przedstawia niektóre opcje dostępne dla usługi Azure Multi-Factor Authentication:

![Opcje uwierzytelniania wieloskładnikowego](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Ograniczenia i ograniczenie dostępu administracyjnego

Ważne jest Zabezpieczanie kont, które pozwala na Zarządzanie subskrypcją platformy Azure. Naruszenia zabezpieczeń w dowolnej z tych kont neguje wartości wszystkich kroków, które może podjąć w celu zapewnienia poufności i integralności danych. Ostatnio przedstawiono przez [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) atakami wewnętrznymi stanowią zagrożenie duży na ogólne bezpieczeństwo każdej organizacji.

W celu oceny osoby do prawa administracyjne następujące kryteria podobne do następujących:

- Są one wykonywane zadania, które wymagają uprawnień administratora
- Jak często są wykonywane te zadania
- Czy istnieje konkretny powód, dlaczego nie można wykonać zadania przez innego administratora w ich imieniu?

Dokumentowanie wszystkich innych znanych alternatywnych metod przyznawania uprawnień i każdy Dlaczego dopuszczalne.

Just enough administration zapobiega niepotrzebne istnieją konta z podwyższonym poziomem uprawnień w okresach, gdy te prawa nie są wymagane. Konta kontem z podniesionymi uprawnieniami przez ograniczony czas, aby administratorzy mogą wykonywania ich zadań. Następnie te prawa są usuwane po zakończeniu zmiany lub po zakończeniu zadania.

Możesz użyć [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) do zarządzania, monitorowanie i kontrolowanie dostępu w Twojej organizacji. Ułatwia on pamiętać o akcję wykonywaną przez osoby w Twojej organizacji. Stwarza również administracji just in time do usługi Azure AD przez wprowadzenie pojęcia uprawnionymi administratorami. Są to osoby, którzy mają konta, z których należy przyznać uprawnienia administratora. Te typy użytkowników, należy udzielić uprawnień administratora przez ograniczony czas i można przejść przez proces aktywacji.

## <a name="use-devtest-labs"></a>Użyj usługi DevTest Labs

Oferta Azure for labs i środowisk programistycznych przejmowałaby opóźnień, które wprowadza zakupów sprzętu. Dzięki temu organizacje mogą zyskać elastyczność w środowisk testowych i programistycznych. Z drugiej strony Brak znajomość platformy Azure lub wymaganą w celu przyspieszenia jej przyjęcia może prowadzić z administratorem, aby być nadmiernie ograniczająca przy użyciu przypisywania uprawnień. To zagrożenie może przypadkowo uwidocznić organizacji atakami wewnętrznymi. Niektórzy użytkownicy mogą otrzymać dostęp o wiele więcej niż powinny mieć.

[Usługi Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) usługi używa [kontroli dostępu](../role-based-access-control/overview.md) (RBAC). Za pomocą funkcji RBAC, można segregowanie zadań w ramach zespołu do ról, które mają przyznaną tylko poziom dostępu, które są niezbędne dla użytkowników do wykonywania ich zadań. RBAC jest powiązana z wstępnie zdefiniowanych ról (właściciel, użytkownik laboratorium i współautor). Te role umożliwia nawet przypisania praw partnerom zewnętrznym i znacznie ułatwiają współpracę.

Ponieważ usługa DevTest Labs korzysta z funkcji RBAC, jest możliwe do utworzenia dodatkowych, [ról niestandardowych](../lab-services/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs nie tylko upraszcza zarządzanie uprawnieniami, upraszcza proces konfigurowania środowisk aprowizowane. Ułatwia on również dotyczyć innych typowych wyzwań związanych z zespołów, które działają w środowiskach deweloperskich i testowych. Wymaga to pewnych przygotowań, ale w długim okresie, jego ułatwi czynności dla Twojego zespołu.

Usługa Azure DevTest Labs funkcje obejmują:

- Kontrolę administracyjną nad opcje dostępne dla użytkowników. Administrator centralnie zarządzać elementów, takich jak dozwolone rozmiary maszyn wirtualnych, maksymalna liczba maszyn wirtualnych, a następnie po uruchomieniu maszyn wirtualnych i zamykania.
- Automatyzacja tworzenia środowiska laboratoryjnego.
- Śledzenie kosztów.
- Uproszczone rozkład maszyn wirtualnych dla tymczasowych pracy zespołowej.
- Samoobsługa, która umożliwia użytkownikom aprowizację ich labs przy użyciu szablonów.
- Zarządzanie i ograniczenie zużycia.

![DevTest Labs](./media/azure-security-iaas/devtestlabs.png)

Bez dodatkowych kosztów jest skojarzony z użyciem usługi DevTest Labs. Tworzenie laboratoriów, zasady, szablonów i artefaktów jest bezpłatne. Płacisz tylko zasobów platformy Azure używane w laboratorium, takie jak maszyny wirtualne, konta magazynu i sieci wirtualnych.

## <a name="control-and-limit-endpoint-access"></a>Kontrolki i limit dostępu do punktu końcowego

Hostingu labs ani systemów produkcyjnych na platformie Azure oznacza, że na komputerach muszą być dostępne z Internetu. Domyślnie nowa maszyna wirtualna Windows ma portu RDP dostępny z Internetu, a maszynę wirtualną z systemem Linux ma otworzyć port SSH. Wykonując kroki limit udostępniane punkty końcowe jest niezbędne zminimalizować ryzyko przed nieautoryzowanym dostępem.

Technologii w systemie Azure może pomóc ograniczyć dostęp do tych administracyjne punktów końcowych. Na platformie Azure, można użyć [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) (NSG). Gdy używasz usługi Azure Resource Manager dla wdrożenia, sieciowe grupy zabezpieczeń Ogranicz dostęp ze wszystkich sieci, można po prostu punktów końcowych zarządzania (protokołu RDP lub SSH). Myśląc sieciowych grup zabezpieczeń, należy traktować routera listy kontroli dostępu. Można ich użyć ściśle kontrolować komunikacji sieciowej między różnych segmentów sieci platformy Azure. Jest to podobne do tworzenia sieci w sieci obwodowej lub w innych sieciach izolowanych. Nie kontroluj ruchu, ale pomagają segmentacji sieci.

Bardziej dynamiczny sposób ograniczania dostępu do maszyn wirtualnych jest użycie usługi Azure Security center [tylko w czasie administracji](../security-center/security-center-just-in-time.md). Usługa Security center można zablokować maszynach wirtualnych platformy Azure i zapewnia dostęp w razie. Proces działa, zezwalając na dostęp do użytkownika, który go zażąda po zweryfikowaniu, że na podstawie ich [kontroli dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md) (RBAC), które mają odpowiednie uprawnienia. Azure Security center będzie następnie wprowadzić niezbędne sieciowych grup zabezpieczeń (NSG), aby zezwolić na ruch przychodzący.

### <a name="site-to-site-vpnvpn-gatewayvpn-gateway-howto-site-to-site-resource-manager-portalmd"></a>[Sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Sieć VPN lokacja lokacja rozszerza sieć lokalną do chmury. Daje to możliwość użycia grup NSG, ponieważ można również zmodyfikować sieciowe grupy zabezpieczeń zezwalają na dostęp z dowolnego miejsca inny niż sieci lokalnej. Następnie można wymagać, że administrowanie odbywa się przez nawiązanie sieci platformy Azure za pośrednictwem sieci VPN.

Opcja sieci VPN typu lokacja lokacja może być najbardziej atrakcyjnym w przypadkach, w którym są hostowane systemów produkcyjnych, które są ściśle zintegrowane z zasobów lokalnych na platformie Azure.

### <a name="point-to-sitevpn-gatewayvpn-gateway-howto-point-to-site-rm-psmd"></a>[Punkt lokacja](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

W sytuacjach, w którym chcesz zarządzać systemów, które nie wymagają dostępu do zasobów lokalnych. Tych systemów można wyizolować w ich własnych sieci wirtualnych platformy Azure. Administratorzy mogą sieci VPN na platformie Azure hostowane środowisko z administracyjnej stacji roboczej.

>[!NOTE]
>Jedną z opcji sieci VPN można użyć do zmiany konfiguracji listy ACL na sieciowe grupy zabezpieczeń zezwalają na dostęp do punktów końcowych zarządzania z Internetu.

### <a name="remote-desktop-gatewayactive-directoryauthenticationhowto-mfaserver-nps-rdgmd"></a>[Brama pulpitu zdalnego](../active-directory/authentication/howto-mfaserver-nps-rdg.md)

Bezpiecznie łączyć się z serwerami usług pulpitu zdalnego przy użyciu protokołu HTTPS podczas stosowania bardziej szczegółowe kontrolek do tych połączeń, można użyć bramy usług pulpitu zdalnego.

Funkcje, które będą mieć dostęp do uwzględnienia:

- Opcje administratora Ograniczanie połączeń do żądania z konkretnych systemów.
- Uwierzytelnianie karty inteligentnej lub usługi Azure Multi-Factor Authentication.
- Kontrolowanie za pośrednictwem systemów, które osoba może połączyć się za pośrednictwem bramy.
- Kontrolę nad przekierowanie urządzenia i dysku.

### <a name="vm-availability"></a>Dostępność maszyny wirtualnej

Jeśli maszyna wirtualna działa najważniejsze aplikacje, które muszą mieć wysokiej dostępności, stanowczo zaleca się stosowanie wielu maszyn wirtualnych. Dla lepszej dostępności Utwórz co najmniej dwóch maszyn wirtualnych w [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md).

[Usługa Azure Load Balancer](../load-balancer/load-balancer-overview.md) wymaga również, że maszyn wirtualnych z równoważeniem obciążenia należą do tego samego zestawu dostępności. Jeśli te maszyny wirtualne muszą być dostępne z Internetu, należy skonfigurować [modułu równoważenia obciążenia dostępnego z Internetu](../load-balancer/load-balancer-internet-overview.md).

## <a name="use-a-key-management-solution"></a>Korzystanie z rozwiązania zarządzania kluczami

Bezpieczne zarządzanie kluczami jest podstawą ochrony danych w chmurze. Za pomocą [usługi Azure Key Vault](../key-vault/key-vault-whatis.md), możesz bezpiecznie przechowywać klucze szyfrowania i małych wpisów tajnych, takich jak hasła w sprzętowych modułach zabezpieczeń (HSM). W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w modułach HSM.

Firma Microsoft będzie przetwarzać klucze w trybie FIPS 140-2 poziom 2 zweryfikowanych sprzętowych modułach zabezpieczeń (sprzęt i oprogramowanie układowe). Użycie klucza monitorować i sprawdzać za pomocą funkcji rejestrowania platformy Azure: potok jest rejestrowany w systemie Azure lub Security Information and Event Management (SIEM) na potrzeby dodatkowej analizy i wykrywania zagrożeń.

Każda osoba z subskrypcją platformy Azure można tworzyć i używać magazynów kluczy. Mimo że usługa Key Vault przynosi korzyści deweloperom i administratorom zabezpieczeń, można zaimplementować i zarządza administrator, który jest odpowiedzialny za zarządzanie usługami systemu Azure w organizacji.

## <a name="encrypt-virtual-disks-and-disk-storage"></a>Szyfrowanie dysków wirtualnych i magazynu danych na dysku

[Usługa Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) adresy zagrożeniem kradzieżą lub ujawnieniem przed nieautoryzowanym dostępem, które odbywa się przez przeniesienie dysku danych. Można dołączyć dysku do innego systemu jako sposób pomijanie innych formantów zabezpieczeń. Dysk używa szyfrowania [funkcji BitLocker](https://technet.microsoft.com/library/hh831713) Windows i DM-Crypt w systemie Linux, aby szyfrować systemu operacyjnego i dysków z danymi. Usługa Azure Disk Encryption integruje się z usługą Key Vault w celu kontrolowania i zarządzać kluczami szyfrowania. Jest ona dostępna dla standardowych maszyn wirtualnych i maszyn wirtualnych dzięki usłudze premium storage.

Aby uzyskać więcej informacji, zobacz [usługi Azure Disk Encryption w Windows i maszyn wirtualnych IaaS z systemem Linux](azure-security-disk-encryption.md).

[Szyfrowanie usługi Azure Storage](../storage/common/storage-service-encryption.md) pomaga chronić dane magazynowane. Jest ona włączona na poziomie konta magazynu. Szyfruje dane, są zapisywane w naszych centrach danych i są automatycznie odszyfrowywane, jak można uzyskać do niego dostęp. Obsługuje następujące scenariusze:

- Szyfrowanie blokowych obiektów blob, uzupełnialne i stronicowe obiekty BLOB
- Szyfrowanie zarchiwizowane wirtualne dyski twarde i szablony przeniesione do platformy Azure ze środowiska lokalnego
- Szyfrowanie dysków systemu operacyjnego i danych dla maszyn wirtualnych IaaS, który został utworzony przy użyciu sieci wirtualnych dysków twardych

Przed kontynuowaniem pracy przy użyciu szyfrowania magazynu platformy Azure należy pamiętać o dwa ograniczenia:

- Nie jest dostępne w klasycznych kont magazynu.
- Koduje tylko dane zapisane po włączeniu szyfrowania.

## <a name="use-a-centralized-security-management-system"></a>System zarządzania scentralizowanych zabezpieczeń

Serwery muszą być monitorowane do stosowania poprawek, konfiguracji, zdarzeń i działań, które może zostać uznane za obawy związane z bezpieczeństwem. Aby rozwiązać te problemy, możesz użyć [usługi Security Center](https://azure.microsoft.com/services/security-center/) i [Operations Management Suite Security and Compliance](https://azure.microsoft.com/services/security-center/). Obie te opcje wykracza poza Konfiguracja w systemie operacyjnym. Zapewniają także monitorowanie konfiguracji podstawowej infrastruktury, takich jak konfiguracja sieci i używania urządzenia wirtualnego.

## <a name="manage-operating-systems"></a>Zarządzanie systemami operacyjnymi

We wdrożeniu IaaS jesteś odpowiedzialny za zarządzanie systemów, które można wdrożyć, tak jak dowolnego serwera lub na stacji roboczej w danym środowisku. Stosowanie poprawek, zaostrzanie poziomu zabezpieczeń, przypisywania praw i dowolne inne działanie związane z konserwacji systemu nadal jest odpowiedzialny za. Dla systemów, które są ściśle zintegrowane z Twoich zasobów w środowisku lokalnym można użyć tych samych narzędzi i procedur, że używasz lokalnych, takich jak oprogramowanie antywirusowe, ochrony przed złośliwym kodem, poprawki i kopii zapasowej.

### <a name="harden-systems"></a>Utrwalanie systemów

Powinny być wzmocnione wszystkich maszyn wirtualnych w modelu IaaS platformy Azure, umożliwiające eksponowanie tylko punkty końcowe usługi, które są wymagane dla aplikacji, które są zainstalowane. W przypadku maszyn wirtualnych Windows postępuj zgodnie z zaleceniami przez firmę Microsoft jako podstawy dla [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) rozwiązania.

Menedżer zgodności zabezpieczeń to bezpłatne narzędzie firmy. Można użyć go, aby szybko skonfigurować i zarządzać komputerów stacjonarnych, tradycyjnych centrów danych i chmury prywatnej i publicznej za pomocą zasad grupy i System Center Configuration Manager.

Menedżer zgodności zabezpieczeń zapewnia gotowe do wdrożenia zasad i pakiety konfiguracyjne Desired Configuration Management, które są badane. Te plany bazowe są oparte na [wskazówki dotyczące zabezpieczeń firmy Microsoft](https://technet.microsoft.com/library/cc184906.aspx) zalecenia i w przemyśle najlepszych rozwiązań. Ułatwiają one zarządzanie nieaktualną konfigurację, adres wymagań dotyczących zgodności i ograniczyć zagrożenia bezpieczeństwa.

Menedżer zgodności zabezpieczeń służy do importowania bieżącą konfigurację komputerów za pomocą dwóch różnych metod. Po pierwsze można zaimportować zasad grupy opartych na usłudze Active Directory. Po drugie, można zaimportować konfigurację "złotą" komputer wzorcowy przy użyciu [narzędzie LocalGPO](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) do tworzenia kopii zapasowych lokalnych zasadach grupy. Następnie można zaimportować zasad grupy lokalnej do Menedżera zgodności zabezpieczeń.

Porównaj standardy branżowe najlepszych rozwiązań, dostosuj je i Utwórz nowe zasady i pakiety konfiguracyjne Desired Configuration Management. Linie bazowe zostały opublikowane dla wszystkich obsługiwanych systemach operacyjnych, w tym Rocznicowej aktualizacji systemu Windows 10 i Windows Server 2016.


### <a name="install-and-manage-antimalware"></a>Instalowanie i zarządzanie nią ochrony przed złośliwym kodem

W środowiskach, które znajdują się oddzielnie od środowiska produkcyjnego można użyć rozszerzenia ochrony przed złośliwym oprogramowaniem pomagające chronić maszyny wirtualne i usługi w chmurze. Integruje się z [usługi Azure Security Center](../security-center/security-center-intro.md).

[Microsoft Antimalware](azure-security-antimalware.md) zawiera funkcje, takie jak ochrona w czasie rzeczywistym, zaplanowane skanowanie, korygowaniem złośliwego oprogramowania, aktualizacje podpisu, aktualizacje aparatu, raportowania zbierania zdarzeń wykluczeń, przykłady i [ObsługaprogramuPowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension).

![Usługa Azure ochrony przed złośliwym kodem](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Zainstaluj najnowsze aktualizacje zabezpieczeń 

Niektóre z pierwszych obciążeń, które klientom przenoszenie na platformę Azure są laboratoria i systemy dołączonej do Internetu. W przypadku maszyn wirtualnych hostowanych na platformie Azure, hostować aplikacje lub usługi, które mają być dostępne w Internecie, być czujność dotyczących poprawiania. Poprawki poza działanie systemu operacyjnego. Bez luk w zabezpieczeniach w aplikacjach innych firm również może prowadzić do problemów, które można uniknąć, jeśli zarządzanie poprawkami dobre znajduje się w miejscu.

### <a name="deploy-and-test-a-backup-solution"></a>Wdrażanie i testowanie rozwiązania tworzenia kopii zapasowych

Podobnie jak aktualizacje zabezpieczeń kopii zapasowej musi być obsługiwane w taki sam sposób obsługi innych operacji. Dotyczy to systemów, które są częścią środowiska produkcyjnego, rozszerzając w chmurze. Systemy testowych i deweloperskich, należy wykonać strategii tworzenia kopii zapasowych, które zapewniają możliwości przywracania, które są podobne do użytkownicy wzrosła przyzwyczajeni, na podstawie ich użycia w środowiskach lokalnych.

Obciążenia produkcyjne przeniesione do platformy Azure należy zintegrować z istniejącymi rozwiązaniami kopii zapasowej, jeśli jest to możliwe. Lub możesz użyć [kopia zapasowa Azure](../backup/backup-azure-arm-vms.md) do rozwiązywania wymogami dotyczącymi kopii zapasowej.

## <a name="monitor"></a>Monitorowanie

### <a name="security-centersecurity-centersecurity-center-intromd"></a>[Security Center](../security-center/security-center-intro.md)

Usługa Security center zapewnia ciągłej oceny stanu zabezpieczeń zasobów platformy Azure do identyfikowania potencjalnych luk w zabezpieczeniach. Lista zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych elementów sterujących.

Przykłady:

- Inicjowanie obsługi ochrony przed złośliwym kodem w celu łatwiejszego identyfikowania i usuwania złośliwego oprogramowania.
- Konfigurowanie grup zabezpieczeń sieci i reguł sterujących ruchem do maszyn wirtualnych.
- Inicjowanie obsługi administracyjnej zapory aplikacji sieci web, aby pomóc Ci chronić przed atakami, których platformą docelową aplikacji sieci web.
- Wdrażanie brakujących aktualizacji systemu.
- Modyfikowanie konfiguracji systemu operacyjnego, które nie są zgodne z zalecanych linii bazowych.

Na poniższej ilustracji przedstawiono niektóre opcje, które można włączyć w usłudze Security Center.

![Zasady usługi Azure Security Center](./media/azure-security-iaas/security-center-policies.png)

### <a name="operations-management-suiteoperations-management-suiteoperations-management-suite-overviewmd"></a>[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) 

Pakietu Operation Management Suite to oparta na chmurze rozwiązanie zarządzania IT ułatwiające zarządzanie chronić lokalne i infrastruktury chmury firmy Microsoft. Ponieważ pakietu Operations Management Suite jest zaimplementowany jako usługa w chmurze, można wdrożyć, szybko i przy minimalnym poziomie inwestycji w zasoby infrastruktury.

Nowe funkcje są dostarczane automatycznie, eliminuje konieczność konserwacji i uaktualniania kosztów. Pakiet Operations Management Suite integruje się również z programu System Center Operations Manager. Ma różne składniki, aby pomóc Ci lepiej zarządzać obciążeń platformy Azure, w tym [Security and Compliance](../operations-management-suite/oms-security-getting-started.md) modułu.

Aby wyświetlić informacje dotyczące zasobów, można użyć funkcji zabezpieczeń i zgodności w usłudze Operations Management Suite. Informacje są zorganizowane w cztery główne kategorie:

- **Domeny zabezpieczeń**: dalszą analizę rekordów zabezpieczeń wraz z upływem czasu. Dostęp do oceny złośliwego oprogramowania, aktualizacji oceny, informacje o zabezpieczeniach sieci, tożsamości i dostępu do informacji i komputerów ze zdarzeniami zabezpieczeń. Wykorzystaj szybki dostęp do pulpitu nawigacyjnego Azure Security Center.
- **Problemy godne uwagi**: szybkie identyfikowanie liczby aktywnych problemów i ważności tych problemów.
- **Wykrycia (Podgląd)**: Identyfikowanie ataku wzorców przez wizualizację alertów zabezpieczeń, po ich wprowadzeniu dotyczących poszczególnych zasobów.
- **Analiza zagrożeń**: Identyfikowanie ataku wzorców dzięki wizualizowaniu całkowita liczba serwerów z wychodzącym złośliwym ruchem IP, typu złośliwych zagrożeń oraz mapy pochodzenia, skąd pochodzą te adresy IP.
- **Typowe zapytania dotyczące zabezpieczeń**: Zobacz listę najbardziej typowych zapytań zabezpieczeń, które służą do monitorowania środowiska. Po kliknięciu jednego z tych zapytań **wyszukiwania** bloku otwiera i wyświetla wyniki tego zapytania.

Poniższy zrzut ekranu przedstawia przykład informacji wyświetlanych w pakiecie Operations Management Suite.

![Podstawy zabezpieczeń pakietu Operations Management Suite](./media/azure-security-iaas/oms-security-baseline.png)

### <a name="monitor-vm-performance"></a>Monitorowanie wydajności maszyny Wirtualnej

W przypadku maszyny Wirtualnej procesy zużywać więcej zasobów niż powinni nadużycie zasobu może to stanowić problem. Problemy z wydajnością za pomocą maszyny Wirtualnej może prowadzić do przerwy w działaniu usługi, która narusza zasady zabezpieczeń dostępności. Z tego powodu należy bezwzględnie monitorować dostęp do maszyny Wirtualnej nie tylko sposób reaktywny, gdy problem, ale również proaktywnie względem punktu odniesienia wydajności mierzonych podczas normalnego działania.

Analizując [pliki dzienników diagnostycznych platformy Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), można monitorować swoje zasoby maszyny Wirtualnej i identyfikowania potencjalnych problemów, które mogą negatywnie wpłynąć na wydajność i dostępność. Rozszerzenie diagnostyki platformy Azure zawiera funkcje monitorowania i diagnostyki na maszynach wirtualnych z systemem Windows. Możesz włączyć te możliwości, łącznie z rozszerzeniem jako część [szablonu usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md).

Można również użyć [usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) uzyskać wgląd w kondycję Twoich zasobów.

Organizacje, które nie monitorować wydajność maszyny Wirtualnej są nie można określić, czy pewne zmiany we wzorcach wydajności są prawidłowe lub nieprawidłowe. Jeśli maszyna wirtualna zużywa więcej zasobów niż zwykle, takie anomalii może wskazywać potencjalny atak z zewnętrznego zasobu lub ze złamanymi zabezpieczeniami procesu uruchomionego na maszynie wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

* [Blog zespołu ds. zabezpieczeń platformy Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Centrum zabezpieczeń firmy Microsoft](https://technet.microsoft.com/library/dn440717.aspx)
* [Najlepsze rozwiązania dotyczące zabezpieczeń platformy Azure i wzorców](security-best-practices-and-patterns.md)
