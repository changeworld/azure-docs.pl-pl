---
title: Usługa Azure Security Baseline dla Eksploratora danych
description: Usługa Azure Security Baseline dla Eksploratora danych
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92a607e4c17a16d07f07c97f626ed98a213d509b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289721"
---
# <a name="azure-security-baseline-for-data-explorer"></a>Usługa Azure Security Baseline dla Eksploratora danych

Usługa Azure Security Baseline dla Eksploratora danych zawiera zalecenia, które pomogą Ci poprawić poziom zabezpieczeń wdrożenia.

Punkt odniesienia dla tej usługi jest pobierany z [usługi Azure Security Benchmark w wersji 1.0,](https://docs.microsoft.com/azure/security/benchmarks/overview)która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure za pomocą naszych najlepszych wskazówek dotyczących rozwiązań.

Aby uzyskać więcej informacji, zobacz [omówienie planów bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

**Wskazówki:** Usługa Azure Data Explorer obsługuje wdrażanie klastra w podsieci w sieci wirtualnej. Ta funkcja umożliwia wymuszanie reguł sieciowej grupy zabezpieczeń (NSG) w łączeniu się z usługą Azure Data Explorer, łączenie sieci lokalnej z podsiecią klastra usługi Azure Data Explorer i zabezpieczanie źródeł połączeń danych (Centrum zdarzeń i siatka zdarzeń) za pomocą usługi Event Hub i Event Grid. punktów końcowych usługi.

Jak wdrożyć klaster usługi Azure Data Explorer w sieci wirtualnej:https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki:** Włącz dzienniki przepływu sieciowej grupy zabezpieczeń (NSG) i wyślij dzienniki do konta magazynu w celu inspekcji ruchu.

Jak włączyć dzienniki przepływu nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Opis zabezpieczeń sieci dostarczanych przez usługę Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki**: Nie dotyczy; Zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmów komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Włącz standard ochrony przed atakami DDoS w sieci wirtualnej w sieci wirtualnej, chroniąc klastry Eksploratora danych w celu ochrony przed atakami DDoS. Usługa Azure Security Center Integrated Threat Intelligence umożliwia odmawianie komunikacji ze znanymi złośliwymi lub nieużywanym internetowymi adresami IP.

Jak skonfigurować ochronę przed atakami DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Poznaj zintegrowaną analizę zagrożeń w centrum zabezpieczeń platformy Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Rejestrowanie pakietów sieciowych i dzienników przepływu

**Wskazówki:** Włącz dzienniki przepływu w sieciowych grupach zabezpieczeń (NSG) używanych do ochrony klastra usługi Azure Data Explorer i wysyłaj dzienniki do konta magazynu w celu inspekcji ruchu.

Jak włączyć dzienniki przepływu nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Wdrożenie systemów wykrywania włamań/włamań opartych na sieci

**Wskazówki**: Nie dotyczy; Ten formant jest wykonywany w punkcie końcowym lub zaporze.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Zarządzanie ruchem w aplikacjach internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i administracyjnych narzutów administracyjnych dotyczących zasad bezpieczeństwa sieci

**Wskazówki:** Użyj tagów usługi sieci wirtualnej do definiowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub zaporach platformy Azure skojarzonych z klastrami usługi Azure Data Explorer. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów.

Opis i używanie tagów usług:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Wymagania konfiguracji tagów usług dla Eksploratora danych platformy Azure:https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Klient do definiowania i implementowania standardowych konfiguracji zabezpieczeń dla zasobów sieciowych za pomocą usługi Azure Policy.

Klient może również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez artefakty kluczowe środowisko pakowania, takie jak szablony ARM, formanty RBAC i zasady, w jednej definicji planu. Łatwo zastosować plan do nowych subskrypcji i środowisk i dostosować kontroli i zarządzania poprzez przechowywanie wersji.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak utworzyć plan platformy Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1.10: Reguły konfiguracji ruchu dokumentów

**Wskazówki:** Użyj tagów dla sieciowych grup zabezpieczeń (NSG) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu dla klastrów eksploratora danych. W przypadku poszczególnych reguł sieciowej grupy danych sieciowych należy użyć pola "Opis", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł umożliwiających ruch do/z sieci.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj zasad platformy Azure, aby sprawdzić poprawność (i/lub skorygować) konfigurację zasobów sieciowych.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Korzystanie z zatwierdzonych źródeł synchronizacji czasu

**Wskazówki**: Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure, klienci mogą aktualizować synchronizację czasu dla wdrożeń obliczeniowych należących do klienta.

Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych platformy Azure:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Usługa Azure Data Explorer używa dzienników diagnostycznych do szczegółowych informacji na temat sukcesów i błędów pozyskiwania. Dzienniki operacji można eksportować do usługi Azure Storage, Event Hub lub Log Analytics w celu monitorowania stanu pozyskiwania.

Jak monitorować operacje pozyskiwania usługi Azure Data Explorer:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Jak pozyskiwania i badania danych monitorowania w Eksploratorze danych platformy Azure:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Włączanie rejestrowania inspekcji zasobów platformy Azure

**Wskazówki:** Włącz ustawienia diagnostyczne dla Eksploratora danych platformy Azure w celu uzyskania dostępu i rejestrowania do operacji i rejestrowania określonych usług. Dzienniki aktywności platformy Azure w usłudze Azure Monitor, która obejmuje rejestrowanie wysokiego poziomu dotyczące zasobu, są domyślnie włączone.

Jak monitorować operacje pozyskiwania usługi Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Jak zbierać dzienniki platformy i metryki za pomocą usługi Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Omówienie dzienników platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Zbieranie dzienników zabezpieczeń z systemu operacyjnego

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** W usłudze Azure Monitor ustaw okres przechowywania obszaru roboczego usługi Log Analytics zgodnie z przepisami dotyczącymi zgodności obowiązującymi w organizacji. Użyj kont usługi Azure Storage dla magazynu długoterminowego/archiwizacji.

Jak ustawić parametry przechowywania dzienników dla obszarów roboczych usługi Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorowanie i przeglądanie dzienników

**Wskazówki:** Analizuj i monitoruj dzienniki pod kątem nietypowych zachowań i regularnie przeglądaj wyniki. Po włączeniu ustawień diagnostycznych dla Eksploratora danych platformy Azure użyj obszaru roboczego analizy dzienników usługi Azure Monitor, aby przejrzeć dzienniki i wykonać kwerendy w danych dziennika.

Opis obszaru roboczego analizy dzienników:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak wykonywać zapytania niestandardowe w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Włącz alerty dla nietypowej aktywności

**Wskazówki**: Nie dotyczy; Usługa Azure Data Explorer nie ma tej możliwości.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizuj rejestrowanie przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; Usługa Azure Data Explorer nie przetwarza rejestrowania ochrony przed złośliwym oprogramowaniem.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2.9: Włącz rejestrowanie zapytań DNS

**Wskazówki**: Nie dotyczy: kwerenda DNS nie jest funkcją Eksploratora danych platformy Azure.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włącz rejestrowanie inspekcji wiersza polecenia

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Kontrola tożsamości i dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Prowadzenie spisu rachunków administracyjnych

**Wskazówki:** W Eksploratorze danych platformy Azure role zabezpieczeń definiują, które podmioty zabezpieczeń (użytkownicy i aplikacje) mają uprawnienia do działania na zabezpieczonym zasobie, takim jak baza danych lub tabela, oraz jakie operacje są dozwolone.  Można wykorzystać kwerendy Kusto do listy zasad w roli administratora dla klastrów i baz danych usługi Azure Data Explorer.
Zarządzanie rolami zabezpieczeń w Eksploratorze danych platformy Azure przy użyciu zapytania Kusto:https://docs.microsoft.com/azure/kusto/management/security-roles



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmienianie haseł domyślnych w stosownych przypadkach

**Wskazówki:** Usługa Azure AD nie ma pojęcia haseł domyślnych. Inne zasoby platformy Azure wymagające hasła wymusza hasło, które mają być tworzone z wymaganiami złożoności i minimalnej długości hasła, która różni się w zależności od usługi. Użytkownik jest odpowiedzialny za aplikacje innych firm i usługi marketplace, które mogą używać haseł domyślnych.


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Zapewnienie wykorzystania specjalnych rachunków administracyjnych

**Wskazówki**: Klient tworzy standardowe procedury operacyjne dotyczące korzystania z dedykowanych kont administracyjnych. Usługa Azure Security Center Identity and Access Management służy do monitorowania liczby kont administracyjnych.

Klienci mogą również włączyć just-in-time / Just-Enough-Access przy użyciu ról uprzywilejowanych zarządzania tożsamościami usługi Azure AD dla usług Microsoft i usługi Azure ARM. 

Co to jest zarządzanie tożsamościami uprzywilejowanymi usługi Azure AD?:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Korzystanie z logowania jednokrotnego (Logowanie jednokrotne) za pomocą usługi Azure Active Directory

**Wskazówki:** Tam, gdzie to możliwe, klient do korzystania z logowania przy użyciu usługi Azure Active Directory (Azure AD), a nie konfigurowania poszczególnych poświadczeń autonomicznych na usługę. Użyj zaleceń dotyczących tożsamości i zarządzania dostępem w usłudze Azure Security Center.

Opis usługi SSO za pomocą usługi Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich dostępu opartego na usłudze Azure Active Directory.

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (Azure AD) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem usługi Azure Security Center.

Jak włączyć usługę MFA na platformie Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Jak monitorować tożsamość i dostęp w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Korzystanie z dedykowanych maszyn (stacje robocze z uprzywilejowanym dostępem) do wszystkich zadań administracyjnych

**Wskazówki:** Użyj paws (uprzywilejowanych stacji roboczych dostępu) z uwierzytelnianiem wieloskładnikowym (MFA) skonfigurowany do logowania się i konfigurowania zasobów platformy Azure.

Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: Rejestrowanie i ostrzeganie o podejrzanej aktywności na kontach administracyjnych

**Wskazówki:** Użyj raportów zabezpieczeń usługi Azure Active Directory (Azure AD) do generowania dzienników i alertów, gdy w środowisku występuje podejrzane lub niebezpieczne działanie. Monitorowanie aktywności związanej z tożsamością i dostępem za pomocą usługi Azure Security Center

Jak zidentyfikować użytkowników usługi Azure AD oflagowanych pod kątem ryzykownych działań:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorować tożsamość użytkowników i dostęp do aktywności w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Klient używa lokalizacji o nazwie Dostęp warunkowy do zezwalania na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

Jak skonfigurować nazwane lokalizacje na platformie Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="39-utilize-azure-active-directory"></a>3.9: Korzystanie z usługi Azure Active Directory

**Wskazówki:** Usługa Azure Active Directory (Azure AD) jest preferowaną metodą uwierzytelniania w Eksploratorze danych platformy Azure. Obsługuje szereg scenariuszy uwierzytelniania:

Uwierzytelnianie użytkownika (logowanie interaktywne): służy do uwierzytelniania podmiotów ludzkich.

Uwierzytelnianie aplikacji (logowanie nieinterakcyjne): służy do uwierzytelniania usług i aplikacji, które muszą być uruchamiane/uwierzytelniane bez obecności użytkownika ludzkiego.

Omówienie kontroli dostępu do usługi Azure Data Explorer:https://docs.microsoft.com/azure/kusto/management/access-control

Uwierzytelnianie za pomocą usługi Azure Active Directory:https://docs.microsoft.com/azure/kusto/management/access-control/aad



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Usługa Azure Active Directory (Azure AD) udostępnia dzienniki ułatwiające odnajdowanie starych kont. Ponadto użyj przeglądów dostępu do tożsamości platformy Azure, aby skutecznie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika można regularnie przeglądać, aby upewnić się, że tylko prawo Użytkownicy mają stały dostęp. 

Jak uwierzytelnić się za pomocą usługi Azure AD dla usługi Azure Data Explorer Access:https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad

Raportowanie usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak korzystać z przeglądów dostępu tożsamości platformy Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych kont

**Wskazówki:** Możesz użyć usługi Azure Active Directory (Azure AD) Zaloguj się w działania, inspekcji i źródeł dziennika zdarzeń ryzyka do monitorowania, który umożliwia integrację z dowolnego narzędzia do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) / monitorowania.

 Możesz usprawnić ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure Active Directory, wysyłając dzienniki inspekcji i logi logowania do obszaru roboczego usługi Log Analytics. Klient, aby skonfigurować żądane alerty w obszarze roboczym analizy dzienników.

Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

**Wskazówki:** Użyj funkcji wykrywania ryzyka usługi Azure Active Directory (Azure AD) i ochrony tożsamości, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Ponadto można pozyskiwania danych do usługi Azure Sentinel do dalszego badania.

Jak wyświetlić ryzykowne logowania usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady ryzyka ochrony tożsamości:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** W scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych klienta, skrytka klienta udostępnia interfejs dla klientów do przeglądania i zatwierdzania lub odrzucania żądań dostępu do danych klienta.

Opis skrytki klienta:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Prowadzenie spisu poufnych informacji

**Wskazówki:** Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemy izolowania przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Implementowanie oddzielnych subskrypcji i/lub grup zarządzania do programowania, testowania i produkcji. Klastry usługi Azure Data Explorer powinny być oddzielone od innych zasobów przez sieć wirtualną/podsieć, odpowiednio oznakowane i zabezpieczone w sieciowej grupie zabezpieczeń (NSG) lub zaporze platformy Azure. Klastry Eksploratora danych przechowujące lub przetwarzające poufne dane powinny być wystarczająco odizolowane.

Jak utworzyć dodatkowe subskrypcje platformy Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak wdrożyć klaster usługi Azure Data Explorer w sieci wirtualnej:https://docs.microsoft.com/azure/data-explorer/vnet-deployment

Jak utworzyć nsg z configiem zabezpieczeń:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego przesyłania poufnych informacji

**Wskazówki**: Nie dotyczy; W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki:** Klaster usługi Azure Data Explorer domyślnie negocjuje TLS 1.2. Upewnij się, że klienci łączący się z zasobami platformy Azure są w stanie negocjować TLS 1.2 lub więcej.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdowania</div>

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania stratom danych nie są jeszcze dostępne dla Eksploratora danych platformy Azure. Implementowanie rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>Kontrola dostępu do zasobów za pomocą narzędzia Azure RBAC</div>

**Wskazówki:** Usługa Azure Data Explorer umożliwia kontrolowanie dostępu do baz danych i tabel przy użyciu modelu kontroli dostępu opartego na rolach (RBAC). W tym modelu podmioty (użytkownicy, grupy i aplikacje) są mapowane na role. Podmioty mogą uzyskiwać dostęp do zasobów zgodnie z przypisanymi rolami.

Lista ról i uprawnień oraz instrukcje dotyczące konfigurowania funkcji RBAC dla Eksploratora danych platformy Azure:https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Wymuszanie kontroli dostępu za pomocą funkcji zapobiegania utracie danych opartych na hoście

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą usługi Azure Data Explorer i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub ekspozycji danych klientów.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfruj poufne informacje w spoczynku

**Wskazówki:** Szyfrowanie dysków platformy Azure pomaga chronić i chronić dane, aby spełnić zobowiązania dotyczące zabezpieczeń i zgodności z przepisami organizacji. Zapewnia szyfrowanie woluminów dla systemu operacyjnego i dysków danych maszyn wirtualnych klastra. Integruje się również z usługą Azure Key Vault, która umożliwia nam kontrolowanie kluczy szyfrowania dysku i wpisy tajne oraz zarządzanie nimi oraz zapewnia, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w stanie spoczynku w usłudze Azure Storage.

Jak włączyć szyfrowanie w spoczynku dla klastrów usługi Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

**Wskazówki:** Użyj usługi Azure Monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące zmiany na poziomie zasobów w klastrach usługi Azure Data Explorer.

Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Postępuj zgodnie z zaleceniami usługi Azure Security Center dotyczącymi zabezpieczania zasobów eksploratora danych platformy Azure.

Firma Microsoft wykonuje również zarządzanie lukami w zabezpieczeniach w podstawowych systemach obsługujących Eksploratora danych platformy Azure.

Poznaj zalecenia usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Wdrażanie rozwiązania do zarządzania poprawkami zautomatyzowanego systemu operacyjnego

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Wdrożenie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanowania luk w zabezpieczeniach typu back-to-back

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Wykorzystaj proces oceny ryzyka, aby nadać priorytet usuwaniu wykrytych luk w zabezpieczeniach.

**Wskazówki:** Użyj domyślnych ocen ryzyka (Secure Score) dostarczonych przez usługę Azure Security Center.
Poznaj bezpieczny wynik usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie zapasami i zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-utilize-azure-asset-discovery"></a>6.1: Korzystanie z odnajdowania zasobów platformy Azure

**Wskazówki:** Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywać wszystkie zasoby (takie jak zasoby obliczeniowe, magazyn, sieć, porty i protokoły itp.) w ramach subskrypcji. Upewnij się, odpowiednie (odczytu) uprawnienia w dzierżawie i wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach subskrypcji.

Chociaż klasyczne zasoby platformy Azure mogą zostać wykryte za pomocą wykresu zasobów, zdecydowanie zaleca się tworzenie i używanie zasobów usługi Azure Resource Manager w przyszłości.

Jak tworzyć zapytania za pomocą programu Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Poznaj usługę Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6.2: Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure, podając metadane logicznie zorganizować je w taksonomię.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** W stosownych przypadkach można używać odpowiednich konwencji nazewnictwa, znakowania, grup zarządzania lub oddzielnych subskrypcji, aby organizować i śledzić zasoby. Za pomocą usługi Azure Resource Graph można regularnie uzgadniać zapasy i upewnić się, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie. 

Jak utworzyć dodatkowe subskrypcje platformy Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak tworzyć zapytania za pomocą programu Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Obsługa spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania.

**Wskazówki:** Należy utworzyć spis zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych zgodnie z potrzebami organizacji.  


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki:** Zasady platformy Azure można używać do nakładania ograniczeń na typ zasobów, które można tworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

    - Niedozwolone typy zasobów

    - Dozwolone typy zasobów

Będziesz mógł monitorować zdarzenia generowane przez zasady za pomocą 

Dzienniki aktywności, które mogą być monitorowane przy użyciu usługi Azure Monitor.

Ponadto można użyć usługi Azure Resource Graph do kwerendy/odnajdywać zasoby w ramach subskrypcji.

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Szybki start: uruchom pierwszą kwerendę wykresu zasobów przy użyciu Eksploratora wykresu zasobów platformy Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Tworzenie, wyświetlanie i zarządzanie alertami dziennika aktywności przy użyciu usługi Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w ramach zasobów obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Usuwanie niezatwierdzonych zasobów platformy Azure i aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="68-utilize-only-approved-applications"></a>6.8: Korzystaj tylko z zatwierdzonych aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="69-utilize-only-approved-azure-services"></a>6.9: Korzystaj tylko z zatwierdzonych usług platformy Azure

**Wskazówki:** Zasady platformy Azure można używać do nakładania ograniczeń na typ zasobów, które można tworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

    - Niedozwolone typy zasobów

    - Dozwolone typy zasobów

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przykłady zasad platformy Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6.10: Wdrożenie listy zatwierdzonych wniosków

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Ograniczanie użytkownikom możliwości interakcji z usługą Azure Resources Manager za pomocą skryptów

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując "Blokuj dostęp" dla aplikacji "Microsoft Azure Management". Uniemożliwi to tworzenie i zmiany zasobów w ramach subskrypcji platformy Azure. 

Zarządzanie dostępem do zarządzania platformą Azure za pomocą dostępu warunkowego:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ograniczenie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizycznie lub logicznie segregować aplikacje wysokiego ryzyka

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj aliasów zasad platformy Azure, aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji zasobów platformy Azure. Można również użyć wbudowanych definicji zasad platformy Azure.

Ponadto usługa Azure Resource Manager ma możliwość eksportowania szablonu w języku JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają / przekraczają wymagania dotyczące zabezpieczeń dla twojej organizacji.

Można również użyć zaleceń z usługi Azure Security Center jako linii bazowej bezpiecznej konfiguracji dla zasobów platformy Azure.

Jak wyświetlić dostępne aliasy zasad platformy Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Eksport pojedynczych i wielozasóbowych do szablonu w witrynie Azure portal:https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

Zalecenia dotyczące zabezpieczeń - przewodnik referencyjny:https://docs.microsoft.com/azure/security-center/recommendations-reference



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Ustanawianie bezpiecznych konfiguracji dla systemu operacyjnego

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Obsługa bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure [odmów] i [wdrożyć, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.  Możesz użyć rozwiązań, takich jak śledzenie zmian, pulpit nawigacyjny zgodności zasad lub niestandardowe rozwiązanie, aby łatwo identyfikować zmiany zabezpieczeń w środowisku.

Poznaj efekty zasad platformy Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Śledź zmiany w swoim środowisku za pomocą rozwiązania śledzenia zmian:https://docs.microsoft.com/azure/automation/change-tracking

Uzyskaj dane dotyczące zgodności zasobów platformy Azure:https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Obsługa bezpiecznych konfiguracji dla systemów operacyjnych

**Wskazówki**: Nie dotyczy; niniejsza wytyczna jest przeznaczona dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj repozytorium platformy Azure, aby bezpiecznie przechowywać kod i zarządzać nim, takim jak niestandardowe zasady platformy Azure, szablony usługi Azure Resource Manager, skrypty konfiguracji żądanego stanu itp. Aby uzyskać dostęp do zasobów, którymi zarządzasz w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w usłudze Azure Active Directory (Azure AD), jeśli są zintegrowane z usługą Azure DevOps lub usługą Active Directory, jeśli są zintegrowane z usługą TFS.

Jak przechowywać kod w usłudze Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Uprawnienia i grupy w usłudze Azure DevOps — informacje:https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów platformy Azure przy użyciu zasad platformy Azure. Aliasy zasad platformy Azure umożliwia tworzenie zasad niestandardowych w celu inspekcji lub wymuszenia konfiguracji sieci zasobów platformy Azure. Można również korzystać z wbudowanych definicji zasad związanych z określonych zasobów.  Ponadto można użyć usługi Azure Automation do wdrażania zmian konfiguracji.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak używać aliasów:https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

**Wskazówki:** Użyj aliasów zasad platformy Azure do tworzenia niestandardowych zasad do alertów, inspekcji i wymuszania konfiguracji systemu. Użyj zasad platformy Azure [inspekcja], [odmówić] i [wdrożyć, jeśli nie istnieje], aby automatycznie wymusić konfiguracje zasobów platformy Azure.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="711-securely-manage-azure-secrets"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Szyfrowanie dysków platformy Azure zapewnia szyfrowanie woluminów dla dysków systemu operacyjnego i danych maszyn wirtualnych klastra usługi Azure Data Explorer. Integruje się również z usługą Azure Key Vault, która umożliwia kontrolowanie kluczy szyfrowania dysku i zarządzania nimi oraz zapewnia szyfrowanie wszystkich danych na dyskach maszyn wirtualnych w spoczynku w magazynie Azure.

Jak zabezpieczyć klaster w Eksploratorze danych platformy Azure:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Tożsamości zarządzane umożliwia uwierzytelnianie do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, w tym Usługi Key Vault, bez żadnych poświadczeń w kodzie. Jak skonfigurować tożsamości zarządzane:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Konfigurowanie tożsamości zarządzanych dla klastra usługi Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/managed-identities


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie na poświadczenia

**Wskazówki:** Implementowanie skanera poświadczeń w celu zidentyfikowania poświadczeń w kodzie. Skaner poświadczeń będzie również zachęcać do przenoszenia wykrytych poświadczeń do bezpieczniejszych lokalizacji, takich jak Usługa Azure Key Vault. 

Jak skonfigurować Skaner poświadczeń:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1: Korzystaj z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Eksplorator danych platformy Azure), jednak nie działa na zawartości klienta.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Przed skanowaniem plików, które mają zostać przesłane do zasobów platformy Azure nieoliczonych

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Usługa Azure Data Explorer), jednak nie działa na zawartości klienta.

