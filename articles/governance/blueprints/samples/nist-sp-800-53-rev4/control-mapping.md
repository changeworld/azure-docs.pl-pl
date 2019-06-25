---
title: Przykładowe mapowanie kontrolek - SP NIST 800-53 R4 planu-
description: Kontrolowanie mapowania NIST SP 800-53 R4 planu próbki do usługi Azure Policy i RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b887c4e6812d201dc83465a578f71e1742e8e9cf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342043"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>Kontrolowanie mapowania NIST SP 800-53 R4 planu próbki

Poniższy artykuł szczegółowo opisuje sposób przykładowy plan Azure schematy-SP NIST 800-53 R4 mapowania NIST SP 800-53 R4 kontrolki. Aby uzyskać więcej informacji na temat formantów, zobacz [SP NIST 800-53](https://nvd.nist.gov/800-53).

Następujące mapowania są **SP NIST 800-53 (Rev. 4)** kontrolki. Korzystanie z nawigacji po prawej stronie umożliwia przejście bezpośrednio do mapowania określonego formantu. Wiele kontrolek zamapowanego są implementowane za pomocą [usługi Azure Policy](../../../policy/overview.md) inicjatywy. Aby zapoznać się z pełną inicjatywy, otwórz **zasad** w witrynie Azure portal i wybierz pozycję **definicje** strony. Następnie znajdź i zaznacz  **[Wersja zapoznawcza]: Inspekcja NIST SP 800-53 R4 kontrolek i wdrażanie określonych rozszerzeń maszyn wirtualnych w celu spełnienia wymagań inspekcji** inicjatywa zasad wbudowanych.

## <a name="ac-2-account-management"></a>Zarządzanie kontami programu AC-2

Ten plan pomaga Przejrzyj kont, które mogą nie spełniać wymagania dotyczące zarządzania kontem Twojej organizacji. Ten plan przypisuje pięć Azure definicjom zasad inspekcji konta zewnętrzne z uprawnieniami do odczytu, zapisu i właściciela na subskrypcji i przestarzałe konta. Przeglądając kont, inspekcji przy użyciu tych zasad, może potrwać odpowiednią akcję, aby upewnić się, że spełnione są wymagania dotyczące zarządzania kontem.

- [Wersja zapoznawcza]: Audit deprecated accounts on a subscription
- [Wersja zapoznawcza]: Audit deprecated accounts with owner permissions on a subscription
- [Wersja zapoznawcza]: Audit external accounts with owner permissions on a subscription
- [Wersja zapoznawcza]: Audit external accounts with read permissions on a subscription
- [Wersja zapoznawcza]: Audit external accounts with write permissions on a subscription

## <a name="ac-2-7-account-management--role-based-schemes"></a>Zarządzanie kontami programu AC-2 (7) | Schematy opartej na rolach

Platforma Azure implementuje [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby ułatwić zarządzanie, kto ma dostęp do zasobów na platformie Azure. W witrynie Azure portal, możesz sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnienia. Ten plan przypisuje dwa [usługi Azure Policy](../../../policy/overview.md) definicje inspekcji korzystanie z uwierzytelniania usługi Azure Active Directory dla serwerów SQL i usługi Service Fabric. Za pomocą usługi Azure Active Directory uwierzytelniania umożliwia zarządzanie uprawnieniami uproszczone i scentralizowane identity management użytkowników bazy danych i innych usług firmy Microsoft.

- Przeprowadź inspekcję aprowizacji administrator usługi Azure Active Directory dla programu SQL server
- Inspekcja użycia usługi Azure Active Directory do uwierzytelniania klientów w usłudze Service Fabric

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>Zarządzanie kontami programu AC-2 (12) | Konto monitorowania / nietypowe użycie

Just-in-time (JIT) maszyny wirtualnej dostępu blokad ruch przychodzący do maszyn wirtualnych platformy Azure, co zmniejsza narażenie na ataki przy zapewnianiu łatwego dostępu, aby nawiązać połączenie z maszynami wirtualnymi w razie. Wszystkie żądania JIT dostęp do maszyn wirtualnych są rejestrowane w dzienniku aktywności, dzięki czemu można monitorować nietypowe użycie. Przypisuje ten plan [usługi Azure Policy](../../../policy/overview.md) definition, która pomaga monitorować maszyny wirtualne, które mogą obsługiwać dostęp just in time, ale nie został skonfigurowany.

- [Wersja zapoznawcza]: Monitoruj ewentualny dostęp tylko w czas (JIT) w usłudze Azure Security Center

## <a name="ac-4-information-flow-enforcement"></a>Wymuszanie przepływ informacji 4 programu AC

Krzyżowe pochodzenia zasobów między źródłami (cors) można zezwolić na zasoby usług aplikacji do zażądany z spoza domeny. Firma Microsoft zaleca, aby zezwolić tylko wymagane domen do interakcji z interfejsu API, funkcji i aplikacji sieci web. Przypisuje ten plan [usługi Azure Policy](../../../policy/overview.md) definicji, które ułatwiają monitorowanie ograniczenia dostępu do zasobów mechanizmu CORS w usłudze Azure Security Center.
Zrozumienie implementacji CORS może pomóc sprawdzić, czy kontroli przepływu informacji są zaimplementowane.

- [Wersja zapoznawcza]: Audit CORS resource access restrictions for a Web Application

## <a name="ac-5-separation-of-duties"></a>AC 5 podział obowiązków

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie zezwala na potrzeby administracyjne nadmiarowości. Z drugiej strony o zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć ryzyko naruszenia zabezpieczeń za pomocą konta właściciela z naruszonymi zabezpieczeniami. Ten plan ułatwia utrzymanie odpowiedniej liczby właścicieli subskrypcji platformy Azure, przypisując dwa [usługi Azure Policy](../../../policy/overview.md) definicje, które inspekcji liczba właścicieli subskrypcji platformy Azure. Ten plan przypisuje cztery definicji zasad platformy Azure, które ułatwiają kontrolowanie członkostwa w grupie Administratorzy na maszynach wirtualnych Windows. Zarządzanie właścicielem subskrypcji i uprawnienia administratora dla maszyny wirtualnej może pomóc w implementacji odpowiednie rozdzielenie obowiązków.

- [Wersja zapoznawcza]: Audit maximum number of owners for a subscription
- [Wersja zapoznawcza]: Audit minimum number of owners for subscription
- Inspekcja, że grupy Administratorzy wewnątrz maszyn wirtualnych Windows wyklucza określone elementy członkowskie
- Inspekcja, że grupa Administratorzy wewnątrz maszyn wirtualnych Windows zawiera określone elementy członkowskie
- Wdrażanie rozszerzenia maszyny Wirtualnej do inspekcji, że grupy Administratorzy wewnątrz maszyn wirtualnych Windows wyklucza określone elementy członkowskie
- Wdrażanie rozszerzenia maszyny Wirtualnej do inspekcji, że grupa Administratorzy wewnątrz maszyn wirtualnych Windows zawiera określone elementy członkowskie

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>Najniższe uprawnienia AC i 6 (7) | Przegląd uprawnień użytkownika

Platforma Azure implementuje [kontroli dostępu opartej na rolach](../../../../role-based-access-control/overview.md) (RBAC), aby ułatwić zarządzanie, kto ma dostęp do zasobów na platformie Azure. W witrynie Azure portal, możesz sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnienia. Ten plan przypisuje sześć [usługi Azure Policy](../../../policy/overview.md) definicji do inspekcji kont, które powinny mieć priorytet do przeglądu. Przeglądając te wskaźniki konta może pomóc upewnij się, że co najmniej uprawnień kontrolki są implementowane.

- [Wersja zapoznawcza]: Audit maximum number of owners for a subscription
- [Wersja zapoznawcza]: Audit minimum number of owners for subscription
- Inspekcja, że grupy Administratorzy wewnątrz maszyn wirtualnych Windows wyklucza określone elementy członkowskie
- Inspekcja, że grupa Administratorzy wewnątrz maszyn wirtualnych Windows zawiera określone elementy członkowskie
- Wdrażanie rozszerzenia maszyny Wirtualnej do inspekcji, że grupy Administratorzy wewnątrz maszyn wirtualnych Windows wyklucza określone elementy członkowskie
- Wdrażanie rozszerzenia maszyny Wirtualnej do inspekcji, że grupa Administratorzy wewnątrz maszyn wirtualnych Windows zawiera określone elementy członkowskie

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>Dostęp zdalny AC-17 (1) | Zautomatyzowane monitorowanie / sterowania

Ten plan ułatwia monitorowanie i kontrolowanie dostępu zdalnego, przypisując trzy [usługi Azure Policy](../../../policy/overview.md) definicje z monitorami oznacza dla zdalnego debugowania dla aplikacji w usłudze Azure App Service jest wyłączona i dwa definicjom zasad inspekcji systemu Linux maszyny wirtualne, zezwalających na połączenia zdalne z kont bez hasła. Ten plan przypisuje definicji usługi Azure Policy, która pomaga monitorować nieograniczonego dostępu do konta magazynu. Monitorowanie tych wskaźników może pomóc upewnić się, że metody dostępu zdalnego są zgodne z zasadami zabezpieczeń.

- [Wersja zapoznawcza]: Audit remote debugging state for a Function App
- [Wersja zapoznawcza]: Audit remote debugging state for a Web Application
- [Wersja zapoznawcza]: Audit remote debugging state for an API App
- [Wersja zapoznawcza]: Audit that Linux VMs do not allow remote connections from accounts without passwords
- [Wersja zapoznawcza]: Deploy VM extension to audit that Linux VMs do not allow remote connections from accounts without passwords
- Przeprowadź inspekcję dostępu do sieci bez ograniczeń do kont magazynu

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Zawartość rekordów inspekcji (Australia) i 3 (2) | Scentralizowane zarządzanie planowane inspekcji zawartości rekordu

Dane dzienników zbieranych przez usługi Azure Monitor znajduje się w obszarze roboczym usługi Log Analytics Włączenie scentralizowanego Konfiguracja i zarządzanie nim. Ten plan pomaga zagwarantować, zdarzenia są rejestrowane przez przypisanie siedem [usługi Azure Policy](../../../policy/overview.md) definicji, które inspekcji i wymuszania wdrożenia agenta usługi Log Analytics na maszynach wirtualnych platformy Azure.

- [Wersja zapoznawcza]: Wdrożenie agenta inspekcji Log Analytics — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Wdrażanie agentów analizy dziennika inspekcji w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Podgląd]: Deploy Log Analytics Agent for Linux VM Scale Sets (zestawu skalowania maszyn wirtualnych)
- [Wersja zapoznawcza]: Deploy Log Analytics Agent for Linux VMs
- [Podgląd]: Deploy Log Analytics Agent for Windows VM Scale Sets (zestawu skalowania maszyn wirtualnych)
- [Wersja zapoznawcza]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-5-response-to-audit-processing-failures"></a>Australia 5 odpowiedzi inspekcji niepowodzenia przetwarzania

Ten plan przypisuje pięciu [usługi Azure Policy](../../../policy/overview.md) definicje, które monitorują inspekcji i konfiguracje rejestrowania zdarzeń. Monitorowanie te konfiguracje można podać wskazuje niepowodzenie systemu inspekcji lub błędnej konfiguracji i ułatwiającymi podejmowanie działań naprawczych.

- [Wersja zapoznawcza]: Monitor unaudited SQL servers in Azure Security Center
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Wystąpienia zarządzane SQL inspekcji bez zaawansowane zabezpieczenia danych
- Przeprowadź inspekcję ustawienia inspekcji poziomu serwera SQL
- Serwery SQL bez zaawansowane zabezpieczenia danych inspekcji

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>Przegląd inspekcji (Australia) do 6 (4), analizy i raportowania | Przegląd centralnej i analiza

Dane dzienników zbieranych przez usługi Azure Monitor są przechowywane w roboczym usługi Log Analytics, umożliwiając scentralizowane raportowania i analizy. Ten plan pomaga zagwarantować, zdarzenia są rejestrowane przez przypisanie siedem [usługi Azure Policy](../../../policy/overview.md) definicji, które inspekcji i wymuszania wdrożenia agenta usługi Log Analytics na maszynach wirtualnych platformy Azure.

- [Wersja zapoznawcza]: Wdrożenie agenta inspekcji Log Analytics — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Wdrażanie agentów analizy dziennika inspekcji w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Podgląd]: Deploy Log Analytics Agent for Linux VM Scale Sets (zestawu skalowania maszyn wirtualnych)
- [Wersja zapoznawcza]: Deploy Log Analytics Agent for Linux VMs
- [Podgląd]: Deploy Log Analytics Agent for Windows VM Scale Sets (zestawu skalowania maszyn wirtualnych)
- [Wersja zapoznawcza]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-12-audit-generation"></a>Generowanie inspekcji 12 (Australia)

