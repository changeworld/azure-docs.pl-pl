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
ms.date: 09/18/2018
ms.author: barclayn
ms.openlocfilehash: da165634f5323183b633ee3c8a59e0d2607e8ef1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586532"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Najlepsze rozwiązania dotyczące obciążeń IaaS na platformie Azure

W większości infrastruktury jako scenariuszy usługi (IaaS) [maszyn wirtualnych (VM)](https://docs.microsoft.com/azure/virtual-machines/) są głównym obciążenia dla organizacji, które korzystają z chmury obliczeniowej. Ten fakt jest widoczna w [scenariuszy hybrydowych](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) gdzie organizacji chce powoli migrację obciążeń do chmury. W takich sytuacjach należy wykonać [zabezpieczenia Ogólne zagadnienia dotyczące modelu IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)i Zastosuj najlepsze rozwiązania dotyczące zabezpieczeń na wszystkich maszynach wirtualnych.

Twoja odpowiedzialność za zabezpieczeń zależy od rodzaju usłudze w chmurze. Poniższej tabeli podsumowano saldo odpowiedzialność firmy Microsoft i możesz:

![Zakres odpowiedzialności](./media/azure-security-iaas/sec-cloudstack-new.png)

Wymagania dotyczące zabezpieczeń różnią się w zależności od szeregu czynników, takich jak różne typy obciążeń. Nie w jednym z tych najlepszych rozwiązań przez siebie zabezpieczyć swoje systemy. Podobnie jak niczego więcej w zabezpieczeń musisz wybierz odpowiednie opcje, a następnie zobacz, jak te rozwiązania mogą uzupełniają się wzajemnie, wypełniając luki.

W tym artykule opisano najlepsze rozwiązania dotyczące zabezpieczeń dla maszyn wirtualnych i systemów operacyjnych.

Najlepsze rozwiązania są oparte na konsensus opinii i pracować z aktualnymi możliwościami platformy Azure, a zestawy funkcji. Ponieważ opinie i technologie mogą się zmieniać wraz z upływem czasu, w tym artykule zostaną zaktualizowane w celu odzwierciedlenia tych zmian.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Ochrona maszyn wirtualnych przy użyciu uwierzytelniania i kontroli dostępu
Pierwszym środkiem ochrony maszyn wirtualnych jest, aby upewnić się, że tylko autoryzowani użytkownicy mogą skonfigurować nowych maszyn wirtualnych i dostęp do maszyn wirtualnych.

**Najlepsze rozwiązanie**: Kontrolowanie dostępu do maszyny Wirtualnej.   
**Szczegóły**: Użyj [zasady usługi Azure](../azure-policy/azure-policy-introduction.md) konwencje dla zasobów w Twojej organizacji i tworzenie zasad niestandardowych. Stosowanie tych zasad do zasobów, takich jak [grup zasobów](../azure-resource-manager/resource-group-overview.md). Maszyny wirtualne, które należą do grupy zasobów dziedziczy jej zasady.

Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposób wydajne zarządzanie dostępem, zasad i zgodności dla tych subskrypcji. [Grupy zarządzania systemu Azure](../azure-resource-manager/management-groups-overview.md) zapewniają poziom zakresu powyżej subskrypcji. Organizowanie subskrypcji do grup zarządzania (kontenery) i Zastosuj warunkach nadzoru do tych grup. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki stosowane do grupy. Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji.

**Najlepsze rozwiązanie**: Ogranicz zmiany w konfiguracji i wdrażania maszyn wirtualnych.   
**Szczegóły**: Użyj [usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) szablony wzmocnienie wybrane opcje wdrażania i ułatwiają zrozumienie i tworzenie spisu maszyn wirtualnych w danym środowisku.

**Najlepsze rozwiązanie**: Bezpieczny dostęp uprzywilejowany.   
**Szczegóły**: Użyj [najniższych uprawnień podejście](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) i wbudowanych ról platformy Azure, aby umożliwić użytkownikom dostęp do konfigurowania maszyn wirtualnych:

- [Współautor maszyny wirtualnej](../role-based-access-control/built-in-roles.md#virtual-machine-contributor): Może zarządzać maszynami wirtualnymi, ale nie wirtualnych sieci lub konta magazynu z którym są połączone.
- [Współautor klasycznej maszyny wirtualnej](../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): Można zarządzać maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania, ale nie na wirtualnej sieci i magazynu koncie z którym są połączone maszyny wirtualne.
- [Administrator zabezpieczeń](../role-based-access-control/built-in-roles.md#security-admin): W usłudze Security Center tylko: Można wyświetlić zasady zabezpieczeń, wyświetlanie stanów zabezpieczeń, edytować zasady zabezpieczeń, wyświetlanie alertów i zaleceń, Odrzuć alerty i zalecenia.
- [Użytkownik usługi DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user): Można przeglądać wszystko i połącz, uruchom, uruchom ponownie i zamykają maszyny wirtualne.

Administratorzy subskrypcji i coadmins usługi można zmienić to ustawienie, dzięki czemu administratorzy wszystkich maszyn wirtualnych w ramach subskrypcji. Należy ufać wszystkich Administratorzy subskrypcji i coadmins, aby zalogować się do dowolnej maszyny.

> [!NOTE]
> Firma Microsoft zaleca konsolidować maszyn wirtualnych przy użyciu tego samego cyklu życia w tej samej grupie zasobów. Za pomocą grup zasobów, wdrażanie, monitorowanie oraz rozliczanie kosztów dla zasobów.
>
>

Organizacje, które kontrolują dostęp do maszyny Wirtualnej i konfiguracji zwiększyć ich ogólnego stanu zabezpieczeń maszyn wirtualnych.

## <a name="use-multiple-vms-for-better-availability"></a>Wiele maszyn wirtualnych na użytek lepszej dostępności
Jeśli maszyna wirtualna działa najważniejsze aplikacje, które muszą mieć wysokiej dostępności, firma Microsoft zaleca użycie wielu maszyn wirtualnych. Aby uzyskać lepszą dostępność, należy użyć [zestaw dostępności](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Zestaw dostępności to logiczne grupowanie, które można użyć na platformie Azure, aby upewnić się, że zasoby maszyny Wirtualnej, które można umieścić w niej od siebie odizolowane, gdy zostały wdrożone w centrum danych platformy Azure. Platforma Azure zapewnia maszyn wirtualnych, umieść w dostępności zestawu wykonywania między wieloma serwerami fizycznymi, regałów obliczeniowych, jednostek magazynowych i przełączników sieciowych. Sytuacji sprzętu lub oprogramowania Azure awarii wpływają tylko na część maszyn wirtualnych, a cała aplikacja nadal będzie dostępna dla klientów. Zestawy dostępności stanowią niezbędną funkcję umożliwia tworzenie niezawodnych rozwiązań w chmurze.

## <a name="protect-against-malware"></a>Chroń się przed złośliwym oprogramowaniem
Należy zainstalować ochrony przed złośliwym kodem, aby ułatwić identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania. Możesz zainstalować [Microsoft Antimalware](azure-security-antimalware.md) lub rozwiązanie do ochrony punktu końcowego partnera firmy Microsoft ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Usługi Windows Defender](https://www.microsoft.com/search/result.aspx?q=Windows+defender+endpoint+protection), i [programu System Center Endpoint Protection](https://www.microsoft.com/search/result.aspx?q=System+Center+endpoint+protection)).

Microsoft Antimalware zawiera funkcje, takie jak ochrona w czasie rzeczywistym, zaplanowane skanowanie, korygowaniem złośliwego oprogramowania, aktualizacji sygnatur, aktualizacje aparatu, przykłady raportowania i zbierania zdarzeń wykluczeń. W środowiskach, które znajdują się oddzielnie od środowiska produkcyjnego można użyć rozszerzenia ochrony przed złośliwym kodem można chronić maszyny wirtualne i usługi w chmurze.

Można zintegrować z rozwiązania Microsoft Antimalware i partnerów z [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/) w celu ułatwienia wdrażania i wbudowane funkcje wykrywania (alerty i zdarzenia).

**Najlepsze rozwiązanie**: Zainstaluj rozwiązanie chroniące przed złośliwym kodem do ochrony przed złośliwym oprogramowaniem.   
**Szczegóły**: [Instalowanie rozwiązania partnera firmy Microsoft lub Microsoft Antimalware](../security-center/security-center-install-endpoint-protection.md)

**Najlepsze rozwiązanie**: Zintegruj swoje rozwiązanie chroniące przed złośliwym kodem z usługą Security Center, aby monitorować stan objętego ochroną.   
**Szczegóły**: [Zarządzanie problemy z ochroną punktu końcowego z usługą Security Center](../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Zarządzanie aktualizacjami maszyny Wirtualnej
Maszyny wirtualne platformy Azure, takich jak wszystkie lokalne maszyny wirtualne, mają być zarządzane przez użytkownika. Azure nie wypychanie aktualizacji Windows, do nich. Konieczne jest zarządzanie aktualizacjami maszyny Wirtualnej.

**Najlepsze rozwiązanie**: Aktualność maszyn wirtualnych.   
**Szczegóły**: Użyj [rozwiązania Update Management](../automation/automation-update-management.md) rozwiązania w usłudze Azure Automation do zarządzania operacyjnego aktualizacji systemu na komputerach, które zostały wdrożone na platformie Azure, Windows i Linux w środowiskach lokalnych lub w innych dostawców chmury. Umożliwia ono szybką ocenę stanu dostępnych aktualizacji na wszystkich komputerach agentów oraz zarządzanie procesem instalacji wymaganych aktualizacji serwerów.

Komputery, które są zarządzane przez zarządzania aktualizacjami do oceny i wdrożenia aktualizacji, użyj następujących konfiguracji:

- Microsoft Monitoring Agent (MMA), Windows i Linux
- Platforma PowerShell Desired State Configuration (DSC) dla systemu Linux
- Hybrydowy proces roboczy elementu runbook usługi Automation
- Microsoft Update lub Windows Server Update Services (WSUS) dla komputerów Windows

Korzystając z witryny Windows Update, pozostaw ustawienie automatyczne Windows Update włączone.

**Najlepsze rozwiązanie**: Upewnij się, podczas wdrażania, obrazy, tworzonej obejmują okrągłej najnowszych aktualizacji Windows.   
**Szczegóły**: Sprawdź i zainstaluj wszystkie aktualizacje Windows pierwszym krokiem w każdym wdrożeniu. Ta miara jest szczególnie ważne zastosować w przypadku wdrażania obrazów, które pochodzą od Ciebie i Twojej własnej biblioteki. Mimo, że obrazy z witryny Azure Marketplace są automatycznie aktualizowane domyślnie, istnieje może wystąpić opóźnienie (maksymalnie kilka tygodni) po publicznym udostępnieniu.

**Najlepsze rozwiązanie**: Okresowe ponowne wdrażanie maszyn wirtualnych, aby wymusić świeży wersję systemu operacyjnego.   
**Szczegóły**: Definiowanie przy użyciu [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) , dzięki czemu można łatwo wdrożyć ponownie go. Przy użyciu szablonu zapewnia poprawionego i bezpieczna maszyna wirtualna gdy ich potrzebujesz.

**Najlepsze rozwiązanie**: Zainstaluj najnowsze aktualizacje zabezpieczeń.   
**Szczegóły**: Niektóre z pierwszych obciążeń, które klientom przenoszenie na platformę Azure są laboratoria i systemy dołączonej do Internetu. Jeśli na maszynach wirtualnych platformy Azure hostowanie aplikacji lub usług, które mają być dostępne w Internecie, być czujność dotyczących poprawiania. Poprawki poza działanie systemu operacyjnego. Bez luk w zabezpieczeniach w aplikacjach partnerów również może prowadzić do problemów, które można uniknąć, jeśli zarządzanie poprawkami dobre znajduje się w miejscu.

**Najlepsze rozwiązanie**: Wdrażanie i testowanie rozwiązania tworzenia kopii zapasowych.   
**Szczegóły**: Kopię zapasową musi być obsługiwane w taki sam sposób obsługi innych operacji. Dotyczy to systemów, które są częścią środowiska produkcyjnego, rozszerzając w chmurze.

Systemy testowych i deweloperskich, należy wykonać strategii tworzenia kopii zapasowych, które zapewniają możliwości przywracania, które są podobne do użytkownicy wzrosła przyzwyczajeni, na podstawie ich użycia w środowiskach lokalnych. Obciążenia produkcyjne przeniesione do platformy Azure należy zintegrować z istniejącymi rozwiązaniami kopii zapasowej, jeśli jest to możliwe. Lub możesz użyć [kopia zapasowa Azure](../backup/backup-azure-vms-first-look-arm.md) do rozwiązywania wymogami dotyczącymi kopii zapasowej.

Organizacje, które nie wymuszają zasady aktualizacji oprogramowania są bardziej widoczne zagrożeniami wykorzystującymi luki w zabezpieczeniach znane, wcześniej stały. Do zapewnienia zgodności z przepisami w branży, firmy muszą potwierdzić, że są one skrupulatne poprawianie listy i za pomocą formantów poprawnych zabezpieczeń do zapewnienia bezpieczeństwa ich obciążeń znajdujących się w chmurze.

Najlepsze rozwiązania dotyczące tradycyjnych centrów danych aktualizacji oprogramowania i IaaS platformy Azure mają wiele podobieństw. Firma Microsoft zaleca, aby ocenić swoje bieżące zasady aktualizacji oprogramowania do uwzględnienia maszyn wirtualnych znajdujących się na platformie Azure.

## <a name="manage-your-vm-security-posture"></a>Zarządzanie poziomu bezpieczeństwa maszyny Wirtualnej
Cyberzagrożeniami wciąż ewoluuje. Ochrona maszyn wirtualnych wymaga możliwości monitorowania, która może szybko wykrywać zagrożenia, Zapobieganie nieautoryzowanemu dostępowi do zasobów, wyzwalać alerty i redukować liczbę fałszywych alarmów.

Aby monitorować stan zabezpieczeń swojej [Windows](../security-center/security-center-virtual-machine.md) i [maszyn wirtualnych systemu Linux](../security-center/security-center-linux-virtual-machine.md), użyj [usługi Azure Security Center](../security-center/security-center-intro.md). W usłudze Security Center chronić maszyny wirtualne dzięki wykorzystaniu następujące możliwości:

- Zastosuj ustawienia zabezpieczeń systemu operacyjnego z zalecanymi regułami konfiguracji.
- Identyfikuj i Pobierz system zabezpieczeń i aktualizacje krytyczne, które może brakować.
- Wdróż zalecenia dotyczące programu endpoint protection ochrony przed złośliwym oprogramowaniem.
- Weryfikowanie szyfrowania dysków.
- Oceń i korygowanie luk w zabezpieczeniach.
- Wykrywanie zagrożeń.

Usługa Security Center może aktywnie monitorować pod kątem zagrożeń i potencjalnych zagrożeń są widoczne w alertach zabezpieczeń. Skorelowane zagrożeń są agregowane w jednym widoku, który wywołuje zdarzenie naruszenia zabezpieczeń.

Usługa Security Center przechowuje dane w [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md). Dzienniki platformy Azure Monitor udostępnia zapytań języka i aparat analityczny, który zapewnia wgląd w działania aplikacji i zasobów. Dane są również zbierane z [usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md), rozwiązania do zarządzania i agenci zainstalowani na maszynach wirtualnych w chmurze lub lokalnie. Ta wspólna funkcjonalność pomaga utworzyć pełny obraz środowiska.

Organizacje, które nie wymuszają silne zabezpieczenia maszyn wirtualnych pozostają niebranie pod uwagę potencjalny prób przez nieautoryzowanych użytkowników do obejścia kontroli zabezpieczeń.

## <a name="monitor-vm-performance"></a>Monitorowanie wydajności maszyny Wirtualnej
W przypadku maszyny Wirtualnej procesy zużywać więcej zasobów niż powinni nadużycie zasobu może to stanowić problem. Problemy z wydajnością za pomocą maszyny Wirtualnej może prowadzić do przerwy w działaniu usługi, która narusza zasady zabezpieczeń dostępności. Jest to szczególnie ważne w przypadku maszyn wirtualnych, które hostują program IIS lub innych serwerów sieci web, ponieważ wysokie użycie procesora CPU lub pamięci może wskazywać typu "odmowa usługi (DoS)". Należy bezwzględnie monitorować dostęp do maszyny Wirtualnej nie tylko sposób reaktywny, gdy problem, ale również proaktywnie względem punktu odniesienia wydajności mierzonych podczas normalnego działania.

Firma Microsoft zaleca użycie [usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) uzyskać wgląd w kondycję zasobu. Funkcje monitorowania platformy Azure:

- [Pliki dzienników diagnostycznych zasobów](../azure-monitor/platform/diagnostic-logs-overview.md): Monitorowanie zasobów maszyny Wirtualnej i identyfikuje potencjalnych problemów, które mogą negatywnie wpłynąć na wydajność i dostępność.
- [Rozszerzenie diagnostyki platformy Azure](../azure-monitor/platform/diagnostics-extension-overview.md): Zapewnia możliwości monitorowania i diagnostyki na maszynach wirtualnych Windows. Możesz włączyć te możliwości, łącznie z rozszerzeniem jako część [szablonu usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md).

Organizacje, które nie monitorować wydajność maszyny Wirtualnej nie może określić, czy pewne zmiany we wzorcach wydajności są prawidłowe lub nieprawidłowe. Maszynę Wirtualną, która zużywa więcej zasobów niż zwykle może wskazywać na atak z zewnętrznego zasobu lub ze złamanymi zabezpieczeniami procesu uruchomionego na maszynie wirtualnej.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Szyfrowanie plików wirtualnego dysku twardego
Firma Microsoft zaleca szyfrowanie wirtualne dyski twarde (VHD), aby pomóc w ochronie wolumin rozruchowy i ilości danych przechowywanych w magazynie, wraz z wpisami tajnymi i kluczami szyfrowania, na których.

[Usługa Azure Disk Encryption](azure-security-disk-encryption-overview.md) umożliwia szyfrowanie dysków maszyn wirtualnych IaaS systemu Linux i Windows. Usługa Azure Disk Encryption korzysta będące standardami branżowymi [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkcja systemu Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. To rozwiązanie jest zintegrowana z usługą [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiają kontrolowanie i zarządzanie kluczami szyfrowania dysku i wpisami tajnymi w ramach subskrypcji usługi key vault. To rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w usłudze Azure Storage.

Poniżej przedstawiono najlepsze rozwiązania dotyczące korzystania z usługi Azure Disk Encryption:

**Najlepsze rozwiązanie**: Włącz szyfrowanie na maszynach wirtualnych.   
**Szczegóły**: Usługa Azure Disk Encryption generuje i zapisuje klucze szyfrowania w magazynie kluczy. Zarządzanie kluczami szyfrowania w magazynie kluczy wymaga uwierzytelniania usługi Azure AD. Utwórz aplikację usługi Azure AD, w tym celu. Na potrzeby uwierzytelniania, można użyć albo uwierzytelnianie oparte na klucz tajny klienta lub [uwierzytelnianie klienta oparte na certyfikatach usługi Azure AD](../active-directory/active-directory-certificate-based-authentication-get-started.md).

**Najlepsze rozwiązanie**: Klucz szyfrowania klucza (KEK) na użytek dodatkową warstwę zabezpieczeń dla kluczy szyfrowania. Dodaj KEK do magazynu kluczy.   
**Szczegóły**: Użyj [AzKeyVaultKey Dodaj](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) polecenia cmdlet, aby utworzyć klucz szyfrowania klucza w magazynie kluczy. Można również zaimportować KEK z Twojego lokalnego sprzętowego modułu zabezpieczeń (HSM) do zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [dokumentacja usługi Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Jeśli klucz szyfrowania jest określony, usługi Azure Disk Encryption używa tego klucza do opakowania wpisów tajnych szyfrowania przed zapisaniem w usłudze Key Vault. Utrzymywanie depozytu kopię tego klucza w lokalnym programie zarządzania kluczami przez moduł HSM oferuje dodatkową ochronę przed przypadkowym usunięciem kluczy.

**Najlepsze rozwiązanie**: Wykonaj [migawki](../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub utworzyć kopię zapasową przed dyski są szyfrowane. Kopie zapasowe podaj opcję odzyskiwania, jeśli wystąpił nieoczekiwany błąd będzie się działo podczas szyfrowania.   
**Szczegóły**: Maszyny wirtualne z dyskami zarządzanymi wymagają kopię zapasową przed zaszyfrowaniem. Po utworzeniu kopii zapasowej można użyć **AzVMDiskEncryptionExtension zestaw** polecenia cmdlet, aby zaszyfrować dyski zarządzane, określając *- skipVmBackup* parametru. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych, zobacz [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) artykułu.

**Najlepsze rozwiązanie**: Aby upewnić się, że klucze tajne szyfrowania nie przekracza granic regionalne, usługa Azure Disk Encryption wymaga magazynu kluczy i maszyny wirtualne muszą znajdować się w tym samym regionie.   
**Szczegóły**: Tworzenie i używanie magazynu kluczy, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania.

Po zastosowaniu Azure Disk Encryption może spełnić następujące wymagania biznesowe:

- Maszyny wirtualne IaaS są chronione za pomocą standardowych w branży technologii umożliwiającą organizacyjnych wymagań dotyczących zabezpieczeń i zgodności.
- Maszyny wirtualne IaaS Rozpocznij w obszarze kontrolowane przez klienta kluczy i zasad, a można przeprowadzać ich inspekcje użycia ich w magazynie kluczy.

## <a name="next-steps"></a>Kolejne kroki
Zobacz [zabezpieczeń platformy Azure najlepsze rozwiązania i wzorce](security-best-practices-and-patterns.md) dla więcej najważniejsze wskazówki dotyczące zabezpieczeń do użycia podczas one projektowanie, wdrażanie i zarządzanie rozwiązań w chmurze, korzystając z platformy Azure.

Do dyspozycji więcej ogólnych informacji na temat zabezpieczeń platformy Azure i powiązane usługi firmy Microsoft są następujące zasoby:
* [Blog zespołu usługi Azure Security](https://blogs.msdn.microsoft.com/azuresecurity/) — aktualne instrukcje dotyczące najnowszych zabezpieczeń platformy Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — w przypadku, gdy luk w zabezpieczeniach firmy Microsoft, w tym problemów z platformą Azure, mogą zostać zgłoszone lub za pośrednictwem poczty e-mail do secure@microsoft.com
