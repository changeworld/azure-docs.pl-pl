---
title: Przykładowe kontrole planu wzorcowego FUNDACJI CIS Microsoft Azure Foundations
description: Mapowanie rekomendacji przykładowego planu wzorca fundacji FUNDACJI CIS Microsoft Azure do zasad platformy Azure.
ms.date: 11/04/2019
ms.topic: sample
ms.openlocfilehash: ea61ae4ea05b34c785485cbb5fd39c8a772565e3
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656966"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Mapowanie rekomendacji przykładowego planu wzorca fundacji CIS Microsoft Azure Foundations

W poniższym artykule opisano, jak plan wzorów porównawczych azure blueprints CIS Microsoft Azure Foundations benchmark jest mapowy na zalecenia dotyczące wzorca fundacji CIS Microsoft Azure Foundations. Aby uzyskać więcej informacji na temat zaleceń, zobacz [wzorzec FUNDACJI CIS Microsoft Azure Foundations](https://www.cisecurity.org/benchmark/azure/).

Poniższe mapowania są do **wnudziwu fundacji FUNDACJI CIS w wersji 1.1.0.** Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do mapowania określonych rekomendacji.
Wiele mapowanych zaleceń jest implementowanych za pomocą inicjatywy [azure policy.](../../../policy/overview.md) Aby przejrzeć pełną inicjatywę, otwórz **policy** w witrynie Azure portal i wybierz stronę **Definicje.** Następnie znajdź i wybierz ** \[zalecenia inspekcji\] WNP Microsoft Azure Foundations Benchmark w wersji 1.1.0 i wdruż określone rozszerzenia maszyn wirtualnych, aby obsługiwać** wbudowaną inicjatywę zasad dotyczącą wymagań inspekcji.

> [!IMPORTANT]
> Każdy formant poniżej jest skojarzony z jedną lub więcej definicji [zasad platformy Azure.](../../../policy/overview.md) Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolą; jednak często nie ma 1:1 lub pełne dopasowanie między formantem i jedną lub więcej zasad. W związku z tym **zgodne** w usłudze Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami formantu. Ponadto standard zgodności zawiera formanty, które nie są objęte żadnych definicji zasad platformy Azure w tej chwili. W związku z tym zgodność w zasadach platformy Azure jest tylko częściowy widok ogólnego stanu zgodności. Skojarzenia między formantami i definicjami zasad platformy Azure dla tego przykładu planu zgodności mogą się zmieniać wraz z czasem. Aby wyświetlić historię zmian, zobacz [historię zatwierdzania gitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich uprzywilejowanych użytkowników

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które ułatwią monitorowanie, gdy uwierzytelnianie wieloskładnikowe nie jest włączone na uprzywilejowanych kontach usługi Azure Active Directory.

- Usługa MFA powinna być włączona na kontach z uprawnieniami właściciela w ramach subskrypcji
- Usługa MFA powinna być włączona na kontach z uprawnieniami do zapisu w ramach subskrypcji

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich użytkowników nieuprzywilejowanych

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która ułatwia monitorowanie, gdy uwierzytelnianie wieloskładnikowe nie jest włączone na nieuprzywilejowanych kontach usługi Azure Active Directory.

- Usługa MFA powinna być włączona na kontach z uprawnieniami do odczytu w ramach subskrypcji

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Upewnienie się, że nie ma użytkowników-gości

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które ułatwią monitorowanie kont gości, które mogą wymagać usunięcia.

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 Upewnij się, że nie są tworzone żadne role właściciela subskrypcji niestandardowej

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które ułatwią monitorowanie ról właściciela subskrypcji niestandardowej, które mogą wymagać usunięcia.

- Role właściciela subskrypcji niestandardowej nie powinny istnieć

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Upewnij się, że wybrano standardową warstwę cenową

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która ułatwia monitorowanie sieci i maszyn wirtualnych, w których warstwa standardowa usługi Security Center nie jest włączona.

 - Należy wybrać standardową warstwę cenową Centrum zabezpieczeń

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 Zapewnienie, aby "automatyczne inicjowanie obsługi administracyjnej agenta monitorującego" było ustawione na "Włączone"

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić automatyczne inicjowanie obsługi administracyjnej agenta usługi Log Analytics jest włączona.

- Automatyczne inicjowanie obsługi administracyjnej agenta monitorowania usługi Log Analytics powinno być włączone w ramach subskrypcji

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Upewnij się, że domyślne ustawienie zasad ASC "Monitorowanie aktualizacji systemu" nie jest "wyłączone"

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że aktualizacje systemu są zainstalowane na maszynach wirtualnych.

- Należy zainstalować aktualizacje systemu na maszynach

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj luki w systemie operacyjnym" nie jest "wyłączone"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która ułatwia monitorowanie nieodprawnionych luk w zabezpieczeniach maszyny wirtualnej.

- Luki w zabezpieczeniach konfiguracji na komputerach powinny zostać naprawione

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Upewnij się, że domyślne ustawienie zasad ASC "Monitorowanie ochrony punktów końcowych" nie jest "wyłączone"

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić ochronę punktów końcowych jest włączona na maszynach wirtualnych.

- Monitorowanie braku ochrony punktów końcowych w usłudze Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Upewnij się, że domyślne ustawienie zasad ASC "Monitorowanie szyfrowania dysku" nie jest "wyłączone"

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że dyski maszyny wirtualnej są szyfrowane.

- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj grupy zabezpieczeń sieci" nie jest "wyłączone"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która ułatwia ochronę maszyn wirtualnych z dostępem do Internetu.

- Reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych z widokiem na Internet powinny zostać wzmocnione

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 Upewnij się, że domyślne ustawienie zasad ASC "Monitorj zaporę aplikacji sieci Web" nie jest "wyłączone"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która ułatwia ochronę maszyn wirtualnych z uruchomionymi aplikacjami sieci web.

- Zasady nsgs dla aplikacji internetowych na IaaS powinny być zaostrzone

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 Upewnij się, że domyślne ustawienie zasad ASC "Włącz monitorowanie zapory nowej generacji (NGFW) nie jest "wyłączone"

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które pomagają chronić podsieci i maszyny wirtualne przed zagrożeniami, ograniczając dostęp. Zasady usługi Security Center, do których odwołuje się to zalecenie wzorca FUNDACJI CIS Microsoft Azure Foundations, zostały zastąpione dwoma nowymi zaleceniami. Zasady, o których mowa poniżej, odnoszą się do nowych zaleceń.

- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń
- Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Upewnij się, że domyślne ustawienie zasad ASC "Monitorowanie oceny luk w zabezpieczeniach" nie jest "wyłączone"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić wykrycie i skorygowanie luk w zabezpieczeniach.

- Luki w zabezpieczeniach powinny zostać naprawione przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 Upewnij się, że domyślne ustawienie zasad ASC "Monitorowanie szyfrowania obiektów blob magazynu" nie jest "wyłączone"

Szyfrowanie usługi Azure Storage jest włączone dla wszystkich nowych oraz istniejących kont magazynu i nie można go wyłączyć. (Jest to domyślna funkcja platformy Azure; nie ma przypisania zasad).

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Upewnij się, że domyślne ustawienie zasad ASC "Monitorj dostęp do sieci JIT" nie jest "wyłączone"

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga kontrolować dostęp do maszyn wirtualnych.

- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 Upewnij się, że domyślne ustawienie zasad ASC "Monitorowanie białej listy aplikacji adaptacyjnych" nie jest "wyłączone"

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że formantów aplikacji adaptacyjnych jest włączona na maszynach wirtualnych.

- Adaptacyjne sterowanie aplikacjami powinno być włączone na maszynach wirtualnych

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 Upewnij się, że domyślne ustawienie zasad ASC "Monitorowanie inspekcji SQL" nie jest "wyłączone"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić włączona inspekcja serwera SQL.

- Inspekcja powinna być włączona w przypadku zaawansowanych ustawień zabezpieczeń danych na programie SQL Server

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj szyfrowanie SQL" nie jest "wyłączone"

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że przezroczyste szyfrowanie danych jest włączone w bazach danych SQL.

- Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 Upewnij się, że ustawiono "e-maile kontaktowe z zabezpieczeniami"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić prawidłowe włączenie powiadomień o zabezpieczeniach

- Dla subskrypcji należy podać adres e-mail kontaktu z zabezpieczeniami

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 Upewnij się, że "numer telefonu" kontaktu bezpieczeństwa jest ustawiony

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić prawidłowe włączenie powiadomień o zabezpieczeniach

- Dla subskrypcji należy podać numer telefonu kontaktu z zabezpieczeniami

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 Upewnij się, że "Wyślij powiadomienie e-mail o alertach o wysokiej ważności" jest ustawione na "On"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić prawidłowe włączenie powiadomień o zabezpieczeniach

- Należy włączyć powiadomienia e-mail o wysokiej ważności

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 Upewnij się, że "Wyślij e-mail również do właścicieli subskrypcji" jest ustawiona na "On"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić prawidłowe włączenie powiadomień o zabezpieczeniach

- Powiadomienie e-mail do właściciela subskrypcji o wysokiej ważności alertów powinno być włączone

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Upewnij się, że "Wymagany bezpieczny transfer" jest ustawiony na "Włączone"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która ułatwia monitorowanie kont magazynu, które zezwalają na niezabezpieczone połączenia.

- Należy włączyć bezpieczny transfer na konta magazynu

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Upewnij się, że domyślna reguła dostępu do sieci dla kont magazynu jest ustawiona na odmowę

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która ułatwia monitorowanie kont magazynu, które umożliwiają nieograniczony dostęp.

- Inspekcja nieograniczonego dostępu do kont magazynu

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 Upewnij się, że "zaufane usługi firmy Microsoft" są włączone w celu uzyskania dostępu do konta magazynu

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która ułatwia monitorowanie kont magazynu, które nie zezwalają na dostęp z zaufanych usług firmy Microsoft.

- Konta magazynu powinny zezwalać na dostęp z zaufanych usług firmy Microsoft

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Upewnienie się, że "inspekcja" jest ustawiona na "On"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić włączona inspekcja serwera SQL. 

- Inspekcja powinna być włączona w przypadku zaawansowanych ustawień zabezpieczeń danych na programie SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Upewnij się, że "AuditActionGroups" w zasadach "inspekcji" dla serwera SQL jest prawidłowo ustawiony

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że inspekcja serwera SQL jest poprawnie skonfigurowana.

- Ustawienia inspekcji SQL powinny mieć skonfigurowane grupy akcji do przechwytywania działań krytycznych

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Zapewnienie, że zatrzymanie "audytu" jest "większe niż 90 dni"

Ten plan przypisuje definicji [usługi Azure Policy,](../../../policy/overview.md) która pomaga upewnić się, że dzienniki serwera SQL są przechowywane przez co najmniej 90 dni.

- Serwery SQL powinny być skonfigurowane z inspekcji dni przechowywania większe niż 90 dni.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Upewnij się, że "Zaawansowane zabezpieczenia danych" na serwerze SQL jest ustawione na "On"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić zaawansowane zabezpieczenia danych jest włączona na serwerach SQL i wystąpieniach zarządzanych SQL.

- Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych SQL
- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 Upewnij się, że "typy wykrywania zagrożeń" są ustawione na "Wszystkie"

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które pomagają zapewnić, że zaawansowana ochrona przed zagrożeniami jest poprawnie skonfigurowana na serwerach SQL i wystąpieniach zarządzanych SQL.

- Zaawansowane typy ochrony przed zagrożeniami powinny być ustawione na "Wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych serwera SQL
- Zaawansowane typy ochrony przed zagrożeniami powinny być ustawione na "Wszystkie" w przypadku zarządzanym SQL ustawienia zaawansowanego zabezpieczenia danych

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 Upewnij się, że ustawiono

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić, że zaawansowane powiadomienia o zabezpieczeniach danych są prawidłowo włączone.

- Zaawansowane ustawienia zabezpieczeń danych dla wystąpienia zarządzanego SQL powinny zawierać adres e-mail do odbierania alertów zabezpieczeń
- Zaawansowane ustawienia zabezpieczeń danych dla serwera SQL powinny zawierać adres e-mail do odbierania alertów zabezpieczeń

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Upewnij się, że "usługa poczty e-mail i współadministratorzy" są "włączone"

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić, że zaawansowane powiadomienia o zabezpieczeniach danych są prawidłowo włączone.

- Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych w przypadku zarządzanym sql
- Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych serwera SQL server

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Upewnij się, że administrator usługi Azure Active Directory jest skonfigurowany

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić, że administrator usługi Azure Active Directory jest aprowidzony dla serwerów SQL.

- Administrator usługi Azure Active Directory powinien być aprowizny dla serwerów SQL

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Upewnij się, że "Szyfrowanie danych" jest ustawione na "Włączone" w bazie danych SQL

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że przezroczyste szyfrowanie danych jest włączone w bazach danych SQL.

- Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Upewnij się, że ochrona TDE serwera SQL jest szyfrowana za pomocą BYOK (Użyj własnego klucza)

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które pomagają zapewnić, że chroniącą zaszyfrowane dane dla serwerów SQL i wystąpień zarządzanych SQL jest szyfrowany za pomocą własnego klucza.

- Ochrona TDE wystąpienia zarządzanego SQL powinna być szyfrowana za pomocą własnego klucza
- Ochrona TDE serwera SQL powinna być szyfrowana za pomocą własnego klucza

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 Upewnij się, że opcja "Wymuszanie połączenia SSL" jest ustawiona na "WŁĄCZONE" dla serwera bazy danych MySQL

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić, że serwery bazy danych MySQL wymuszają połączenia TLS/SSL.

- Wymuszanie połączenia SSL powinno być włączone dla serwerów bazy danych MySQL

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 Upewnij się, że parametr serwera "log_checkpoints" jest ustawiony na "ON" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić postgreSQL serwerów bazy danych dziennik punktów kontrolnych.

- Punkty kontrolne dziennika powinny być włączone dla serwerów bazy danych PostgreSQL

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 Upewnij się, że opcja "Wymuszanie połączenia SSL" jest ustawiona na "WŁĄCZONE" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić, że serwery bazy danych PostgreSQL wymuszają połączenia TLS/SSL.

- Wymuszanie połączenia SSL powinno być włączone dla serwerów bazy danych PostgreSQL

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 Upewnij się, że parametr serwera "log_connections" jest ustawiony na "ON" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić postgreSQL serwerów bazy danych rejestrowania połączeń.

- Połączenia dziennika powinny być włączone dla serwerów bazy danych PostgreSQL

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 Upewnij się, że parametr serwera "log_disconnections" jest ustawiony na "ON" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić postgreSQL serwerów bazy danych log rozłączenia.

- Rozłączenia powinny być rejestrowane dla serwerów bazy danych PostgreSQL.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 Upewnij się, że parametr serwera "log_duration" jest ustawiony na "ON" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że serwery bazy danych PostgreSQL rejestrują czas trwania zakończonych instrukcji.

- Czas trwania dziennika powinien być włączony dla serwerów bazy danych PostgreSQL

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 Upewnij się, że parametr serwera "connection_throttling" jest ustawiony na "ON" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga ograniczyć ataki brute życie na serwerach bazy danych PostgreSQL.

- Ograniczanie połączeń powinno być włączone dla serwerów bazy danych PostgreSQL

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Upewnij się, że administrator usługi Azure Active Directory jest skonfigurowany

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić, że administrator usługi Azure Active Directory jest aprowidzony dla serwerów SQL. Narzędzie CIS Microsoft Azure Foundations Benchmark zawiera to zalecenie; jest to jednak duplikat [zalecenia 4.8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Administrator usługi Azure Active Directory powinien być aprowizny dla serwerów SQL

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Upewnienie się, że istnieje profil dziennika

Ten plan przypisuje definicji [usługi Azure Policy,](../../../policy/overview.md) która pomaga upewnić się, że profil dziennika istnieje dla wszystkich subskrypcji platformy Azure. 

- Subskrypcje platformy Azure powinny mieć profil dziennika dla dziennika aktywności

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Upewnij się, że przechowywanie dzienników aktywności jest ustawione na 365 dni lub więcej

Ten plan przypisuje definicji [usługi Azure Policy,](../../../policy/overview.md) która pomaga upewnić się, że dzienniki aktywności są przechowywane przez co najmniej jeden rok.

- Dziennik aktywności powinien być zachowany przez co najmniej rok

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Zapewnienie, że profil audytu rejestruje wszystkie działania

Ten plan przypisuje definicji [usługi Azure Policy,](../../../policy/overview.md) która pomaga upewnić się, że profil dziennika jest poprawnie skonfigurowany.

- Profil dziennika usługi Azure Monitor powinien zbierać dzienniki dla kategorii "napisz", "usuń" i "akcja"

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Upewnij się, że profil dziennika przechwytuje dzienniki aktywności dla wszystkich regionów, w tym

Ten plan przypisuje definicji [usługi Azure Policy,](../../../policy/overview.md) która pomaga upewnić się, że profil dziennika jest poprawnie skonfigurowany.

- Usługa Azure Monitor powinna zbierać dzienniki aktywności ze wszystkich regionów

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Upewnij się, że rejestrowanie usługi Azure KeyVault jest "włączone"

Ten plan przypisuje definicji [usługi Azure Policy,](../../../policy/overview.md) która pomaga upewnić się, że dzienniki diagnostyczne są włączone dla magazynów kluczy.

- Dzienniki diagnostyczne w magazynie kluczy powinny być włączone

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Upewnij się, że obserwator sieci jest "włączony"

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że kontrola sieci jest włączona dla wszystkich regionów, w których są wdrażane zasoby. Ta zasada wymaga tablicy parametrów, która określa wszystkie odpowiednie regiony. Wartością domyślną w tej definicji inicjatywy polityki jest "eastus".

- Obserwator sieci powinien być włączony

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Upewnij się, że "dysk systemu operacyjnego" jest zaszyfrowany

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że szyfrowanie dysku jest włączone na maszynach wirtualnych.

- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Upewnij się, że "dyski danych" są szyfrowane

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że szyfrowanie dysku jest włączone na maszynach wirtualnych.

- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 Upewnij się, że "niezałączone dyski" są szyfrowane

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że dyski nieprzyłączone są szyfrowane.

- Dyski niezałączone powinny być szyfrowane

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 Upewnić się, że zainstalowane są tylko zatwierdzone rozszerzenia

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że tylko zatwierdzone rozszerzenia maszyny wirtualnej są zainstalowane. Ta zasada wymaga tablicy parametrów, która określa wszystkie zatwierdzone rozszerzenia maszyny wirtualnej. Ta definicja inicjatywy zasad zawiera sugerowane wartości domyślne, które klienci powinni zweryfikować. 

 - Należy zainstalować tylko zatwierdzone rozszerzenia maszyn wirtualnych

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Upewnij się, że stosowane są najnowsze poprawki systemu operacyjnego dla wszystkich maszyn wirtualnych

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że aktualizacje systemu są zainstalowane na maszynach wirtualnych.

- Należy zainstalować aktualizacje systemu na maszynach

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Upewnij się, że zainstalowano ochronę punktu końcowego dla wszystkich maszyn wirtualnych

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić ochronę punktów końcowych jest włączona na maszynach wirtualnych.

- Monitorowanie braku ochrony punktów końcowych w usłudze Azure Security Center

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 Upewnienie się, że przechowalnia kluczy jest odzyskiwalna

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić, że obiekty magazynu kluczy są możliwe do odzyskania w przypadku przypadkowego usunięcia.

- Obiekty magazynu kluczy powinny być odzyskiwalne

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Włączanie kontroli dostępu opartej na rolach (RBAC) w usługach Azure Kubernetes

Ten plan przypisuje definicję [zasad platformy Azure,](../../../policy/overview.md) która pomaga zapewnić, że kontrola dostępu oparta na rolach jest używana do zarządzanych uprawnień w klastrach usług Kubernetes

- \[Wersja\]zapoznawcza : Kontrola dostępu oparta na rolach (RBAC) powinna być używana w usługach Kubernetes

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Upewnij się, że aplikacja internetowa przekierowuje cały ruch HTTP do HTTPS w usłudze Azure App Service

Ten plan przypisuje definicji [zasad platformy Azure,](../../../policy/overview.md) która pomaga upewnić się, że aplikacje sieci web są dostępne tylko za pośrednictwem bezpiecznych połączeń.

- Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 Upewnij się, że aplikacja internetowa korzysta z najnowszej wersji szyfrowania TLS

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które pomagają upewnić się, że aplikacje sieci web korzystają z najnowszej wersji protokołu TLS.

- Najnowsza wersja TLS powinna być używana w aplikacji interfejsu API
- Najnowsza wersja TLS powinna być używana w aplikacji funkcji
- Najnowsza wersja TLS powinna być używana w aplikacji sieci Web

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 Upewnij się, że aplikacja internetowa ma ustawioną na "Włączone certyfikaty klientów (certyfikaty klientów przychodzących)"

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które pomagają upewnić się, że tylko klienci z prawidłowymi certyfikatami mogą dotrzeć do aplikacji sieci web.

- Upewnij się, że aplikacja INTERFEJSU API ma ustawioną na "Włączone certyfikaty klientów".
- Upewnij się, że aplikacja Function ma ustawioną na "Włączone certyfikaty klientów".
- Upewnij się, że aplikacja WEB ma ustawioną na "Włączone certyfikaty klientów (certyfikaty klientów przychodzących)"

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 Upewnij się, że rejestracja w usłudze Azure Active Directory jest włączona w usłudze App Service

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które pomagają upewnić się, że aplikacje sieci web używają tożsamości zarządzanej.

- Upewnij się, że usługa Zarejestruj się w usłudze Azure Active Directory jest włączona w aplikacji interfejsu API
- Upewnij się, że rejestracja w usłudze Azure Active Directory jest włączona w aplikacji function
- Upewnij się, że usługa Zarejestruj się w usłudze Azure Active Directory jest włączona w aplikacji WEB

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 Upewnij się, że wersja '.Net Framework' jest najnowsza, jeśli jest używana jako część aplikacji internetowej

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które pomagają upewnić się, że aplikacje sieci web korzystają z najnowszej wersji programu .Net Framework.

- Upewnij się, że wersja ".Net Framework" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API
- Upewnij się, że wersja ".Net Framework" jest najnowsza, jeśli jest używana jako część aplikacji funkcji
- Upewnij się, że wersja ".Net Framework" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 Upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji internetowej

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które pomagają upewnić się, że aplikacje sieci web korzystają z najnowszej wersji PHP.

- Upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana jako część aplikacji Api
- Upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana jako część aplikacji Function
- Upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana jako część aplikacji WEB

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 Upewnij się, że "wersja Pythona" jest najnowsza, jeśli jest używana do uruchamiania aplikacji internetowej

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które pomagają upewnić się, że aplikacje sieci web używają najnowszej wersji języka Python.

- Upewnij się, że "wersja Języka Python" jest najnowsza, jeśli jest używana jako część aplikacji Api
- Upewnij się, że "wersja Języka Python" jest najnowsza, jeśli jest używana jako część aplikacji Function
- Upewnij się, że "wersja Języka Python" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 Upewnij się, że "wersja Java" jest najnowsza, jeśli jest używana do uruchamiania aplikacji internetowej

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które pomagają upewnić się, że aplikacje sieci web używają najnowszej wersji oprogramowania Java.

- Upewnij się, że "wersja Java" jest najnowsza, jeśli jest używana jako część aplikacji Api
- Upewnij się, że "wersja Java" jest najnowsza, jeśli jest używana jako część aplikacji Funtion
- Upewnij się, że "wersja Java" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 Upewnij się, że "Wersja HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji internetowej

Ten plan przypisuje definicje [zasad platformy Azure,](../../../policy/overview.md) które pomagają upewnić się, że aplikacje sieci web używają najnowszej wersji protokołu HTTP.

- Upewnij się, że "Wersja HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji Api
- Upewnij się, że "Wersja HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji Function
- Upewnij się, że "Wersja HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji sieci Web


## <a name="next-steps"></a>Następne kroki

Teraz, gdy przejrzysz mapowanie kontrolne planu wzorcowego fundacji CIS Microsoft Azure Foundations, odwiedź następujące artykuły, aby dowiedzieć się więcej o planie, lub odwiedź witrynę Azure Policy w witrynie Azure portal, aby przypisać tę inicjatywę:

> [!div class="nextstepaction"]
> [Plan wzorcowy fundacji CIS Microsoft Azure Foundations — omówienie](./index.md)
> [planu wzorcowego FUNDACJI CIS Microsoft Azure Foundations — wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).