Ten plan pomaga zagwarantować, system zdarzenia są rejestrowane przez przypisanie 15 [usługi Azure Policy](../../../policy/overview.md) definicje, które Przeprowadź inspekcję ustawienia dziennika dla zasobów platformy Azure. Te definicje zasad inspekcji i wymuszania wdrożenia agenta usługi Log Analytics w usłudze Azure virtual machines i konfiguracji ustawień inspekcji dla innych typów zasobów platformy Azure. Te definicje zasad inspekcji również konfiguracji dzienniki diagnostyczne w celu zapewnienia wglądu w operacji wykonywanych w ramach zasobów platformy Azure. Ponadto Inspekcja i zabezpieczeniami zaawansowanymi danych są skonfigurowane na serwerze SQL.

- [Wersja zapoznawcza]: Wdrożenie agenta inspekcji Log Analytics — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Wdrażanie agentów analizy dziennika inspekcji w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Podgląd]: Deploy Log Analytics Agent for Linux VM Scale Sets (zestawu skalowania maszyn wirtualnych)
- [Wersja zapoznawcza]: Deploy Log Analytics Agent for Linux VMs
- [Podgląd]: Deploy Log Analytics Agent for Windows VM Scale Sets (zestawu skalowania maszyn wirtualnych)
- [Wersja zapoznawcza]: Deploy Log Analytics Agent for Windows VMs
- [Wersja zapoznawcza]: Monitor unaudited SQL servers in Azure Security Center
- Stosuj ustawienia diagnostyczne dla sieciowych grup zabezpieczeń
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Wystąpienia zarządzane SQL inspekcji bez zaawansowane zabezpieczenia danych
- Przeprowadź inspekcję ustawienia inspekcji poziomu serwera SQL
- Serwery SQL bez zaawansowane zabezpieczenia danych inspekcji
- Wdrażanie zaawansowanych danych na serwerach SQL
- Wdrażanie inspekcji na serwerach SQL

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>Funkcjonalność co najmniej 7 CM (2) | Zapobieganie wykonywania programu

