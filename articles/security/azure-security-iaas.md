---
title: Zabezpieczenia najlepszych rozwiązań IaaS obciążeń na platformie Azure | Dokumentacja firmy Microsoft
description: " Migracji obciążeń do IaaS platformy Azure oferuje możliwości, aby obliczyć ponownie nasze projekty "
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
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 1a6ff01274c4a47730ffe45275aed9d122994260
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Najlepsze rozwiązania dotyczące IaaS obciążeń na platformie Azure

Rozpocznie się zastanawiać przenoszenie obciążeń do infrastruktury platformy Azure jako usługa (IaaS), możesz prawdopodobnie zrealizowane zapoznali pewne zagadnienia. Środowisko zabezpieczanie środowisk wirtualnych mogą już istnieć. Po przeniesieniu do IaaS platformy Azure, możesz się swoją wiedzą w zabezpieczanie środowisk wirtualnych i użyj nowy zestaw opcji, aby ułatwić zabezpieczanie zasobów.

Zacznijmy od informujący o tym, że powinien nie oczekujemy, można wyświetlić lokalnych zasobów jako jeden do jednego na platformie Azure. Nowe opcje i wyzwania Przełącz możliwość obliczyć ponownie istniejących deigns, narzędzi i procesów.

Twoje odpowiedzialność za bezpieczeństwo jest oparty na typie usługi w chmurze. Poniżej przedstawiono saldo odpowiedzialność firmy Microsoft i można:


![Zakres odpowiedzialności](./media/azure-security-iaas/sec-cloudstack-new.png)


Omówiono niektóre opcje dostępnej na platformie Azure, które pomaga spełnić wymagania dotyczące zabezpieczeń w organizacji. Należy pamiętać, że wymagania dotyczące zabezpieczeń mogą być różne w różnych rodzajów obciążeń. Nie jest elementem następujące najlepsze rozwiązania samodzielnie zabezpieczyć systemy. Jak żadnych innych elementów zabezpieczeń należy wybrać odpowiednie opcje i zobacz, jak te rozwiązania mogą uzupełniają przez wypełniania luk.

## <a name="use-privileged-access-workstations"></a>Użyj stacje robocze uprzywilejowanego dostępu

Organizacje często wchodzą żerują do cyberattacks ponieważ administratorom wykonywać akcji przy użyciu konta z podwyższonym poziomem uprawnień. Zazwyczaj nie jest to złośliwy, ale ponieważ zezwala na to istniejącej konfiguracji i procesów. Większość tych użytkowników świadomość ryzyka tych działań z punktu widzenia koncepcyjnej, ale nadal wybierz ich wykonywania.

Wykonywanie czynności, takie jak sprawdzanie poczty e-mail i przeglądania Internetu wydaje się wystarczająco nieszkodliwie. Ale one może narazić kont z podwyższonym poziomem uprawnień na naruszenia przez złośliwych osób. Przeglądanie działań, specjalnie przygotowany wiadomości e-mail lub innych technik może służyć do uzyskiwania dostępu do Twojej organizacji. Zdecydowanie zaleca się użycie bezpiecznego zarządzania stacje robocze (piły) przeprowadzania wszystkich zadań administracyjnych usługi Azure. Piły służą ograniczyć ryzyko przypadkowego naruszenia zabezpieczeń.

Uprzywilejowany dostęp do stacji roboczych (łapy) zawierają dedykowanego systemu operacyjnego poufnych zadań —, który jest chroniony z Internetu ataków i zagrożeń wektory. Oddzielanie tych ważnych zadań i konta z urządzeń i stacji roboczych używanych codziennie udostępnia silną ochronę. Ta separacja ogranicza wpływ wyłudzania, aplikacji i luk w zabezpieczeniach systemu operacyjnego, ataków na różnych personifikacji i ataków kradzieży poświadczeń. (rejestrowanie, Pass--Hash i Pass--Ticket klawiszy)

