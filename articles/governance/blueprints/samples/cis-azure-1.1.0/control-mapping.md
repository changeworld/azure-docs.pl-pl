---
title: Przykład — przykłady Microsoft Azure fundacje testów porównawczych — mapowanie rekomendacji
description: Rekomendacja zaleceń usługi CIS Microsoft Azure przykłady planów testów porównawczych do Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f048262003a3567175c40ebf4ee744c41e11b5f9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918709"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Rekomendacja zaleceń przykładowego planu testów usługi CIS Microsoft Azure

W tym artykule szczegółowo przedstawiono sposób, w jaki plan platformy Azure Microsoft Azure fundacje planów testów porównawczych jest mapowany na zaleceń usługi CIS Microsoft Azure dotyczącej testów porównawczych. Aby uzyskać więcej informacji na temat zaleceń, zobacz artykuł dotyczący [testów usług CIS Microsoft Azure](https://www.cisecurity.org/benchmark/azure/).

Poniżej przedstawiono mapowania zaleceń dotyczących usługi **CIS Microsoft Azure testy porównawcze 1.1.0** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania rekomendacji.
Liczne zamapowane zalecenia są implementowane z inicjatywy [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz pozycję  **\[wersja zapoznawcza inspekcja w wersji zapoznawczej\] usługi CIS Microsoft Azure 1.1.0** .

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich uprzywilejowanych użytkowników

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które są wyrównane z tym zaleceniem CIS.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich użytkowników bez uprawnień

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 Upewnij się, że nie ma żadnych użytkowników-Gości

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które są wyrównane z tym zaleceniem CIS.

- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj aktualizacje systemu" nie jest "wyłączone"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Aktualizacje systemu powinny być zainstalowane na maszynach

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj luki w zabezpieczeniach systemu operacyjnego" nie jest wyłączone

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 Upewnij się, że domyślne ustawienie zasad ASC "Monitor Endpoint Protection nie jest" wyłączone "

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Monitoruj brakujące Endpoint Protection w Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj szyfrowanie dysków" nie jest wyłączone

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 upewnij się, że domyślne ustawienie zasad ASC "Monitoruj zaporę aplikacji sieci Web" nie jest "wyłączone"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Reguły sieciowych grup zabezpieczeń dla aplikacji sieci Web w usłudze IaaS powinny być zaostrzone

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 upewnij się, że domyślne ustawienie zasad ASC "Ocena luk w zabezpieczeniach monitora" nie jest wyłączone

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 upewnij się, że domyślne ustawienie zasad ASC "Monitoruj dostęp do sieci JIT" nie jest wyłączone

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Kontrola dostępu do sieci just in Time powinna być stosowana na maszynach wirtualnych

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 upewnij się, że domyślne ustawienie zasad ASC "Monitoruj szyfrowanie SQL" nie jest wyłączone

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Należy włączyć Transparent Data Encryption baz danych SQL

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 Upewnij się, że "wymagany bezpieczny transfer" jest ustawiony na wartość "Enabled"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Należy włączyć bezpieczny transfer do kont magazynu

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 upewnij się, że domyślna reguła dostępu do sieci dla kont magazynu jest ustawiona na Odmów

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 Upewnij się, że wartość "inspekcja" jest ustawiona na wartość "on"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Inspekcja powinna być włączona w zaawansowanych ustawieniach zabezpieczeń danych na SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 Upewnij się, że element "AuditActionGroups" w zasadach "Audits" dla programu SQL Server jest skonfigurowany prawidłowo

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Ustawienia inspekcji SQL powinny mieć skonfigurowane grupy akcji do przechwytywania działań krytycznych

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 Upewnij się, że przechowywanie "inspekcja" jest większe niż 90 dni

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Serwery SQL powinny być skonfigurowane z okresem przechowywania inspekcji większym niż 90 dni.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 Upewnij się, że w programie SQL Server ustawiono wartość "on" (Zaawansowane zabezpieczenia danych).

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Zaawansowane zabezpieczenia danych powinny być włączone na Twoich serwerach SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 Upewnij się, że ustawienie "typy wykrywania zagrożeń" ma wartość "All"

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które są wyrównane z tym zaleceniem CIS.

- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server
- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "All" w zaawansowanych ustawieniach zabezpieczeń danych wystąpienia zarządzanego SQL

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 Upewnij się, że ustawiono wartość "Wyślij alerty do"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Zaawansowane ustawienia zabezpieczeń danych dla programu SQL Server powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 upewnij się, że "usługa poczty E-mail i współadministratory" jest włączona

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Zaawansowane ustawienia zabezpieczeń danych dla wystąpienia zarządzanego SQL powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 upewnij się, że skonfigurowano administratora Azure Active Directory

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 upewnij się, że "szyfrowanie danych" jest ustawione na wartość "on" na SQL Database

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Należy włączyć Transparent Data Encryption baz danych SQL

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 upewnij się, że funkcja ochrony TDE programu SQL Server jest zaszyfrowana z BYOK (Użyj własnego klucza)

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które są wyrównane z tym zaleceniem CIS.

- Funkcja ochrony TDE serwera SQL powinna być zaszyfrowana za pomocą Twojego własnego klucza
- Funkcja ochrony TDE wystąpienia zarządzanego SQL powinna być szyfrowana przy użyciu własnego klucza

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 upewnij się, że rejestrowanie dla magazynu kluczy platformy Azure ma włączoną funkcję "Enabled"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Dzienniki diagnostyczne w Key Vault powinny być włączone

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 Upewnij się, że "dysk systemu operacyjnego" jest szyfrowany

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 Upewnij się, że "dyski danych" są zaszyfrowane

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 Upewnij się, że są stosowane najnowsze poprawki systemu operacyjnego dla wszystkich Virtual Machines

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Aktualizacje systemu powinny być zainstalowane na maszynach

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 Upewnij się, że program Endpoint Protection dla wszystkich Virtual Machines jest zainstalowany

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Monitoruj brakujące Endpoint Protection w Azure Security Center

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 włączenie kontroli dostępu opartej na rolach (RBAC) w ramach usług Azure Kubernetes Services

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- \[Wersja\]zapoznawcza: Access Control oparte na rolach (RBAC) powinny być używane w usługach Kubernetes Services

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 Upewnij się, że aplikacja internetowa przekierowuje cały ruch HTTP do protokołu HTTPS w Azure App Service

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która jest wyrównana do tego zalecenia CIS.

- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontroli w ramach planu porównawczego usługi CIS Microsoft Azure Zapoznaj się z poniższym artykułem, aby dowiedzieć się więcej na temat planu, lub odwiedź Azure Policy w Azure Portal, aby przypisać inicjatywę:

> [!div class="nextstepaction"]
> [Plan porównawczy usług CIS Microsoft Azure — omówienie](./index.md)
> [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).