Funkcji adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center to rozwiązanie umieszczania na białej liście automatyczne aplikacji inteligentnych end-to-end, które mogą zablokować lub uniemożliwienie określonym oprogramowaniem na maszynach wirtualnych. Sterowanie aplikacjami można uruchomić w trybie wymuszania, która zakazuje niezatwierdzonych aplikacji. Ten plan przypisuje usługi Azure Policy definition, która pomaga monitorować maszyny wirtualne, gdzie listę dozwolonych aplikacji jest zalecane, ale nie został jeszcze skonfigurowany.

- [Wersja zapoznawcza]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>Funkcjonalność co najmniej 7 CM (5) | Autoryzowane oprogramowanie / umieszczania na białej liście

Funkcji adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center to rozwiązanie umieszczania na białej liście automatyczne aplikacji inteligentnych end-to-end, które mogą zablokować lub uniemożliwienie określonym oprogramowaniem na maszynach wirtualnych. Kontrola aplikacji pomaga utworzyć listę zatwierdzonych aplikacji dla maszyn wirtualnych. Przypisuje ten plan [usługi Azure Policy](../../../policy/overview.md) definition, która pomaga monitorować maszyny wirtualne, gdzie listę dozwolonych aplikacji jest zalecane, ale nie został jeszcze skonfigurowany.