Podejście ŁAPY jest rozszerzeniem ustalonym i zalecane praktyki indywidualnie przypisane konta administracyjnego. Konto administracyjne jest oddzielony od standardowego konta użytkownika. ŁAPY udostępnia zaufanego stacji roboczej dla tych kont poufnych.

Aby uzyskać więcej informacji i implementacji pomocy, zobacz [uprzywilejowanego dostępu stacje robocze](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

W przeszłości w Twojej sieci obwodowej był używany do kontrolowania dostępu do danych firmowych. W środowisku chmury — pierwszy, najpierw mobile tożsamość jest płaszczyzny sterowania: służy do kontrolowania dostępu do usługi IaaS z dowolnego urządzenia. Możesz również Użyj, aby uzyskać wgląd i wgląd w jak i gdzie danych jest używany. Ochrona tożsamości cyfrowych użytkowników platformy Azure jest podstawą ochrony subskrypcji przed kradzieżą tożsamości i innych cybercrimes.

Jedną z najbardziej przydatne czynności, które należy wykonać, aby zabezpieczyć konto jest włączenie uwierzytelniania dwuskładnikowego. Uwierzytelnianie dwuskładnikowe jest sposób uwierzytelniania przy użyciu elementu oprócz hasła. Pomaga ograniczyć ryzyko dostępu przez osobę, która zarządza uzyskanie hasła do kogoś innego.

[Usługa Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) pomaga w zabezpieczaniu dostępu do danych i aplikacji spełniając zapotrzebowanie na prosty proces logowania. Zapewnia silne uwierzytelnianie za pomocą różnych opcji weryfikacji łatwe — połączenie telefoniczne, wiadomość tekstowa lub powiadomienie aplikacji mobilnej. Użytkownicy wybierają metodę preferowany.

Najprostszym sposobem użycia usługi Multi-Factor Authentication jest aplikacji mobilnej Authenticator firmy Microsoft można używać na urządzeniach przenośnych z systemem Windows, iOS i Android. Najnowsza wersja systemu Windows 10 i integracji lokalnej usługi Active Directory z usługą Azure Active Directory (Azure AD) [Windows Hello dla firm](../active-directory/active-directory-azureadjoin-passport-deployment.md) może służyć do łatwego logowanie jednokrotne do zasobów platformy Azure. W takim przypadku urządzenia z systemem Windows 10 jest używany jako drugi składnik uwierzytelniania.

Dla konta, które zarządzają subskrypcją platformy Azure i kont, które można zalogować się do maszyn wirtualnych przy użyciu usługi Multi-Factor Authentication zapewnia znacznie wyższy poziom zabezpieczeń niż używanie tylko hasła. Inne formy uwierzytelniania dwuskładnikowego może działać równie dobrze, ale ich wdrożeniem może być skomplikowane, jeśli tak nie jest już w środowisku produkcyjnym.

Poniższy zrzut ekranu przedstawia niektóre z dostępnych opcji uwierzytelnianie wieloskładnikowe Azure:

![Opcje uwierzytelniania wieloskładnikowego](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Ograniczenia i ograniczenie dostępu administracyjnego

Bardzo ważne jest zabezpieczenie kont, które mogą zarządzać subskrypcją platformy Azure. Naruszenie któregokolwiek z tych kont Negacja wartości wszystkich kroków, które może wykonać, aby zapewnić poufność i integralność danych. Ostatnio ilustrowane przez [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) atakami wewnętrznymi stanowiących zagrożenie ogromnych na ogólne bezpieczeństwo każdej organizacji.

W celu oceny osoby do prawa administracyjne podobne do następujących następujące kryteria:

- Są one wykonywanie zadań, które wymagają uprawnień administratora?
- Jak często wykonywane zadania
- Czy istnieje powód, dlaczego nie można wykonać zadania przez innego administratora w ich imieniu?

Dokument wszystkich innych znanych alternatywnych metod do przyznawania uprawnień i każdego Dlaczego dopuszczalne.

Użycie administracji just in time zapobiega niepotrzebnych istnieją konta z podwyższonym poziomem uprawnień w okresach, gdy nie są wymagane te prawa. Konta podniesionymi uprawnieniami przez ograniczony czas, aby administratorzy można wykonać swoje zadania. Następnie te prawa są usuwane po zakończeniu zmiany lub po zakończeniu zadania.

Można użyć [Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) do zarządzania, monitorowania i kontroli dostępu w organizacji. Pomaga pamiętać o akcje wykonywane przez osoby w Twojej organizacji. Stwarza również just in time administracji do usługi Azure AD dzięki zastosowaniu pojęcie kwalifikujących się Administratorzy. Są to osób, które mają konta, z których można udzielić uprawnień administratora. Te typy użytkowników, należy udzielić uprawnień administratora przez ograniczony czas i można przejść za pośrednictwem procesu aktywacji.


## <a name="use-devtest-labs"></a>Użyj DevTest Labs

Laboratoria i środowisk deweloperskich przy użyciu usługi Azure umożliwia organizacjom uzyskać elastyczność w badania i rozwój wykonując optymalizacji opóźnień, które wprowadza nabywania sprzętu. Niestety Brak znajomość Azure lub chęć pomocy przyspieszenia przyjęciu może prowadzić administratorowi być nadmiernie ograniczająca z Przypisywanie praw. To zagrożenie przypadkowo może narazić organizacji atakami wewnętrznymi. Niektórzy użytkownicy mogą otrzymać znacznie szerszy dostęp niż powinny mieć.

[Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) usługi używa [kontroli dostępu](../role-based-access-control/overview.md) (RBAC). Za pomocą RBAC, można rozdzielenie obowiązków w obrębie organizacji do ról, które udzielić tylko poziom dostępu, które są niezbędne dla użytkowników do wykonywania zadań. RBAC zawiera wstępnie zdefiniowane role (właściciel, użytkownik laboratorium i współautor). Te role umożliwia nawet przypisać prawa do partnerami zewnętrznymi i znacznie upraszcza współpracy.

Ponieważ DevTest Labs używa RBAC, istnieje możliwość utworzenia dodatkowych, [role niestandardowe](../lab-services/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs nie tylko ułatwia zarządzanie uprawnienia, takie rozwiązanie upraszcza proces konfigurowania środowiska obsługi administracyjnej. Pomaga również postępowania w przypadku innych typowych wyzwań związanych z zespołów, które pracują na środowisk projektowania i testowania. Wymaga on pewne przygotowania, ale w długim okresie, jego będzie ułatwienia dla zespołu.

Azure DevTest Labs funkcje:

- Kontrolę administracyjną nad opcje dostępne dla użytkowników. Dozwolone rozmiary maszyn wirtualnych, maksymalna liczba maszyn wirtualnych i rozpoczęcie maszyn wirtualnych i zamykania centralnie może zarządzać administrator.
- Automatyzacja tworzenia środowiska laboratorium.
- Śledzenie kosztów.
- Uproszczony dystrybucji maszyn wirtualnych do tymczasowej pracy współpracy.
- Samoobsługi, która umożliwia użytkownikom udostępnianie ich labs przy użyciu szablonów.
- Ograniczanie zużycia i zarządzanie.

![Tworzenie laboratorium przy użyciu DevTest Labs](./media/azure-security-iaas/devtestlabs.png)

Bez dodatkowych kosztów jest skojarzona z użycia DevTest Labs. Tworzenie labs, zasady, szablonów i artefakty jest bezpłatna. Płaci się tylko zasobów Azure używane w laboratorium, na przykład maszyny wirtualne, konta magazynu i sieci wirtualnych.



## <a name="control-and-limit-endpoint-access"></a>Limit i kontroli dostępu do punktu końcowego

Hosting labs lub systemów produkcyjnych na platformie Azure oznacza, że systemy muszą być dostępne z Internetu. Domyślnie nowej maszyny wirtualnej systemu Windows ma numer portu protokołu RDP z Internetu, a maszyny wirtualnej systemu Linux ma otworzyć port SSH. Pobieranie kroki do punktów końcowych limit widoczne jest niezbędne ograniczyć ryzyko nieautoryzowanego dostępu.

Technologie na platformie Azure mogą pomóc ograniczyć dostęp do tych administracyjne punktów końcowych. Na platformie Azure, można użyć [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md) (NSG). Gdy używasz usługi Azure Resource Manager do wdrożenia grup NSG ograniczyć dostęp ze wszystkich sieci do właśnie zarządzania punktów końcowych (RDP lub SSH). Jeśli uważasz, że grupy NSG, wziąć pod uwagę router listy kontroli dostępu. Ścisła kontrola komunikację sieciową między różnymi segmentami sieci platformy Azure można używać ich. Jest to podobne do tworzenia sieci w sieci obwodowej lub innych sieci izolowanej. Nie kontrolują ruch, ale pomagają segmentacji sieci.


Na platformie Azure, można skonfigurować [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) z sieci lokalnej. Sieć VPN lokacja lokacja rozszerza sieci lokalnych do chmury. Umożliwia innym możliwość użycia grup NSG, ponieważ można również zmodyfikować grupy NSG można nie zezwolić na dostęp z dowolnego miejsca innych niż sieci lokalnej. Następnie można wymagać, aby zarządzanie odbywa się przez nawiązanie sieć platformy Azure za pośrednictwem sieci VPN.

Opcja VPN lokacja lokacja może być najbardziej atrakcyjne w przypadku których prowadzą hosting systemów produkcyjnych, które są ściśle zintegrowane z lokalnymi zasobami na platformie Azure.

Alternatywnie można użyć [punkt lokacja](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) opcji w sytuacji, gdy do zarządzania komputerami, które nie wymagają dostępu do zasobów lokalnych. Te systemy można samodzielnie w ich własnych sieci wirtualnych platformy Azure. Administratorzy mogą sieci VPN do platformy Azure hostowanej środowisko z ich administracyjnej stacji roboczej.

>[!NOTE]
>Jedną z opcji VPN umożliwia ponownie skonfigurować listy ACL na grup NSG do nie zezwalają na dostęp do punktów końcowych zarządzania z Internetu.

Inną opcją warto rozważane jest [bramy usług pulpitu zdalnego](../active-directory/authentication/howto-mfaserver-nps-rdg.md) wdrożenia. To wdrożenie służy do bezpiecznego łączenia się z serwerami usług pulpitu zdalnego za pośrednictwem protokołu HTTPS, podczas stosowania bardziej szczegółowe kontrolek do tych połączeń.

Funkcje, które będzie mieć dostęp do uwzględnienia:

- Opcje administratora, aby ograniczyć liczbę połączeń na żądania z konkretnych systemów.
- Uwierzytelnianie karty inteligentnej lub uwierzytelnianie wieloskładnikowe Azure.
- Kontrola nad systemach, w których ktoś może nawiązać połączenie za pośrednictwem bramy.
- Kontrolę nad urządzeniami i dysk przekierowania.

## <a name="use-a-key-management-solution"></a>Za pomocą rozwiązania zarządzania kluczami

Bezpieczne zarządzanie kluczami jest podstawą ochrony danych w chmurze. Z [usługi Azure Key Vault](../key-vault/key-vault-whatis.md), można bezpiecznie przechowywać kluczy szyfrowania i kluczy tajnych w małych, takich jak hasła w sprzętowych modułach zabezpieczeń (HSM). W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w modułach HSM.

Procesy Microsoft klucze w trybie FIPS 140-2 poziom 2 HSM zweryfikowanych (sprzęt i oprogramowanie układowe). Monitor i inspekcji użycie kluczy z rejestrowaniem Azure: potoku dzienniki systemu Azure lub Security Information and Event Management (SIEM) dla dodatkowych analizy i wykrywania zagrożeń.

Każdy użytkownik z subskrypcją platformy Azure można tworzyć i używać magazynów kluczy. Mimo że Key Vault przynosi korzyści deweloperom i administratorom zabezpieczeń, można wdrożone i zarządzane przez administratora, który jest odpowiedzialny za zarządzanie usługami Azure w organizacji.


## <a name="encrypt-virtual-disks-and-disk-storage"></a>Szyfrowanie dysków wirtualnych i magazynu danych na dysku

[Szyfrowanie dysków Azure](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) adresy zagrożeń kradzieżą lub ujawnieniem przed nieautoryzowanym dostępem, że jest to osiągane przez przeniesienie dysku danych. Dysk może zostać dołączona do innego systemu sposób pomijanie inne kontrole dotyczące zabezpieczenia. Dysk używa szyfrowania [funkcji BitLocker](https://technet.microsoft.com/library/hh831713) w systemach Windows i DM-Crypt w systemie Linux, aby zaszyfrować systemu operacyjnego i dysków z danymi. Szyfrowanie dysków Azure integruje się z Key Vault w celu kontrolowania i zarządzać kluczami szyfrowania. Jest ona dostępna dla standardowych maszyn wirtualnych i maszyn wirtualnych z magazyn w warstwie premium.

Aby uzyskać więcej informacji, zobacz [szyfrowania dysków Azure w systemie Windows i maszyn wirtualnych systemu Linux IaaS](azure-security-disk-encryption.md).

[Szyfrowanie usługi Magazyn Azure](../storage/common/storage-service-encryption.md) pomaga chronić dane przechowywane. Jest ona włączona na poziomie konta magazynu. Ponieważ jest ona zapisywana w naszych centrach danych, które są automatycznie odszyfrowywane, tylko uzyskujesz dostęp do, szyfruje dane. Obsługuje następujące scenariusze:

- Dołącz szyfrowania blokowych obiektów blob, obiekty BLOB i stronicowe obiekty BLOB
- Szyfrowanie zarchiwizowane szablony przeniesiony do platformy Azure z lokalnymi i wirtualne dyski twarde
- Szyfrowanie podstawowego systemu operacyjnego i dysków z danymi dla maszyn wirtualnych IaaS, utworzony za pomocą sieci wirtualne dyski twarde

Przed przystąpieniem do szyfrowania magazynu Azure, należy pamiętać o dwa ograniczenia:

- Nie jest dostępny na klasycznych kont magazynu.
- Koduje tylko danych zapisane po włączeniu szyfrowania.

## <a name="use-a-centralized-security-management-system"></a>Używany system zarządzania scentralizowanych zabezpieczeń

Serwery muszą być monitorowane przez stosowanie poprawek, konfiguracji, zdarzeń i działania, które może zostać uznane za bezpieczeństwo. Aby rozwiązać te problemy, można użyć [Centrum zabezpieczeń](https://azure.microsoft.com/services/security-center/) i [Operations Management Suite zabezpieczeń i zgodności](https://azure.microsoft.com/services/security-center/). Obie te opcje wykracza poza konfiguracji w systemie operacyjnym. Zapewniają także monitorowanie konfiguracji podstawowej infrastruktury, takich jak konfiguracji sieci i używania urządzenia wirtualnego.

## <a name="manage-operating-systems"></a>Zarządzanie systemami operacyjnymi

W przypadku wdrożenia IaaS jesteś nadal odpowiedzialni za zarządzanie systemów, które można wdrożyć, podobnie jak inne serwery lub stacji roboczej w środowisku. Stosowanie poprawek, ograniczenia funkcjonalności przypisywania praw i innych działań związanych z konserwacji systemu nadal są z odpowiedzialności. Dla systemów, które są ściśle powiązane z zasobami lokalnymi można użyć tego samego narzędzia i procedury, że używasz lokalnych elementów, jak oprogramowanie antywirusowe, ochrony przed złośliwym kodem, poprawki i kopii zapasowej.

### <a name="harden-systems"></a>Ograniczenia funkcjonalności systemów
Wszystkie maszyny wirtualne Azure IaaS powinien wzmocnione zabezpieczenia, dzięki czemu udostępniają tylko usługi punktów końcowych, które są wymagane dla aplikacji, które są zainstalowane. Dla maszyn wirtualnych systemu Windows, należy postępować zgodnie z zaleceniami, przez firmę Microsoft jako planów bazowych dla [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) rozwiązania.

Menedżer zgodności zabezpieczeń to bezpłatne narzędzie. Można ją szybko skonfigurować i Zarządzanie pulpitami, tradycyjnych centrów danych i chmury prywatnej i publicznej za pomocą zasad grupy i System Center Configuration Manager.

Menedżer zgodności zabezpieczeń zawiera gotowe do wdrożenia zasad i zarządzania żądaną konfiguracją pakiety konfiguracyjne, które są sprawdzane pod. Te plany bazowe są oparte na [wskazówki dotyczące zabezpieczeń Microsoft](https://technet.microsoft.com/library/cc184906.aspx) zaleceń oraz z branży najlepsze rozwiązania. Pomagają odejście konfiguracji, adres wymagania dotyczące zgodności i zmniejsza zagrożenia bezpieczeństwa.

Menedżer zgodności zabezpieczeń służy do importowania bieżącej konfiguracji komputerów za pomocą dwóch różnych metod. Po pierwsze można zaimportować zasad grupy opartych na usłudze Active Directory. Po drugie, można zaimportować konfigurację "wzorcowe" komputera odniesienia przy użyciu [LocalGPO narzędzia](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) do tworzenia kopii zapasowych lokalnych zasadach grupy. Następnie można zaimportować zasad grupy lokalnej do Menedżera zgodności zabezpieczeń.

Porównanie standardy do najlepsze rozwiązania w branży, dostosuj je i Utwórz nowe zasady i pakiety konfiguracji zarządzania żądaną konfiguracją. Linie bazowe zostały opublikowane dla wszystkich obsługiwanych systemów operacyjnych, w tym Windows 10 Anniversary Update i Windows Server 2016.


### <a name="install-and-manage-antimalware"></a>Zainstaluj i Zarządzaj ochrony przed złośliwym oprogramowaniem

W środowiskach hostowanych oddzielnie od środowiska produkcyjnego można użyć rozszerzenia ochrony przed złośliwym kodem można chronić maszyny wirtualne i usługi w chmurze. Integruje się z [Centrum zabezpieczeń Azure](../security-center/security-center-intro.md).


[Microsoft Antimalware](azure-security-antimalware.md) obejmuje funkcje, takie jak ochrona w czasie rzeczywistym, zaplanowane skanowanie, korygowaniem złośliwego oprogramowania, aktualizacji podpisu, aktualizacji aparatu i przykłady raportowania zbierania zdarzeń wykluczeń, i [ObsługaprogramuPowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Azure ochrony przed złośliwym oprogramowaniem](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Zainstaluj najnowsze aktualizacje zabezpieczeń
Pierwszy obciążeń, które klienci przenieść Azure należą labs i systemy dołączonej do Internetu. W przypadku maszyn wirtualnych hostowanych w usłudze Azure obsługuje aplikacje lub usługi, które muszą być dostępne z Internetem, można czujność o stosowanie poprawek. Poprawka poza działanie systemu operacyjnego. Które luk w zabezpieczeniach w aplikacjach innych firm również może prowadzić do problemów, które można uniknąć, jeśli zarządzanie poprawkami dobrej znajduje się w miejscu.

### <a name="deploy-and-test-a-backup-solution"></a>Wdrażanie i testowanie kopii zapasowej rozwiązania

Podobnie jak aktualizacje zabezpieczeń kopii zapasowej musi zostać obsłużone taki sam sposób obsługi przez inną operację. Dotyczy to systemów, które są częścią środowiska produkcyjnego, rozszerzanie do chmury. Systemy testu i deweloperów wykonaj strategii tworzenia kopii zapasowych, które zapewniają możliwości przywracania, które są podobne do użytkownicy przekroczył przyzwyczajony do, na podstawie ich doświadczeń w środowiskach lokalnych.

Obciążeń produkcyjnych przeniesione do platformy Azure należy zintegrować z istniejącymi rozwiązaniami tworzenia kopii zapasowej, jeśli to możliwe. Możesz też użyć [kopia zapasowa Azure](../backup/backup-azure-arm-vms.md) mogą pomóc w pokonywaniu wymagania dotyczące tworzenia kopii zapasowej.


## <a name="monitor"></a>Monitorowanie

[Centrum zabezpieczeń](../security-center/security-center-intro.md) zapewnia ciągłej oceny stanu zabezpieczeń zasobów platformy Azure do identyfikowania potencjalnych luk w zabezpieczeniach. Lista zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych elementów sterujących.

Przykłady:

- Inicjowanie obsługi ochrony przed złośliwym kodem do identyfikacji i usuwania złośliwego oprogramowania.
- Konfigurowanie grup zabezpieczeń sieci i reguł sterujących ruchem do maszyn wirtualnych.
- Inicjowanie zapór aplikacji sieci web pomagających chronić przed atakami kierowanych obsługi aplikacji sieci web.
- Wdrażanie brakujących aktualizacji systemu.
- Modyfikowanie konfiguracji systemu operacyjnego, które nie są zgodne z zalecanymi planami bazowymi.

Na poniższej ilustracji przedstawiono niektóre opcje, które można włączyć w Centrum zabezpieczeń.

![Zasadami Centrum zabezpieczeń Azure](./media/azure-security-iaas/security-center-policies.png)

[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) Microsoft oparte na chmurze IT rozwiązanie do zarządzania ułatwiające zarządzanie i ochrona lokalnej infrastruktury w chmurze. Ponieważ Operations Management Suite jest zaimplementowany jako usługa w chmurze, można wdrożyć, szybko i z minimalnym inwestycji w zasoby infrastruktury.

Nowe funkcje są przeprowadzane automatycznie, eliminuje konieczność rutynowej konserwacji i uaktualnić kosztów. Operations Management Suite integruje się również z programu System Center Operations Manager. Składa się z różnych składników w celu lepszego zarządzania Azure obciążeń, w tym [zabezpieczeń i zgodności](../operations-management-suite/oms-security-getting-started.md) modułu.

Funkcje zabezpieczeń i zgodności w Operations Management Suite służy do wyświetlania informacji o zasobach. Informacje są zorganizowane na cztery główne kategorie:

- **Domen zabezpieczeń**: dalszą analizę rekordy zabezpieczeń w czasie. Dostęp do oceny złośliwego oprogramowania, aktualizacji oceny, informacje o zabezpieczeniach sieci, tożsamości i dostępu do informacji i komputery ze zdarzeniami zabezpieczeń. Skorzystaj z szybki dostęp do pulpitu nawigacyjnego Centrum zabezpieczeń Azure.
- **Godne uwagi problemy**: szybką identyfikację liczba aktywnych problemów i ważność tych problemów.
- **Wykryć (wersja zapoznawcza)**: wykrycie ataku wzorce przez wizualizacja alerty zabezpieczeń po ich wprowadzeniu względem zasobów.
- **Analizy zagrożeń**: wykrycie ataku wzorce przez wizualizacja łącznej liczby serwerów z wychodzącym ruchem złośliwego oprogramowania IP, typ złośliwe oprogramowanie i mapy, pokazujący, gdzie pochodzą z tych adresów IP.
- **Typowe zapytania zabezpieczeń**: Zobacz listę typowych zapytań zabezpieczeń, które służy do monitorowania środowiska. Po kliknięciu jednego z tych kwerend **wyszukiwania** bloku otwiera i wyświetla wyniki dla tego zapytania.

Poniższy zrzut ekranu przedstawia przykład informacje, które można wyświetlić Operations Management Suite.

![Plany bazowe zabezpieczeń programu Operations Management Suite](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>Kolejne kroki

* [Blog zespołu ds. zabezpieczeń platformy Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Centrum zabezpieczeń firmy Microsoft](https://technet.microsoft.com/library/dn440717.aspx)
* [Wskazówki dotyczące zabezpieczeń platformy Azure i wzorce](security-best-practices-and-patterns.md)