Wstępnie skanuj dowolną zawartość przekazywanych do nie obliczających zasobów platformy Azure, takich jak Azure Data Explorer, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL itp. Firma Microsoft nie może uzyskać dostępu do danych użytkownika w takich przypadkach.



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie i podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście obsługującym usługi platformy Azure, jednak nie działa na zawartości klienta.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnij regularne automatyczne zapasy zapasów

**Wskazówki:** Dane na koncie magazynu platformy Microsoft Azure używane przez klaster eksploratora danych są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności. Usługa Azure Storage kopiuje dane, dzięki czemu są chronione przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętu, awariami sieci lub zasilania oraz masowymi klęskami żywiołowymi. Można replikować dane w tym samym centrum danych, w strefowych centrach danych w tym samym regionie lub w regionach oddzielonych geograficznie.

Opis nadmiarowości usługi Azure Storage i umów na poziomie usługi:https://docs.microsoft.com/azure/storage/common/storage-redundancy

Eksportowanie danych do magazynu:https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Usługa Azure Data Explorer szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania danych. Klucze zarządzane przez klienta muszą być przechowywane w magazynie kluczy platformy Azure. 

Konfigurowanie kluczy zarządzanych przez klienta przy użyciu języka C#:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu usługi Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Jak wykonać kopię zapasową certyfikatów usługi Azure Key Vault:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Sprawdzanie poprawności wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki:** Okresowo przetestuj przywracanie danych wpisów tajnych usługi Azure Key Vault.