- [Wersja zapoznawcza]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-11-user-installed-software"></a>CM-11 User-Installed Software

Funkcji adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center to rozwiązanie umieszczania na białej liście automatyczne aplikacji inteligentnych end-to-end, które mogą zablokować lub uniemożliwienie określonym oprogramowaniem na maszynach wirtualnych. Kontrola aplikacji mogą pomóc w wymuszanie i monitorowanie zgodności z zasadami ograniczeń oprogramowania. Przypisuje ten plan [usługi Azure Policy](../../../policy/overview.md) definition, która pomaga monitorować maszyny wirtualne, gdzie listę dozwolonych aplikacji jest zalecane, ale nie został jeszcze skonfigurowany.

- [Wersja zapoznawcza]: Monitor possible app whitelisting in Azure Security Center

## <a name="cp-7-alternate-processing-site"></a>Lokacja przetwarzania alternatywne CP-7

Usługa Azure Site Recovery replikuje obciążenia uruchomione na maszynach wirtualnych z lokalizacji podstawowej do dodatkowej lokalizacji. Jeśli wystąpi awaria w lokacji głównej, obciążenia kończy się niepowodzeniem w dodatkowej lokalizacji. Przypisuje ten plan [usługi Azure Policy](../../../policy/overview.md) definicji, który przeprowadza inspekcję maszyn wirtualnych bez odzyskiwania po awarii skonfigurować. Monitorowanie tego wskaźnika może pomóc upewnić się, że konieczne kontroli gotowości w miejscu.

