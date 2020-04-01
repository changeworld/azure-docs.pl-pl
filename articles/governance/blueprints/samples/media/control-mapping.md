---
title: Kontrole przykładów planu mediów
description: Kontrolowanie mapowania przykładów planu mediów. Każdy formant jest mapowany na jedną lub więcej zasad platformy Azure, które pomagają w ocenie.
ms.date: 02/24/2020
ms.topic: sample
ms.openlocfilehash: bdc4ef65c6d61a9d4217d7873bb29bd787842e71
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201906"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Mapowanie sterowania przykładowego planu mediów

W poniższym artykule opisano, jak przykładowy plan nośnika azure blueprint jest mapowana do kontrolek Media. Aby uzyskać więcej informacji na temat formantów, zobacz [Media](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Następujące mapowania są do **media** formantów. Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania sterowania. Wiele mapowanych formantów są implementowane za pomocą inicjatywy [azure policy.](../../../policy/overview.md) Aby przejrzeć pełną inicjatywę, otwórz **policy** w witrynie Azure portal i wybierz stronę **Definicje.** Następnie znajdź i wybierz ** \[opcję Podgląd\]: Inspekcja sterowania multimediami** wbudowaną inicjatywą zasad.

> [!IMPORTANT]
> Każdy formant poniżej jest skojarzony z jedną lub więcej definicji [zasad platformy Azure.](../../../policy/overview.md) Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolą; jednak często nie ma 1:1 lub pełne dopasowanie między formantem i jedną lub więcej zasad. W związku z tym **zgodne** w usłudze Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami formantu. Ponadto standard zgodności zawiera formanty, które nie są objęte żadnych definicji zasad platformy Azure w tej chwili. W związku z tym zgodność w zasadach platformy Azure jest tylko częściowy widok ogólnego stanu zgodności. Skojarzenia między formantami i definicjami zasad platformy Azure dla tego przykładu planu zgodności mogą się zmieniać wraz z czasem. Aby wyświetlić historię zmian, zobacz [historię zatwierdzania gitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Kontrola dostępu

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1- Upewnij się, że nie istnieje klucz dostępu głównego

- \[Podgląd\]: Wdrażanie wymagań wstępnych w celu inspekcji maszyn wirtualnych systemu Windows, które nie zawierają określonych certyfikatów w zaufanego katalogu głównego

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 - Hasła, kody PIN i tokeny muszą być chronione

- \[Podgląd\]: Wdrażanie wymagań wstępnych do inspekcji maszyn wirtualnych systemu Windows, które nie ograniczają minimalnej długości hasła do 14 znaków

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 - Dostęp do konta współdzielonego jest zabroniony

- Wszystkie reguły autoryzacji z wyjątkiem funkcji RootManageSharedAccessKey powinny zostać usunięte z obszaru nazw usługi Service Bus

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 -System musi ograniczyć dostęp do autoryzowanych użytkowników.

- Inspekcja nieograniczonego dostępu do kont magazynu

### <a name="ac--114--system-must-enforce-access-rights"></a>AC- 1.14 -System musi egzekwować prawa dostępu.

- \[Wersja\]zapoznawcza : Wdrażanie wymagań wstępnych do inspekcji konfiguracji maszyn wirtualnych systemu Windows w "Przypisywaniu praw użytkownika"

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC- 1.15 -Zapobieganie nieautoryzowanemu dostępowi do istotnych dla bezpieczeństwa informacji lub funkcji.

- \[Podgląd\]: Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w sekcji "Opcje zabezpieczeń - ustawienia systemowe"

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 - Rozdzielenie obowiązków musi być egzekwowane poprzez odpowiednie przypisanie roli.

- [Podgląd:\]Kontrola dostępu oparta na rolach (RBAC) powinna być używana w usługach Kubernetes

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40 - Upewnij się, że systemy nie łączą zaufanej sieci i niezaufanych sieci w tym samym czasie.

- \[Wersja\]zapoznawcza : Wdrażanie wymagań wstępnych do inspekcji konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń — dostęp do sieci"

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 & AC- 1.43 - Dostęp zdalny dla osób niebędących pracownikami musi być ograniczony, aby umożliwić dostęp tylko do specjalnie zatwierdzonych systemów informatycznych

- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Linux, które umożliwiają zdalne połączenia z kont bez haseł

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50- Rejestrowanie zdarzeń związanych z zabezpieczeniami dla wszystkich składników systemu informacyjnego.

- Dzienniki diagnostyczne w aplikacjach logiki powinny być włączone

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54- Upewnij się, że uwierzytelnianie wieloskładnikowe (MFA) jest włączone dla wszystkich użytkowników konsoli w chmurze.

- Usługa MFA powinna być włączona konta z uprawnieniami do zapisu w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe (MFA) powinno być włączone dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu kont lub zasobów.

## <a name="auditing--logging"></a>Rejestrowanie & inspekcji

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1- Udane i nieudane zdarzenia muszą być rejestrowane.

- Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL -2.16 - Urządzenia sieciowe/wystąpienia muszą rejestrować każde zdarzenie sklasyfikowane jako krytyczne zdarzenie zabezpieczeń przez to urządzenie sieciowe/wystąpienie (elfy sieci Web, zapory aplikacji internetowych itp.)

- \[Podgląd\]: Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Opcje zabezpieczeń - konta"

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17- Serwery/wystąpienia muszą rejestrować każde zdarzenie sklasyfikowane jako krytyczne zdarzenie zabezpieczeń przez ten serwer/wystąpienie

- \[Podgląd\]: Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Opcje zabezpieczeń - konta"

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 - Zdarzenia domeny muszą rejestrować każde zdarzenie sklasyfikowane jako zdarzenie krytyczne lub o wysokim poziomie bezpieczeństwa przez oprogramowanie do zarządzania domeną

- \[Podgląd\]: Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Opcje zabezpieczeń - konta"
- \[Wersja\]zapoznawcza : Wdrażanie wymagań wstępnych do inspekcji konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń — klient sieciowy firmy Microsoft"

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20- Zdarzenia domeny muszą rejestrować każde zdarzenie sklasyfikowane jako krytyczne zdarzenie zabezpieczeń przez kontrolę zabezpieczeń domeny

- \[Podgląd\]: Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Opcje zabezpieczeń - konta"

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21- Zdarzenia domeny muszą rejestrować każdy dostęp lub zmiany w dzienniku domeny

- \[Podgląd\]: Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych z systemem Windows w "Opcje zabezpieczeń - konsola odzyskiwania"

## <a name="cryptographic-controls"></a>Formanty kryptograficzne

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2 - Aplikacje i systemy muszą używać aktualnych rozwiązań kryptograficznych do ochrony danych.

- Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL
- Przejrzyste szyfrowanie danych powinno być włączone w celu ochrony danych w spoczynku i spełnienia wymagań zgodności

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5- Certyfikaty cyfrowe muszą być podpisane przez zatwierdzony urząd certyfikacji.

- \[Podgląd\]: Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, które zawierają certyfikaty wygasające w ciągu określonej liczby dni

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6- Certyfikaty cyfrowe muszą być jednoznacznie przypisane do użytkownika lub urządzenia.

- \[Podgląd\]: Wdrażanie wymagań wstępnych w celu inspekcji maszyn wirtualnych systemu Windows zawierających certyfikaty wygasające w ciągu określonej liczby dni

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7- Materiał kryptograficzny musi być przechowywany, aby umożliwić odszyfrowywanie rekordów przez czas przechowywania rekordów.

- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych
- Maszyny wirtualne bez włączonego szyfrowania dysku będą monitorowane przez usługę Azure Security Center jako zalecenia

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8- Klucze tajne i prywatne muszą być bezpiecznie przechowywane.

- Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL
- Przejrzyste szyfrowanie danych powinno być włączone w celu ochrony danych w spoczynku i spełnienia wymagań zgodności

## <a name="change--config-management"></a>Zmiana & zarządzania configami

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2- Tylko autoryzowani użytkownicy mogą wprowadzać zatwierdzone zmiany w systemie.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez usługę Azure Security Center jako zalecenia

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12 - Utrzymuj aktualną, kompletną, dokładną i łatwo dostępną podstawową konfigurację systemu informacyjnego.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez usługę Azure Security Center jako zalecenia

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13- Stosować zautomatyzowane narzędzia do utrzymania podstawowej konfiguracji systemu informacyjnego.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez usługę Azure Security Center jako zalecenia

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14 - Identyfikowanie i wyłączanie niepotrzebnych i/lub niezabezpieczonych funkcji, portów, protokołów i usług.

- Interfejsy sieciowe powinny wyłączyć przekazywanie adresów IP
- \[Podgląd:\]Przekazywanie ip na maszynie wirtualnej powinno być wyłączone

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19- Monitorowanie zmian w ustawieniach konfiguracji zabezpieczeń.

- Wdrażanie ustawień diagnostycznych dla grup zabezpieczeń sieci

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22 - Upewnij się, że tylko autoryzowane oprogramowanie i aktualizacje są zainstalowane w systemach firmy.

- Należy zainstalować aktualizacje systemu na maszynach
- Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez usługę Azure Security Center jako zalecenia

## <a name="identity--authentication"></a>Uwierzytelnianie & tożsamości

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1- Konta użytkowników muszą być jednoznacznie przypisane do osób fizycznych w celu uzyskania dostępu do informacji, które nie są klasyfikowane jako publiczne. Identyfikatory kont muszą być konstruowane przy użyciu standardowego formatu logicznego.

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji, aby zapobiec niemonitorowanemu dostępowi.

## <a name="network-security"></a>Bezpieczeństwo sieci

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2 - Dostęp do funkcji zarządzania urządzeniami sieciowymi jest ograniczony do autoryzowanych użytkowników.

- \[Wersja\]zapoznawcza : Wdrażanie wymagań wstępnych do inspekcji konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń — dostęp do sieci"

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3- Wszystkie urządzenia sieciowe muszą być skonfigurowane przy użyciu ich najbardziej bezpiecznych konfiguracji.

- \[Wersja\]zapoznawcza : Wdrażanie wymagań wstępnych do inspekcji konfiguracji maszyn wirtualnych systemu Windows w obszarze "Opcje zabezpieczeń — dostęp do sieci"

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5- Wszystkie połączenia sieciowe z systemem za pośrednictwem zapory muszą być regularnie zatwierdzane i poddawane inspekcji.

- \[Podgląd\]: Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Właściwości zapory systemu Windows"

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7- Odpowiednie formanty muszą być obecne na dowolnej granicy między zaufaną siecią a dowolną niezaufaną lub publiczną siecią.

- \[Podgląd\]: Wdrażanie wymagań wstępnych do inspekcji konfiguracji maszyn wirtualnych systemu Windows w "Właściwościach Zapory systemu Windows"

## <a name="security-planning"></a>Planowanie zabezpieczeń

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3- Należy zidentyfikować zagrożenia, które mogą negatywnie wpłynąć na poufność, integralność lub dostępność informacji i treści firmy wraz z prawdopodobieństwem ich wystąpienia.

- Zaawansowane typy ochrony przed zagrożeniami powinny być ustawione na "Wszystkie" w przypadku zarządzanym SQL ustawienia zaawansowanego zabezpieczenia danych

### <a name="security-continuity"></a>Ciągłość zabezpieczeń

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5- Dane w długoterminowym przechowywaniu muszą być dostępne przez cały okres przechowywania i chronione przed degradacją mediów i zmianami technologicznymi.

- Serwery SQL powinny być skonfigurowane z inspekcji dni przechowywania większe niż 90 dni.
- Inspekcja serwerów SQL skonfigurowanych z okresem przechowywania inspekcji krótszym niż 90 dni.

## <a name="system-integrity"></a>Integralność systemu

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3- Tylko autoryzowany personel może monitorować działania sieci i użytkowników.

- Luki w bazach danych SQL powinny zostać naprawione
- Monitoruj wyniki skanowania oceny luk w zabezpieczeniach i zalecenia dotyczące korygowania luk w zabezpieczeniach bazy danych.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4- Systemy internetowe muszą mieć wykrywanie włamań.

- Wdrażanie wykrywania zagrożeń na serwerach SQL

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13- Standardowe, centralnie zarządzane oprogramowanie chroniące przed złośliwym oprogramowaniem powinno zostać wdrożone w całej firmie.

- Wdrażanie domyślnego rozszerzenia Microsoft IaaSAntimalware dla systemu Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14- Oprogramowanie chroniące przed złośliwym oprogramowaniem musi skanować komputery i nośniki co tydzień co najmniej.

- Wdrażanie domyślnego rozszerzenia Microsoft IaaSAntimalware dla systemu Windows Server

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4- Upewnij się, że aplikacje są skanowane w poszukiwaniu luk w zabezpieczeniach co miesiąc.

- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyny wirtualnej powinny zostać naprawione
- Inspekcja luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyny wirtualnej, aby chronić je przed atakami.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5- Upewnij się, że luki są identyfikowane, sparowane z zagrożeniami i oceniane pod kątem ryzyka.

- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyny wirtualnej powinny zostać naprawione
- Inspekcja luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyny wirtualnej, aby chronić je przed atakami.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6- Upewnij się, że zidentyfikowane luki zostały naprawione w ramach wspólnie uzgodnionego harmonogramu.

- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyny wirtualnej powinny zostać naprawione
- Inspekcja luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyny wirtualnej, aby chronić je przed atakami.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7- Dostęp do systemów zarządzania lukami w zabezpieczeniach i korzystanie z nich musi być ograniczony do upoważnionego personelu.

- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyny wirtualnej powinny zostać naprawione
- Inspekcja luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyny wirtualnej, aby chronić je przed atakami.

> [!NOTE]
> Dostępność określonych definicji zasad platformy Azure może się różnić w przypadku platformy Azure dla instytucji rządowych i innych chmur krajowych. 

## <a name="next-steps"></a>Następne kroki

Przejrzano mapowanie formantu przykładowego planu mediów. Następnie odwiedź następujące artykuły, aby dowiedzieć się więcej o omówieniu i sposobie wdrażania tego przykładu:

> [!div class="next step action"]
> [Plan mediów — omówienie](./control-mapping.md)
> [planu nośnika — wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