Jak przywrócić certyfikaty usługi Azure Key Vault:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

Konfigurowanie kluczy zarządzanych przez klienta przy użyciu języka C#:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu usługi Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Klient, aby włączyć soft-delete w magazynie kluczy, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.  Można również włączyć ochronę przed przeczyszczeniem, tak aby w przypadku przechowalni lub obiektu w stanie usuniętym nie można go wyczyścić, dopóki nie upłynie okres przechowywania.  

Jak włączyć ochronę usuwania i usuwania podczas usuwania kluczy w magazynie kluczy:https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

Konfigurowanie kluczy zarządzanych przez klienta przy użyciu języka C#:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu usługi Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Reagowanie na incydenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Tworzenie przewodnika po reagowaniu na incydenty

**Wskazówki:** Tworzenie przewodnika po zdarzeniach dla twojej organizacji. Upewnij się, że istnieją pisemne plany reagowania na incydenty, które definiują wszystkie role personelu, a także fazy obsługi incydentów/zarządzania od wykrywania do przeglądu po incydencie.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania zdarzeń i priorytetów

**Wskazówki:** Centrum zabezpieczeń przypisuje ważność do każdego alertu, aby ułatwić ustalanie priorytetów alertów, które powinny być najpierw zbadane. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki: Przeprowadzaj**ćwiczenia, aby przetestować możliwości reagowania na incydenty w systemach w regularnym rytmie, aby chronić zasoby platformy Azure. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Podaj dane kontaktowe incydentu bezpieczeństwa i konfiguruj powiadomienia o alertach &nbsp;dla incydentów bezpieczeństwa