- Przeprowadź inspekcję maszyn wirtualnych bez odzyskiwania po awarii skonfigurować

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identyfikacja IA-2 (1) i uwierzytelnianie (użytkownicy w organizacji) | Dostęp sieciowy do kont uprzywilejowanych

Ten plan pomaga ograniczyć i kontroli dostępu uprzywilejowanego, przypisując dwa [usługi Azure Policy](../../../policy/overview.md) definicji do inspekcji kont z właścicielem i/lub uprawnienia, które nie mają włączonego uwierzytelniania wieloskładnikowego do zapisu. Uwierzytelnianie wieloskładnikowe pomaga bezpieczeństwo konta, nawet w przypadku naruszenia zabezpieczeń jeden zestaw informacji uwierzytelniania. Monitorując kont bez włączonego uwierzytelniania wieloskładnikowego, można zidentyfikować konta, które mogą być bardziej prawdopodobne naruszenia.

- [Wersja zapoznawcza]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Wersja zapoznawcza]: Audit accounts with write permissions who are not MFA enabled on a subscription

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identyfikacja IA-2 (2) i uwierzytelnianie (użytkownicy w organizacji) | Dostęp sieciowy do konta bez uprawnień

Ten plan pomaga ograniczyć i kontroli dostępu, przypisując [usługi Azure Policy](../../../policy/overview.md) definicji do inspekcji kont z Odczyt, które nie mają włączonego uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe pomaga bezpieczeństwo konta, nawet w przypadku naruszenia zabezpieczeń jeden zestaw informacji uwierzytelniania. Monitorując kont bez włączonego uwierzytelniania wieloskładnikowego, można zidentyfikować konta, które mogą być bardziej prawdopodobne naruszenia.

- [Wersja zapoznawcza]: Audit accounts with read permissions who are not MFA enabled on a subscription

## <a name="ia-5-authenticator-management"></a>Zarządzanie Authenticator IA-5

Ten plan przypisuje pięciu [usługi Azure Policy](../../../policy/overview.md) Przeprowadź inspekcję maszyn wirtualnych systemu Linux, które Zezwalaj na połączenia zdalne z kont bez haseł i/lub nieprawidłowe uprawnienia definicji ustawiona w pliku haseł. Ten plan przypisuje definicja zasad, która przeprowadza inspekcję koniugacja typu szyfrowania hasła dla maszyn wirtualnych Windows. Monitorowanie pomaga te wskaźniki, upewnij się, że wystawców uwierzytelnienia systemu są zgodne z zasadami organizacji Identyfikacja i uwierzytelnianie.

- [Wersja zapoznawcza]: Audit that Linux VMs do not have accounts without passwords
- [Wersja zapoznawcza]: Deploy VM extension to audit that Linux VMs do not have accounts without passwords
- [Wersja zapoznawcza]: Audit that Linux VMs have the passwd file permissions set to 0644
- [Wersja zapoznawcza]: Audit that Windows VMs store passwords using reversible encryption
- [Wersja zapoznawcza]: Deploy VM extension to audit that Linux VMs have the passwd file permissions set to 0644

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>Zarządzanie wystawcy uwierzytelnienia IA-5 (1) | Uwierzytelnianie oparte na hasłach

Ten plan pomaga można wymuszać stosowanie silnych haseł, przypisując 12 [usługi Azure Policy](../../../policy/overview.md) definicje, które Przeprowadź inspekcję maszyn wirtualnych Windows, które nie wymuszają minimalne i inne wymagania dotyczące hasła. Rozpoznawanie maszyn wirtualnych z naruszeniem zasad siły haseł pomaga podjąć działania naprawcze, aby upewnić się, że hasła dla kont wszystkich użytkowników maszyny wirtualnej są zgodne z zasadami haseł w organizacji.

