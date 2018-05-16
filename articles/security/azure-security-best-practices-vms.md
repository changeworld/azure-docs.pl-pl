---
title: Najlepsze rozwiązania maszyny wirtualnej platformy Azure
description: Ten artykuł zawiera szereg najlepszych rozwiązań dotyczących zabezpieczeń do użycia w przypadku maszyn wirtualnych znajdujących się na platformie Azure.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: ''
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 832f842aeae53e9c089a9889bf064918de417ed5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="best-practices-for-azure-vm-security"></a>Najlepsze rozwiązania dotyczące zabezpieczeń maszyny Wirtualnej Azure

W większości infrastruktury jako scenariuszy usługa (IaaS) [Azure maszynach wirtualnych (VM)](https://docs.microsoft.com/azure/virtual-machines/) są główne obciążenia dla organizacji korzystających z chmury obliczeniowej. Ten fakt jest szczególnie widoczne w [scenariuszy hybrydowych](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) miejscu organizacje powoli migracji obciążeń do chmury. W takich sytuacjach należy wykonać [zabezpieczeń Ogólne zagadnienia dotyczące IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)i stosowania najlepszych rozwiązań dotyczących zabezpieczeń na wszystkich maszynach wirtualnych.

W tym artykule opisano różne wirtualna najlepszych rozwiązań dotyczących zabezpieczeń, każdy pochodzące z naszych klientów i własnej bezpośrednio z maszyną wirtualną.

Najlepsze rozwiązania są oparte na konsensu opinii i pracować z bieżącej funkcji platformy Azure i zestawy funkcji. Ponieważ opinie i technologie mogą ulec zmianie, firma Microsoft planuje aktualizację w tym artykule regularnie w celu odzwierciedlenia tych zmian.

Dla każdego ze względów wyjaśniono:

* Co to jest najlepszym rozwiązaniem.
* Dlaczego jest dobrym rozwiązaniem, aby je włączyć.
* Jak możesz dowiedzieć się ją włączyć.
* Co może się zdarzyć, jeśli nie można go włączyć.
* Możliwe alternatywy najlepsze rozwiązanie.

Artykuł sprawdza następujące najlepsze rozwiązania zabezpieczeń maszyny Wirtualnej:

* Maszyna wirtualna uwierzytelniania i kontroli dostępu
* Maszyna wirtualna dostępności i dostępu do sieci
* Ochrona danych przechowywanych na maszynach wirtualnych przez wymuszenie szyfrowania
* Zarządzanie aktualizacjami sieci maszyny Wirtualnej
* Zarządzanie strukturę bezpieczeństwa maszyny Wirtualnej
* Monitor wydajności maszyny Wirtualnej

## <a name="vm-authentication-and-access-control"></a>Maszyna wirtualna uwierzytelniania i kontroli dostępu

Pierwszym środkiem ochrony maszyny Wirtualnej jest zapewnienie, że tylko autoryzowani użytkownicy będą mogli skonfigurować nowe maszyny wirtualne. Można użyć [zasady Azure](../azure-policy/azure-policy-introduction.md) nawiązywania konwencje dla zasobów w organizacji, tworzenie zasad niestandardowych i zastosować te zasady do zasobów, takich jak [grup zasobów](../azure-resource-manager/resource-group-overview.md).

Maszyny wirtualne, które należą do grupy zasobów naturalnie dziedziczą jego zasadami. Mimo że firma Microsoft zaleca to rozwiązanie do zarządzania maszynami wirtualnymi, można także kontrolować dostęp do poszczególnych zasad maszyny Wirtualnej za pomocą [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md).

Po włączeniu zasady Resource Manager i RBAC kontrolować dostęp do maszyny Wirtualnej, można zwiększyć ogólne bezpieczeństwo maszyny Wirtualnej. Firma Microsoft zaleca konsolidować maszyn wirtualnych z tego samego cyklu życia w tej samej grupie zasobów. Za pomocą grup zasobów, można wdrożyć, monitorowania i rzutowanie rozliczeń kosztów zasobów. Aby umożliwić użytkownikom dostęp i konfigurowanie maszyn wirtualnych, należy użyć [najniższych uprawnień podejście](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models). A jeśli uprawnienia są przypisane do użytkowników, będą używane następujące role wbudowane Azure:

- [Maszyny wirtualnej współautora](../role-based-access-control/built-in-roles.md#virtual-machine-contributor): można zarządzać maszynami wirtualnymi, ale nie wirtualnych sieci lub magazynu konto z którym jest połączony.
- [Klasycznym współautora maszyny wirtualnej](../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): można zarządzać maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania, ale nie z wirtualnych sieci lub magazynu konta połączenie maszyn wirtualnych.
- [Administrator zabezpieczeń](../role-based-access-control/built-in-roles.md#security-admin): można zarządzać składników zabezpieczeń i zasady zabezpieczeń.
- [DevTest Labs użytkownika](../role-based-access-control/built-in-roles.md#devtest-labs-user): można przeglądać wszystko i połączyć, uruchom ponownie uruchom i zamknij maszyny wirtualne.

Nie udostępniaj kont i haseł innym administratorom, a nie ponownie użyć hasła w wielu kont użytkowników lub usług, szczególnie hasła związanych z mediami społecznościowymi lub innymi działaniami innych niż administracyjne. W idealnym przypadku należy użyć [usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) następujące szablony maszyn wirtualnych bezpieczny sposób. Przy użyciu tej metody, można wzmocnienie wybrane opcje wdrażania oraz wymusi ustawienia zabezpieczeń w całym wdrożeniu.

Organizacji, które nie wymusić kontrolę dostępu do danych dzięki wykorzystaniu możliwości, takich jak RBAC może być przyznania użytkownikom więcej uprawnień niż jest to konieczne. Niewłaściwe użytkownikowi dostęp do niektórych danych bezpośrednio może naruszyć tych danych.

## <a name="vm-availability-and-network-access"></a>Maszyna wirtualna dostępności i dostępu do sieci

Jeśli maszyna wirtualna jest uruchomiona krytyczne aplikacje, które wymagają wysokiej dostępności, zdecydowanie zaleca się użycie wielu maszyn wirtualnych. Aby uzyskać większą dostępność, Utwórz co najmniej dwóch maszyn wirtualnych w [zestawu dostępności](../virtual-machines/windows/tutorial-availability-sets.md).

[Moduł równoważenia obciążenia Azure](../load-balancer/load-balancer-overview.md) wymaga również, że równoważeniem obciążenia maszyn wirtualnych należą do tego samego zestawu dostępności. Jeśli te maszyny wirtualne muszą być dostępne z Internetu, należy skonfigurować [modułu równoważenia obciążenia internetowy](../load-balancer/load-balancer-internet-overview.md).

Jeśli maszyny wirtualne są udostępniane w Internecie, ważne jest, że [sterowaniu przepływem ruchu sieciowego z grup zabezpieczeń sieci (NSG)](../virtual-network/virtual-networks-nsg.md). Ponieważ grupy NSG można zastosować do podsieci, można zminimalizować liczbę grup NSG przez grupowanie zasobów według podsieci i zastosowanie grup NSG do podsieci. W zamierzeniu, aby utworzyć warstwę izolacji sieci, co można zrobić, odpowiednio konfigurując [zabezpieczenia sieci](../best-practices-network-security.md) możliwości na platformie Azure.

Umożliwia także funkcję just in time (JIT) dostęp do maszyny Wirtualnej z Centrum zabezpieczeń Azure do kontrolowania, kto ma dostęp zdalny do określonej maszyny Wirtualnej i jak długo.

Organizacje, które nie Wymuszaj ograniczenia dostępu do sieci maszyn wirtualnych połączonych z Internetem są narażone na zagrożenia bezpieczeństwa, takie jak atak Siłowy protokołu RDP (Remote Desktop).

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>Ochrona danych przechowywanych na maszyn wirtualnych przez wymuszenie szyfrowania

[Szyfrowanie danych magazynowanych](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) jest obowiązkowy warstwę danych suwerenności prywatności, zgodności i danych. [Szyfrowanie dysków Azure](../security/azure-security-disk-encryption.md) umożliwiają administratorom szyfrowanie dysków systemu Windows i maszyny Wirtualnej systemu Linux IaaS. Szyfrowanie dysków łączy standardowych funkcji Windows BitLocker i funkcję dm-crypt systemu Linux w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi.

Można zastosować szyfrowanie dysków, które zapewni ochronę danych w celu spełnienia zabezpieczeń organizacji i wymagań dotyczących zgodności. Organizacji należy wziąć pod uwagę przy użyciu szyfrowania w celu zmniejszenia ryzyka danych związanych z nieautoryzowanego dostępu. Zalecamy również szyfrowania dysków, przed przystąpieniem do napisania poufne dane do nich.

Pamiętaj zaszyfrować woluminach danych maszyny Wirtualnej, aby je chronić, przechowywane na koncie magazynu Azure. Ochrona przy użyciu kluczy szyfrowania i klucz tajny [usługi Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-whatis/).

Organizacje, które nie wymuszania szyfrowania danych są bardziej widoczne dla problemy z integralnością danych. Na przykład złośliwe lub nieautoryzowanym użytkownikom może kradzieży danych, którego bezpieczeństwo zostało naruszone konta lub uzyskania nieautoryzowanego dostępu do danych w ClearFormat na stałe. Oprócz biorąc na tego ryzyka, w celu zapewnienia przestrzegania przepisów branżowych firm musi udowodnić, że są wykonywania starannością oraz za pomocą formantów poprawnych zabezpieczeń, aby zwiększyć ich bezpieczeństwo danych.

Aby dowiedzieć się więcej na temat szyfrowania dysków, zobacz [Azure dysku szyfrowanie dla systemu Windows i maszyn wirtualnych systemu Linux IaaS](azure-security-disk-encryption.md).


## <a name="manage-your-vm-updates"></a>Zarządzanie aktualizacjami sieci maszyny Wirtualnej

Ponieważ maszynach wirtualnych platformy Azure, takich jak wszystkich lokalnych maszyn wirtualnych powinny być zarządzane przez użytkownika, Azure nie wypychania aktualizacji systemu Windows do nich. Jesteś, jednak zaleca się, aby pozostawić włączone automatyczne ustawienie usługi Windows Update. Innym rozwiązaniem jest wdrożenie [systemu Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) lub innego produktu odpowiednie zarządzanie aktualizacjami w innej maszyny Wirtualnej lub lokalnymi. Zarówno program WSUS i usługi Windows Update aktualizował maszyny wirtualne. Zalecamy również użyć skanowania produktu można zweryfikować, że wszystkie maszyny wirtualne IaaS są aktualne.

Obrazy standardowych dostarczany przez platformę Azure są regularnie zaktualizowano rundzie najnowsze aktualizacje systemu Windows. Jednak nie ma żadnej gwarancji, że będzie bieżący w czasie wdrażania obrazów. Następujące wersje publicznego niewielkie opóźnienie (of nie więcej niż kilka tygodni) może być możliwe. Wyszukiwanie i instalowanie wszystkich aktualizacji systemu Windows powinna być pierwszym krokiem przy każdym wdrożeniu. To jest miara jest szczególnie ważne do zastosowania podczas wdrażania obrazów, które pochodzą od Ciebie i własnej biblioteki. Obrazy, które są dostarczane jako część portalu Azure Marketplace są automatycznie aktualizowane domyślnie.

Organizacje, które nie wymuszają zasady aktualizacji oprogramowania więcej są widoczne na zagrożenia, które wykorzystują luki w zabezpieczeniach znane, wcześniej stałym. Oprócz tych zagrożeń, w celu zapewnienia przestrzegania przepisów branżowych ryzyka firmy muszą udowodnić wykonywania starannością i ich za pomocą formantów poprawnych zabezpieczeń do zapewnienia bezpieczeństwa swoje obciążeń znajdujących się w chmurze.

Jest ważne podkreślić, że aktualizacja oprogramowania najlepsze rozwiązania dotyczące tradycyjnych centrów danych i IaaS platformy Azure ma wiele podobieństw. Dlatego zaleca się dokonanie oceny bieżące zasady aktualizacji oprogramowania do uwzględnienia maszyn wirtualnych.

## <a name="manage-your-vm-security-posture"></a>Zarządzanie strukturę bezpieczeństwa maszyny Wirtualnej

Rozwijającymi się zagrożeniami przez i ochrony maszyn wirtualnych wymaga zaawansowanej możliwości monitorowania, które może szybko wykrywać zagrożenia, uniemożliwić nieautoryzowany dostęp do zasobów Wyzwalanie alertów i redukować liczbę fałszywych alarmów. Stan zabezpieczeń dla takich obciążeń obejmuje wszystkie aspekty zabezpieczeń maszyny wirtualnej z zarządzania aktualizacjami w celu zabezpieczenia dostępu do sieci.

Aby monitorować stan zabezpieczeń użytkownika [Windows](../security-center/security-center-virtual-machine.md) i [maszyn wirtualnych systemu Linux](../security-center/security-center-linux-virtual-machine.md), użyj [Centrum zabezpieczeń Azure](../security-center/security-center-intro.md). W Centrum zabezpieczeń Azure ochrony maszyn wirtualnych, korzystając z następujących funkcji:

* Zastosuj ustawienia zabezpieczeń systemu operacyjnego przy użyciu reguł zalecanych konfiguracji
* Identyfikowanie i Pobierz system zabezpieczeń i aktualizacje krytyczne, które mogą być brakujące
* Wdrażanie zaleceń ochrony przed złośliwym kodem punktu końcowego
* Sprawdź poprawność szyfrowania dysków
* Oceniania i korygowania luk w zabezpieczeniach
* Wykrywania zagrożeń

Centrum zabezpieczeń aktywnie można monitorować w przypadku zagrożeń i potencjalne zagrożenia są widoczne w obszarze **alerty zabezpieczeń**. Skorelowane zagrożenia są agregowane w jednym widoku o nazwie **naruszające**.

Aby zrozumieć, jak Centrum zabezpieczeń może ułatwić zidentyfikowanie potencjalnych zagrożeń w maszyn wirtualnych znajdujących się na platformie Azure, obejrzyj film następujące:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player]

Organizacje, które nie wymuszają postawie silne zabezpieczenie ich maszyn wirtualnych pozostają bez "świadomości" potencjalnych prób przez nieautoryzowanych użytkowników do obejścia zabezpieczeń ustalonych.

## <a name="monitor-vm-performance"></a>Monitor wydajności maszyny Wirtualnej

Nadużycia zasobu może to stanowić problem, gdy maszyna wirtualna procesów zużywać więcej zasobów niż powinni. Problemy z wydajnością z maszyny Wirtualnej może prowadzić do przerw w działaniu usługi, która narusza zasady zabezpieczeń dostępności. Z tego powodu konieczne jest nie tylko rozbudowy monitorować dostęp maszyny Wirtualnej, gdy występuje problem, ale również aktywnego względem linii bazowej wydajności mierzony podczas normalnego działania.

Analizując [pliki dzienników diagnostycznych platformy Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), możesz monitorować zasobów maszyny Wirtualnej i zidentyfikuj potencjalne problemy, które mogą negatywnie wpłynąć na wydajność i dostępność. Rozszerzenie diagnostyki Azure udostępnia możliwości monitorowania i diagnostyki na maszynach wirtualnych z systemem Windows. Możesz włączyć te możliwości, wraz z rozszerzeniem jako część [szablonu usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md).

Można również użyć [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) wgląd w kondycję Twojego zasobów.

Nie można ustalić, czy niektóre zmiany we wzorcach wydajności są prawidłowe lub nieprawidłowe są organizacji, które nie monitorować wydajność maszyny Wirtualnej. Jeśli maszyna wirtualna zużywa więcej zasobów niż zwykle, potencjalny atak z zewnętrznego zasobu lub ze złamanymi zabezpieczeniami procesu uruchomionego na maszynie wirtualnej, może to wskazywać takie anomalii.