**Wskazówki:** Informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dane użytkownika zostały uzyskiane przez osobę niezgodną z prawem lub nieautoryzowaną. Przejrzyj incydenty po fakcie, aby upewnić się, że problemy zostały rozwiązane.
    

Jak ustawić kontakt zabezpieczeń usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

**Wskazówki:** Eksportuj alerty i zalecenia usługi Azure Security Center za pomocą funkcji ciągłego eksportowania, aby ułatwić identyfikowanie zagrożeń dla zasobów platformy Azure. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów do usługi Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzacja reakcji na alerty bezpieczeństwa

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Aplikacji logiki" w alertach i zaleceniach dotyczących zabezpieczeń w celu ochrony zasobów platformy Azure.
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola bezpieczeństwa: Testy penetracyjne i Ćwiczenia zespołu Czerwonego](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Przeprowadzaj regularne testy penetracyjne zasobów platformy Azure i upewnij się, że w ciągu 60 dni należy skorygować wszystkie krytyczne ustalenia dotyczące zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft: .

Więcej informacji na temat strategii firmy Microsoft i wykonywania testów red teaming i testów penetracji witryn na żywo w oparciu o zarządzał infrastrukturą, usługami i aplikacjami firmy Microsoft w chmurze:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

## <a name="next-steps"></a>Następne kroki

- Zobacz [wzorzec zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [planach bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