- [Wersja zapoznawcza]: Audit that Windows VMs cannot re-use the previous 24 passwords
- [Wersja zapoznawcza]: Audit that Windows VMs have a maximum password age of 70 days
- [Wersja zapoznawcza]: Audit that Windows VMs have a minimum password age of 1 day
- [Wersja zapoznawcza]: Audit that Windows VMs have the password complexity setting enabled
- [Wersja zapoznawcza]: Audit that Windows VMs restrict the minimum password length to 14 characters
- [Wersja zapoznawcza]: Audit that Windows VMs store passwords using reversible encryption
- [Wersja zapoznawcza]: Deploy VM extension to audit that Windows VMs cannot re-use the previous 24 passwords
- [Wersja zapoznawcza]: Deploy VM extension to audit that Windows VMs have a maximum password age of 70 days
- [Wersja zapoznawcza]: Deploy VM extension to audit that Windows VMs have a minimum password age of 1 day
- [Wersja zapoznawcza]: Deploy VM extension to audit that Windows VMs have the password complexity setting enabled
- [Wersja zapoznawcza]: Deploy VM extension to audit that Windows VMs restrict the minimum password length to 14 characters
- [Wersja zapoznawcza]: Deploy VM extension to audit that Windows VMs store passwords using reversible encryption

## <a name="ra-5-vulnerability-scanning"></a>RA-5 wyszukiwaniu luk w zabezpieczeniach

Ten plan pomaga w zarządzaniu informacje dotyczą luk w zabezpieczeniach, przypisując cztery [usługi Azure Policy](../../../policy/overview.md) definicje, które monitorują luki w zabezpieczeniach systemu operacyjnego, wykrywania zagrożeń SQL i luk maszyny wirtualnej na platformie Azure Centrum zabezpieczeń. Usługa Azure Security Center zapewnia możliwości raportowania, które umożliwiają wgląd w czasie rzeczywistym w stan zabezpieczeń zasobów platformy Azure wdrożonych. Ten plan przypisuje także trzy definicje zasad inspekcji, które wymuszają zaawansowane zabezpieczenia danych na serwerach SQL. Bezpieczeństwo danych zaawansowane uwzględnione oceny luk w zabezpieczeniach i zagrożeń zaawansowane funkcje ochrony ułatwiające zrozumienie luk w zabezpieczeniach w wdrożonych zasobów.

- Wystąpienia zarządzane SQL inspekcji bez zaawansowane zabezpieczenia danych
- Serwery SQL bez zaawansowane zabezpieczenia danych inspekcji
- Wdrażanie zaawansowanych danych na serwerach SQL
- [Wersja zapoznawcza]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [Wersja zapoznawcza]: Monitor OS vulnerabilities in Azure Security Center
- [Wersja zapoznawcza]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Wersja zapoznawcza]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="sc-5-denial-of-service-protection"></a>Typu "odmowa SC-5" przez usługę

Platformy Azure, rozproszona odmowa usługi (DDoS) w warstwie standardowa zapewnia dodatkowe funkcje i możliwości ograniczania w warstwie podstawowa. Te dodatkowe funkcje obejmują integracji usługi Azure Monitor i przejrzeć raporty ograniczaniu ryzyka ataków po możliwości. Przypisuje ten plan [usługi Azure Policy](../../../policy/overview.md) definicję, która przeprowadza inspekcję obecności w warstwie standardowa przed atakami DDoS jest włączona. Opis możliwości różnią się warstwy usług może pomóc wybrać najlepsze rozwiązanie, aby adres typu "odmowa" zabezpieczenia usługi w środowisku platformy Azure.

- [Wersja zapoznawcza]: Audit standard tier of DDoS protection is enabled for a virtual network

## <a name="sc-7-boundary-protection"></a>Ochrona granic SC-7

Ten plan pomaga zarządzania i sterowania granicy systemu, przypisując [usługi Azure Policy](../../../policy/overview.md) definicję, która monitoruje sieciowe grupy zabezpieczeń z liberalnych regułach. Reguły, które są zbyt liberalnych mogą zezwolić na dostęp do sieci niezamierzone i powinna zostać przejrzana pod. Ten plan przypisuje definicja zasad, która monitoruje zalecenia dotyczące ograniczenia funkcjonalności grupy zabezpieczeń sieci w Centrum zabezpieczeń Azure. Usługa Azure Security Center analizuje wzorce ruchu z Internetem maszyn wirtualnych i zapewnia bezpieczeństwo sieci zalecenia regułę grupy, aby zmniejszyć obszar potencjalnych ataków.
Ponadto ten plan przypisuje także trzy definicje zasad, które monitorują niechronione punkty końcowe, aplikacji i kont magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę i kont magazynu z nieograniczonym dostępem umożliwia niekontrolowanego dostępu do informacji zawartych w systemie informacji.

- [Wersja zapoznawcza]: Monitor Internet-facing virtual machines for Network Security Group traffic hardening recommendations
- [Wersja zapoznawcza]: Monitor permissive network access in Azure Security Center
- [Wersja zapoznawcza]: Monitor unprotected network endpoints in Azure Security Center
- [Wersja zapoznawcza]: Monitor unprotected web application in Azure Security Center
- Przeprowadź inspekcję dostępu do sieci bez ograniczeń do kont magazynu

## <a name="sc-7-3-boundary-protection--access-points"></a>Ochrona granic SC-7 (3) | Punkty dostępu

Just-in-time (JIT) maszyny wirtualnej dostępu blokad ruch przychodzący do maszyn wirtualnych platformy Azure, co zmniejsza narażenie na ataki przy zapewnianiu łatwego dostępu, aby nawiązać połączenie z maszynami wirtualnymi w razie. Dostęp do maszyn wirtualnych JIT pomaga ograniczyć liczbę połączeń zewnętrznych z zasobami na platformie Azure. Przypisuje ten plan [usługi Azure Policy](../../../policy/overview.md) definition, która pomaga monitorować maszyny wirtualne, które mogą obsługiwać dostęp just in time, ale nie został skonfigurowany.

- [Wersja zapoznawcza]: Monitoruj ewentualny dostęp tylko w czas (JIT) w usłudze Azure Security Center

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>Ochrona granic SC-7 (4) | Usługi zewnętrzne telekomunikacyjne

Just-in-time (JIT) maszyny wirtualnej dostępu blokad ruch przychodzący do maszyn wirtualnych platformy Azure, co zmniejsza narażenie na ataki przy zapewnianiu łatwego dostępu, aby nawiązać połączenie z maszynami wirtualnymi w razie. Dostęp do maszyn wirtualnych JIT pomaga w zarządzaniu wyjątki od zasad przepływu ruchu w ułatwienia procesów żądania i zatwierdzania dostępu. Przypisuje ten plan [usługi Azure Policy](../../../policy/overview.md) definition, która pomaga monitorować maszyny wirtualne, które mogą obsługiwać dostęp just in time, ale nie został skonfigurowany.

- [Wersja zapoznawcza]: Monitoruj ewentualny dostęp tylko w czas (JIT) w usłudze Azure Security Center

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>(1) SC-28 ochrony informacji w stanie spoczynku | Kryptograficzna ochrona

Ten plan ułatwia wymuszanie zasad dotyczących używania cryptograph kontroli w celu ochrony informacji w spoczynku, przypisując 9 [usługi Azure Policy](../../../policy/overview.md) definicje, które wymuszają określonych cryptograph kontroli i inspekcji użytkowania weak kryptograficzne Ustawienia. Zrozumienie, w której zasoby platformy Azure może mieć optymalnej konfiguracji kryptograficznych mogą pomóc podjąć działania naprawcze, aby upewnić się, że zasoby są skonfigurowane zgodnie z zasadami bezpieczeństwa informacji. W szczególności definicje zasad przypisany przez ten plan wymagane szyfrowanie dla kont usługi data lake storage; wymagają technologii transparent data encryption w bazach danych SQL; i przeprowadzaj inspekcję brakującego szyfrowania bazy danych SQL, dyski maszyny wirtualnej i zmienne konta usługi automation.

- [Wersja zapoznawcza]: Monitor unencrypted SQL databases in Azure Security Center
- [Wersja zapoznawcza]: Monitor unencrypted VM Disks in Azure Security Center
- Inspekcja bezpiecznego transferu na kontach magazynu
- Wystąpienia zarządzane SQL inspekcji bez zaawansowane zabezpieczenia danych
- Serwery SQL bez zaawansowane zabezpieczenia danych inspekcji
- Inspekcja stanu przezroczystego szyfrowania danych
- Wdrażanie zaawansowanych danych na serwerach SQL
- Wdrażanie bazy danych SQL przezroczyste szyfrowanie danych
- Wymuszanie szyfrowania konta Data Lake Store

## <a name="si-2-flaw-remediation"></a>Korygowanie wady SI-2

Ten plan pomaga w zarządzaniu informacje o systemie wad, przypisując sześć [usługi Azure Policy](../../../policy/overview.md) definicje, które monitorują brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego, wykrywania zagrożeń SQL i maszyny wirtualnej luki w zabezpieczeniach w usłudze Azure Security Center. Usługa Azure Security Center zapewnia możliwości raportowania, które umożliwiają wgląd w czasie rzeczywistym w stan zabezpieczeń zasobów platformy Azure wdrożonych. Ten plan przypisuje definicji zasad, które gwarantuje, że automatyczne uaktualnianie systemu operacyjnego dla zestawów skalowania maszyn wirtualnych.

- [Wersja zapoznawcza]: Audit any missing system updates on virtual machine scale sets in Azure Security Center
- [Wersja zapoznawcza]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [Wersja zapoznawcza]: Monitor missing system updates in Azure Security Center
- [Wersja zapoznawcza]: Monitor OS vulnerabilities in Azure Security Center
- [Wersja zapoznawcza]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Wersja zapoznawcza]: Monitor VM Vulnerabilities in Azure Security Center
- Wymuszaj automatyczne uaktualnianie systemu operacyjnego z kontrolami kondycji aplikacji w usłudze VMSS

## <a name="si-3-malicious-code-protection"></a>Ochrona SI 3 złośliwego kodu.

Ten plan ułatwia zarządzanie programem endpoint protection, w tym ochronę złośliwego kodu, przypisując trzy [usługi Azure Policy](../../../policy/overview.md) definicje, które monitorują brakujących programu endpoint protection na maszynach wirtualnych w usłudze Azure Security Center i Wymuszanie rozwiązania do ochrony przed złośliwym oprogramowaniem firmy Microsoft na maszynach wirtualnych Windows.

- [Wersja zapoznawcza]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [Wersja zapoznawcza]: Monitor missing Endpoint Protection in Azure Security Center
- Wdróż domyślne rozszerzenie Microsoft IaaSAntimalware dla systemu Windows Server

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI i 3 (1) złośliwego kodu ochrony | Centralne zarządzanie

Ten plan ułatwia zarządzanie programem endpoint protection, w tym ochronę złośliwego kodu, przypisując dwa [usługi Azure Policy](../../../policy/overview.md) definicje, które monitorują brakujących programu endpoint protection na maszynach wirtualnych w usłudze Azure Security Center. Usługa Azure Security Center zapewnia scentralizowanego zarządzania i funkcje raportowania, które umożliwiają wgląd w czasie rzeczywistym w stan zabezpieczeń zasobów platformy Azure wdrożonych.

- [Wersja zapoznawcza]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [Wersja zapoznawcza]: Monitor missing Endpoint Protection in Azure Security Center

## <a name="si-4-information-system-monitoring"></a>System SI 4 informacji monitorowania

Ten plan pomaga monitorować system, inspekcji i wymuszania rejestrowania i bezpieczeństwo danych w ramach zasobów platformy Azure. W szczególności przypisanych zasad inspekcji i wymuszania wdrożenia agenta usługi Log Analytics, a także rozszerzone ustawienia zabezpieczeń dla baz danych SQL, kont magazynu i zasobów sieciowych. Te możliwości mogą pomóc wykrywać nietypowe zachowanie i wskaźniki ataków, aby można było podjąć odpowiednie działania.

- [Wersja zapoznawcza]: Wdrożenie agenta inspekcji Log Analytics — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Wdrażanie agentów analizy dziennika inspekcji w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
- [Wersja zapoznawcza]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Podgląd]: Deploy Log Analytics Agent for Linux VM Scale Sets (zestawu skalowania maszyn wirtualnych)
- [Wersja zapoznawcza]: Deploy Log Analytics Agent for Linux VMs
- [Podgląd]: Deploy Log Analytics Agent for Windows VM Scale Sets (zestawu skalowania maszyn wirtualnych)
- [Wersja zapoznawcza]: Deploy Log Analytics Agent for Windows VMs
- Wystąpienia zarządzane SQL inspekcji bez zaawansowane zabezpieczenia danych
- Serwery SQL bez zaawansowane zabezpieczenia danych inspekcji
- Wdrażanie zaawansowanych danych na serwerach SQL
- Wdróż zaawansowaną ochronę przed zagrożeniami dla kont magazynu
- Wdrażanie inspekcji na serwerach SQL
- Wdrażaj zasób network watcher podczas tworzenia sieci wirtualnych
- Wdrażanie wykrywanie zagrożeń na serwerach SQL

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI – 4 (18) systemu informacji monitorowania | Analizowanie ruchu / przekonwertować wykradanie

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystać kont magazynu. Alerty Protection obejmują wzorce nietypowego dostępu, nietypowe wyodrębnia/przekazywanie i magazynu podejrzanych działań. Tych wskaźników mogą pomóc wykryć przekonwertować wykradanie danych.

- Wdróż zaawansowaną ochronę przed zagrożeniami dla kont magazynu

## <a name="next-steps"></a>Kolejne kroki

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).