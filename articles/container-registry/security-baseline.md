---
title: Usługa Azure Security Baseline dla rejestru kontenerów platformy Azure
description: Usługa Azure Security Baseline dla rejestru kontenerów platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2970ebf42acb87e93f8b827b3687b219da5867c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244294"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Usługa Azure Security Baseline dla rejestru kontenerów platformy Azure

Usługa Azure Security Baseline for Azure Container Registry zawiera zalecenia, które pomogą Ci poprawić poziom zabezpieczeń wdrożenia.

Punkt odniesienia dla tej usługi jest pobierany z [usługi Azure Security Benchmark w wersji 1.0,](https://docs.microsoft.com/azure/security/benchmarks/overview)która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure za pomocą naszych najlepszych wskazówek dotyczących rozwiązań.

Aby uzyskać więcej informacji, zobacz [omówienie planów bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

**Wskazówki:** Usługa Azure Virtual Network zapewnia bezpieczną, prywatną sieć dla zasobów platformy Azure i lokalnych. Ograniczając dostęp do prywatnego rejestru kontenerów platformy Azure z sieci wirtualnej platformy Azure, upewnij się, że tylko zasoby w sieci wirtualnej uzyskują dostęp do rejestru. W przypadku scenariuszy międzylokacjowych można również skonfigurować reguły zapory, aby zezwolić na dostęp do rejestru tylko z określonych adresów IP. Zza zapory skonfiguruj reguły dostępu do zapory i tagi usług, aby uzyskać dostęp do rejestru kontenerów.

Ogranicz dostęp do rejestru kontenerów platformy Azure przy użyciu reguł sieci wirtualnej lub zapory platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Konfigurowanie reguł w celu uzyskania dostępu do rejestru kontenerów platformy Azure za zaporą:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki:** Skorzystaj z usługi Azure Security Center i koryguj zalecenia dotyczące ochrony sieci, aby chronić zasoby sieciowe na platformie Azure. Włącz dzienniki przepływu nsg i wysyłaj dzienniki do konta magazynu dla inspekcji ruchu.

Jak włączyć dzienniki przepływu nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Ochrona zasobów sieciowych:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki**: Nie dotyczy. Benchmark jest przeznaczony dla usługi Azure App Service lub zasobów obliczeniowych hosting aplikacji sieci web.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Włącz standardową ochronę DDoS w sieciach wirtualnych w celu ochrony przed atakami DDoS. Usługa Azure Security Center Integrated Threat Intelligence umożliwia odmawianie komunikacji ze znanymi złośliwymi lub nieużywanym internetowymi adresami IP.  Wdrażanie zapory platformy Azure w każdej z granic sieci organizacji z włączoną analizą zagrożeń i skonfigurowane jako "Alert i odmów" dla złośliwego ruchu sieciowego.

Możesz użyć dostępu do sieci usługi Azure Security Center just in time, aby skonfigurować sieciowe sieciowe, aby ograniczyć ekspozycję punktów końcowych na zatwierdzone adresy IP przez ograniczony okres. Ponadto użyj narzędzia Azure Security Center Adaptive Network Hardening, aby polecić konfiguracje sieciowej grupy zabezpieczeń, które ograniczają porty i źródłowe interfejsy IP na podstawie analizy rzeczywistego ruchu i zagrożeń.

Jak skonfigurować ochronę przed atakami DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Jak wdrożyć Zaporę platformy Azure:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Poznaj zintegrowaną analizę zagrożeń w centrum zabezpieczeń platformy Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Poznaj program platformy Azure Security Center Adaptacyjne wzmocnienie sieci:https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Kontrola dostępu do sieci usługi Azure Security Center just in time:https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Rejestrowanie pakietów sieciowych i dzienników przepływu

**Wskazówki:** Włącz dzienniki przepływu sieciowej grupy zabezpieczeń (NSG) dla sieciowej grupy zabezpieczeń dołączonej do podsieci używanej do ochrony rejestru kontenerów platformy Azure. Można rejestrować dzienniki przepływu nsg na koncie usługi Azure Storage do generowania rekordów przepływu. Jeśli jest to wymagane do badania nietypowej aktywności, włącz przechwytywanie pakietów usługi Azure Network Watcher.

Jak włączyć dzienniki przepływu nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak włączyć funkcję Obserwatora sieciowego:https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Wdrażanie opartych na sieci systemów wykrywania włamań/włamań (IDS/IPS)

**Wskazówki:** Wybierz ofertę z portalu Azure Marketplace, która obsługuje funkcje IDS/IPS z możliwościami inspekcji ładunku. Jeśli wykrywanie włamań i/lub zapobieganie na podstawie inspekcji ładunku nie jest wymagane, można użyć zapory azure z analizą zagrożeń. Filtrowanie oparte na analizie zagrożeń zapory azure może ostrzegać i odmawiać ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z źródła danych microsoft Threat Intelligence.

Wdrażaj wybrane rozwiązanie zapory w każdej z granic sieci organizacji, aby wykrywać i/lub odrzucać złośliwy ruch.

Azure Marketplace:https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Jak wdrożyć Zaporę platformy Azure:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak skonfigurować alerty za pomocą Zapory platformy Azure:https://docs.microsoft.com/azure/firewall/threat-intel


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki**: Nie dotyczy. Benchmark jest przeznaczony dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i administracyjnych narzutów administracyjnych dotyczących zasad bezpieczeństwa sieci

**Wskazówki:** W przypadku zasobów, które wymagają dostępu do rejestru kontenerów, użyj tagów usługi sieci wirtualnej dla usługi Azure Container Registry do definiowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub zaporze azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi "AzureContainerRegistry" w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów.

Zezwalaj na dostęp według tagu usługi:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z rejestrami kontenerów platformy Azure za pomocą zasad platformy Azure. Użyj aliasów zasad platformy Azure w obszarach nazw "Microsoft.ContainerRegistry" i "Microsoft.Network", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji sieciowej rejestrów kontenerów. 

Planów platformy Azure można użyć, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie artefaktów środowiska kluczy, takich jak szablony usługi Azure Resource Manager, formanty RBAC i zasady, w jednej definicji planu. Łatwo zastosować plan do nowych subskrypcji i dostosować kontrolę i zarządzanie poprzez przechowywanie wersji.

Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu zasad platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak utworzyć plan platformy Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1.10: Przepisy konfiguracji ruchu dokumentów

**Wskazówki:** Klient może używać planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez artefakty kluczowe środowisko pakowania, takie jak szablony usługi Azure Resource Manager, formanty RBAC i zasady, w jednej definicji planu. Łatwo zastosować plan do nowych subskrypcji i dostosować kontrolę i zarządzanie poprzez przechowywanie wersji.

Jak utworzyć plan platformy Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z rejestrami kontenerów. Tworzenie alertów w usłudze Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak tworzyć alerty w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Korzystanie z zatwierdzonych źródeł synchronizacji czasu

**Wskazówki:** Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure, jednak masz możliwość zarządzania ustawieniami synchronizacji czasu dla zasobów obliczeniowych.

Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych platformy Azure:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Pozyskiwania dzienników za pośrednictwem usługi Azure Monitor do agregowania danych zabezpieczeń generowanych przez rejestr kontenerów platformy Azure. W usłudze Azure Monitor użyj obszaru roboczego analizy dzienników do wykonywania zapytań i wykonywania analiz oraz użyj kont usługi Azure Storage do długoterminowego/archiwizacji magazynu.

Dzienniki rejestru kontenerów platformy Azure do oceny diagnostycznej i inspekcji:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Włączanie rejestrowania inspekcji zasobów platformy Azure

**Wskazówki:** Usługa Azure Monitor zbiera dzienniki zasobów (dawniej nazywane dziennikami diagnostycznymi) dla zdarzeń opartych na użytkownikach w rejestrze. Zbieranie i korzystanie z tych danych do inspekcji zdarzeń uwierzytelniania rejestru i zapewnić pełny ślad działania na artefakty rejestru, takie jak ściąganie i wypychanie zdarzeń, dzięki czemu można zdiagnozować problemy z zabezpieczeniami z rejestru.

Dzienniki rejestru kontenerów platformy Azure do oceny diagnostycznej i inspekcji:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki**: Nie dotyczy. Benchmark jest przeznaczony dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** W usłudze Azure Monitor ustaw okres przechowywania obszaru roboczego usługi Log Analytics zgodnie z przepisami dotyczącymi zgodności obowiązującymi w organizacji. Użyj kont usługi Azure Storage dla magazynu długoterminowego/archiwizacji.

Jak ustawić parametry przechowywania dzienników dla obszarów roboczych usługi Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorowanie i przeglądanie dzienników

**Wskazówki:** Analizuj i monitoruj dzienniki rejestru kontenerów platformy Azure pod kątem nietypowego zachowania i regularnie przeglądaj wyniki. Użyj obszaru roboczego analizy dzienników usługi Azure Monitor, aby przeglądać dzienniki i wykonywać kwerendy dotyczące danych dziennika.

Dzienniki rejestru kontenerów platformy Azure do oceny diagnostycznej i inspekcji:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Opis obszaru roboczego analizy dzienników:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak wykonywać zapytania niestandardowe w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Włączanie alertów o nietypowej aktywności

**Wskazówki:** Użyj obszaru roboczego usługi Azure Log Analytics do monitorowania i alertów o nietypowych działaniach w dziennikach zabezpieczeń i zdarzeniach związanych z rejestrem kontenerów platformy Azure.

Dzienniki rejestru kontenerów platformy Azure do oceny diagnostycznej i inspekcji:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Jak ostrzegać o danych dziennika analizy dziennika:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizuj rejestrowanie przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy. Usługa Azure Container Registry nie przetwarza ani nie generuje dzienników związanych ze złośliwym oprogramowaniem.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Nie dotyczy. Usługa Azure Container Registry jest punktem końcowym i nie inicjuje komunikacji, a usługa nie wysyła zapytań do systemu DNS.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włącz rejestrowanie inspekcji wiersza polecenia

**Wskazówki**: Nie dotyczy. Benchmark jest przeznaczony dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Kontrola tożsamości i dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Prowadzenie spisu rachunków administracyjnych

**Wskazówki:** Usługa Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i są możliwe do queryable. Użyj modułu programu Azure AD PowerShell do wykonywania zapytań ad hoc w celu odnajdowania kont, które są członkami grup administracyjnych.

Dla każdego rejestru kontenerów platformy Azure należy śledzić, czy wbudowane konto administratora jest włączone czy wyłączone. Wyłącz konto, gdy nie jest używane.

Jak uzyskać rolę katalogu w usłudze Azure AD za pomocą programu PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak uzyskać członków roli katalogu w usłudze Azure AD za pomocą programu PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Konto administratora rejestru kontenerów platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3.2: W stosownych przypadkach zmieniaj domyślne hasła

**Wskazówki:** Usługa Azure Active Directory (Azure AD) nie ma pojęcia haseł domyślnych. Inne zasoby platformy Azure wymagające hasła wymuszają na tworzenie hasła z wymaganiami dotyczącymi złożoności i minimalną długością hasła, które różnią się w zależności od usługi. Użytkownik jest odpowiedzialny za aplikacje innych firm i usługi Marketplace, które mogą używać haseł domyślnych.

Jeśli domyślne konto administratora rejestru kontenerów platformy Azure jest włączone, złożone hasła są tworzone automatycznie i powinny być obracane. Wyłącz konto, gdy nie jest używane.

Konto administratora rejestru kontenerów platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Korzystanie z dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych rachunków administracyjnych. Usługa Azure Security Center Identity and Access Management służy do monitorowania liczby kont administracyjnych.

Ponadto należy utworzyć procedury, aby włączyć wbudowane konto administratora rejestru kontenerów. Wyłącz konto, gdy nie jest używane.

Poznaj tożsamość i dostęp usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Konto administratora rejestru kontenerów platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Używanie logowania jednokrotnego (Logowanie jednokrotne) w usłudze Azure Active Directory

**Wskazówki:** Tam, gdzie to możliwe, użyj logowania samouśowego usługi Azure Active Directory zamiast konfigurowania indywidualnych poświadczeń autonomicznych na usługę. Użyj zaleceń dotyczących tożsamości i zarządzania dostępem w usłudze Azure Security Center.

Aby uzyskać indywidualny dostęp do rejestru kontenerów, użyj indywidualnego logowania zintegrowanego z usługą Azure Active Directory.

Poznaj ujedniaka ujednawcze za pomocą usługi Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Indywidualne logowanie do rejestru kontenerów:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (Azure AD) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem usługi Azure Security Center.

Jak włączyć usługę MFA na platformie Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorować tożsamość i dostęp w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Używanie dedykowanych maszyn (stacje robocze z uprzywilejowanym dostępem) do wszystkich zadań administracyjnych

**Wskazówki:** Użyj paws (uprzywilejowanych stacji roboczych dostępu) z usługi MFA skonfigurowany do logowania się i konfigurowania zasobów platformy Azure.

Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak włączyć usługę MFA na platformie Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitorowanie usługi Azure Security Center:** Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Rejestrowanie i ostrzeganie o podejrzanych działaniach z kont administracyjnych

**Wskazówki:** Użyj raportów zabezpieczeń usługi Azure Active Directory (Azure AD) do generowania dzienników i alertów, gdy w środowisku występuje podejrzane lub niebezpieczne działanie. Usługa Azure Security Center służy do monitorowania tożsamości i dostępu do aktywności.

Jak zidentyfikować użytkowników usługi Azure AD oflagowanych pod kątem ryzykownych działań:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorować tożsamość użytkowników i dostęp do aktywności w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj lokalizacji nazwanych dostępu warunkowego, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

Jak skonfigurować nazwane lokalizacje na platformie Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3.9: Korzystanie z usługi Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych w spoczynku i podczas przesyłania. Usługa Azure AD również sole, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

Jak utworzyć i skonfigurować wystąpienie usługi Azure AD:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Usługa Azure Active Directory (Azure AD) udostępnia dzienniki ułatwiające odnajdowanie starych kont. Ponadto użyj przeglądów dostępu do tożsamości platformy Azure, aby skutecznie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika można regularnie przeglądać, aby upewnić się, że tylko prawo Użytkownicy mają stały dostęp.

Opis raportowania usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak korzystać z przeglądów dostępu do tożsamości platformy Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych kont

**Wskazówki:** Masz dostęp do działania logowania usługi Azure Active Directory (Azure AD), źródeł dzienników inspekcji i zdarzeń ryzyka, które umożliwiają integrację z dowolnym narzędziem do zarządzania informacjami o zabezpieczeniach i zdarzeniami (SIEM) /Monitorowanie.

Możesz usprawnić ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure Active Directory i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Można skonfigurować żądane alerty w obszarze roboczym analizy dzienników.

Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

**Wskazówki:** Użyj funkcji ochrony przed ryzykiem i tożsamością usługi Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. 

Jak wyświetlić ryzykowne logowania usługi Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak skonfigurować i włączyć zasady ryzyka ochrony tożsamości:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki**: Niedostępne; Skrytka klienta nie jest obecnie obsługiwana dla rejestru kontenerów platformy Azure.

Lista obsługiwanych usług skrytki klienta:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Prowadzenie wykazu informacji poufnych

**Wskazówki:** Użyj tagów zasobów, aby pomóc w śledzeniu rejestrów kontenerów platformy Azure, które przechowują lub przetwarzają poufne informacje.

Oznaczanie i wersje obrazów kontenerów lub innych artefaktów w rejestrze oraz blokowanie obrazów lub repozytoriów, aby ułatwić śledzenie obrazów przechowujących lub przetwarzających poufne informacje.

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Zalecenia dotyczące oznaczania i przechowywania obrazów kontenerów:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Blokowanie obrazu kontenera w rejestrze kontenerów platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemy izolowania przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Implementowanie oddzielnych rejestrów kontenerów, subskrypcji i/lub grup zarządzania do zarządzania, testowania i produkcji. Zasoby przechowujące lub przetwarzające dane wrażliwe powinny być wystarczająco odizolowane.

Zasoby powinny być oddzielone siecią wirtualną lub podsiecią, odpowiednio oznakowane i zabezpieczone przez sieciową grupę zabezpieczeń (NSG) lub Zaporę platformy Azure.

Jak utworzyć dodatkowe subskrypcje platformy Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Ogranicz dostęp do rejestru kontenerów platformy Azure przy użyciu reguł sieci wirtualnej lub zapory platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Jak utworzyć nsg z config zabezpieczeń:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak wdrożyć Zaporę platformy Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak skonfigurować alerty lub alerty i odmówić za pomocą Zapory platformy Azure:

https://docs.microsoft.com/azure/firewall/threat-intel



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego przesyłania poufnych informacji

**Wskazówki**: Wdrożenie zautomatyzowanego narzędzia na obwodzie sieci, które monitoruje nieautoryzowane przesyłanie poufnych informacji i blokuje takie transfery, jednocześnie ostrzegając specjalistów od bezpieczeństwa informacji.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współdzielone

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfrowanie wszystkich poufnych informacji podczas przesyłania

**Wskazówki:** Upewnij się, że klienci łączący się z rejestrem kontenerów platformy Azure mogą negocjować tls 1.2 lub więcej. Zasoby platformy Microsoft Azure domyślnie negocjują usługę TLS 1.2.

W stosownych przypadkach należy postępować zgodnie z zaleceniami usługi Azure Security Center dotyczącymi szyfrowania w spoczynku i szyfrowania podczas przesyłania.

Poznaj szyfrowanie podczas przesyłania za pomocą platformy Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Współdzielone

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Identyfikowanie poufnych danych za pomocą aktywnego narzędzia do wykrywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla rejestru kontenerów platformy Azure. Implementowanie rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Sterowanie dostępem do zasobów za pomocą narzędzia Azure RBAC

**Wskazówki:** Użyj usługi Azure Active Directory (Azure AD) RBAC do kontrolowania dostępu do danych i zasobów w rejestrze kontenerów platformy Azure. 

Jak skonfigurować rbac na platformie Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Role i uprawnienia rejestru kontenerów platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Wymuszanie kontroli dostępu za pomocą funkcji zapobiegania utracie danych opartych na hoście

**Wskazówki**: Jeśli jest to wymagane dla zgodności zasobów obliczeniowych, należy wdrożyć narzędzie innej firmy, takie jak zautomatyzowane rozwiązanie do zapobiegania utracie danych oparte na hoście, aby wymusić kontrolę dostępu do danych, nawet gdy dane są kopiowane z systemu.

W przypadku podstawowej platformy, która jest zarządzana przez firmę Microsoft, firma Microsoft traktuje całą zawartość klienta jako wrażliwą i dokłga mierze chroni przed utratą i ekspozycją danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje zestaw niezawodnych mechanizmów kontroli i możliwości ochrony danych.

Poznaj ochronę danych klientów na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Użyj szyfrowania w spoczynku na wszystkich zasobach platformy Azure. Domyślnie wszystkie dane w rejestrze kontenerów platformy Azure są szyfrowane w spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft.

Poznaj szyfrowanie w spoczynku na platformie Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Klucze zarządzane przez klienta w rejestrze kontenerów platformy Azure:https://aka.ms/acr/cmk



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

**Wskazówki:** Usługa Azure Monitor zbiera dzienniki zasobów (dawniej nazywane dziennikami diagnostycznymi) dla zdarzeń opartych na użytkownikach w rejestrze. Zbieranie i korzystanie z tych danych do inspekcji zdarzeń uwierzytelniania rejestru i zapewnić pełny dziennik aktywności na artefakty rejestru, takie jak ściąganie i ściąganie zdarzeń, dzięki czemu można zdiagnozować problemy operacyjne z rejestru.

Dzienniki rejestru kontenerów platformy Azure do oceny diagnostycznej i inspekcji:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Postępuj zgodnie z zaleceniami usługi Azure Security Center dotyczącymi przeprowadzania ocen luk w zabezpieczeniach na obrazach kontenerów. Opcjonalnie wdrażaj rozwiązania innych firm z usługi Azure Marketplace w celu przeprowadzania ocen luk w zabezpieczeniach obrazu.

Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Integracja usługi Azure Container Registry z Centrum zabezpieczeń (wersja zapoznawcza):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki:** Firma Microsoft wykonuje zarządzanie poprawkami w podstawowych systemach, które obsługują usługę Azure Container Registry.

Zautomatyzuj aktualizacje obrazu kontenera po wykryciu aktualizacji obrazów bazowych z systemu operacyjnego i innych poprawek.

Informacje o aktualizacjach obrazów podstawowych dla zadań rejestru kontenerów platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

**Wskazówki:** Można użyć rozwiązania innych firm do poprawiania obrazów aplikacji.  Ponadto można uruchomić zadania rejestru kontenerów platformy Azure, aby zautomatyzować aktualizacje obrazów aplikacji w rejestrze kontenerów na podstawie poprawek zabezpieczeń lub innych aktualizacji w obrazach podstawowych.

Informacje o aktualizacjach obrazów podstawowych dla zadań usługi ACR:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu back-to-back

**Wskazówki:** Integracja usługi Azure Container Registry (ACR) z usługą Azure Security Center w celu umożliwienia okresowego skanowania obrazów kontenerów w poszukiwaniu luk w zabezpieczeniach. Opcjonalnie wdrażaj rozwiązania innych firm z usługi Azure Marketplace w celu wykonywania okresowych skanowania luk w zabezpieczeniach obrazu.

Integracja usługi Azure Container Registry z Centrum zabezpieczeń (wersja zapoznawcza):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Użyj procesu oceny ryzyka, aby nadać priorytet usuwaniu wykrytych luk w zabezpieczeniach

**Wskazówki:** Integracja usługi Azure Container Registry (ACR) z usługą Azure Security Center w celu umożliwienia okresowego skanowania obrazów kontenerów w poszukiwaniu luk w zabezpieczeniach i klasyfikacji ryzyka. Opcjonalnie wdrażaj rozwiązania innych firm z usługi Azure Marketplace, aby wykonywać okresowe skanowanie luk w zabezpieczeniach obrazu i klasyfikację ryzyka.

Integracja usługi Azure Container Registry z Centrum zabezpieczeń (wersja zapoznawcza):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitorowanie usługi Azure Security Center:** Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie zapasami i zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Korzystanie z odnajdowania zasobów platformy Azure

**Wskazówki:** Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywać wszystkie zasoby (takie jak zasoby obliczeniowe, magazyn, sieć, porty i protokoły itp.) w ramach subskrypcji.  Upewnij się, odpowiednie (odczytu) uprawnienia w dzierżawie i wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach subskrypcji.

Chociaż klasyczne zasoby platformy Azure mogą zostać wykryte za pomocą wykresu zasobów, zdecydowanie zaleca się tworzenie i używanie zasobów usługi Azure Resource Manager w przyszłości.

Jak tworzyć zapytania za pomocą programu Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Poznaj usługę Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6.2: Obsługa metadanych zasobów

**Wskazówki:** Usługa Azure Container Registry przechowuje metadane, w tym tagi i manifesty obrazów w rejestrze. Postępuj zgodnie z zalecanymi praktykami tagowania artefaktów.

Informacje o rejestrach, repozytoriach i obrazach:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Zalecenia dotyczące oznaczania i przechowywania obrazów kontenerów:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Usługa Azure Container Registry przechowuje metadane, w tym tagi i manifesty obrazów w rejestrze. Postępuj zgodnie z zalecanymi praktykami tagowania artefaktów.

Informacje o rejestrach, repozytoriach i obrazach:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Zalecenia dotyczące oznaczania i przechowywania obrazów kontenerów:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Obsługa spisu zatwierdzonych zasobów i tytułów oprogramowania platformy Azure

**Wskazówki:** Należy utworzyć spis zatwierdzonych zasobów platformy Azure zgodnie z potrzebami organizacji.  

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w ramach subskrypcji.

Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywanie zasobów w ramach ich subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu zasad platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą programu Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w zasobach obliczeniowych

**Wskazówki:** Analizuj i monitoruj dzienniki rejestru kontenerów platformy Azure pod kątem nietypowego zachowania i regularnie przeglądaj wyniki. Użyj obszaru roboczego analizy dzienników usługi Azure Monitor, aby przeglądać dzienniki i wykonywać kwerendy dotyczące danych dziennika.

Dzienniki rejestru kontenerów platformy Azure do oceny diagnostycznej i inspekcji:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Opis obszaru roboczego analizy dzienników:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak wykonywać zapytania niestandardowe w usłudze Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Usuwanie niezatwierdzonych zasobów platformy Azure i aplikacji

**Wskazówki:** Usługa Azure Automation zapewnia pełną kontrolę podczas wdrażania, operacji i likwidacji obciążeń i zasobów.  Można zaimplementować własne rozwiązanie do usuwania nieautoryzowanych zasobów platformy Azure. Wprowadzenie do usługi Azure Automation:https://docs.microsoft.com/azure/automation/automation-intro


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="68-use-only-approved-applications"></a>6.8: Używaj wyłącznie zatwierdzonych aplikacji

**Wskazówki**: Nie dotyczy. Benchmark jest przeznaczony dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="69-use-only-approved-azure-services"></a>6.9: Korzystaj tylko z zatwierdzonych usług platformy Azure

**Wskazówki:** Wykorzystaj zasady platformy Azure, aby ograniczyć usługi, które można aprowizować w swoim środowisku.

Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu zasad platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu za pomocą usługi Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6.10: Wdrożenie listy zatwierdzonych wniosków

**Wskazówki**: Nie dotyczy. Benchmark jest przeznaczony dla zasobów obliczeniowych.



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Ogranicz możliwość interakcji użytkowników z usługą AzureResources Manager za pomocą skryptów

**Wskazówki:** Użyj konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć zdolność użytkowników do wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure.

Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resources Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ograniczenie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

**Wskazówki:** Użyj konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć zdolność użytkowników do wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure.

Na przykład sposób sterowania wykonywaniem skryptów programu PowerShell w środowiskach systemu Windows:https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizycznie lub logicznie segregować aplikacje wysokiego ryzyka

**Wskazówki:** Oprogramowanie, które jest wymagane dla operacji biznesowych, ale może nieść większe ryzyko dla organizacji, powinno być odizolowane w obrębie własnej maszyny wirtualnej i/lub sieci wirtualnej i wystarczająco zabezpieczone za pomocą zapory azure lub sieciowej grupy zabezpieczeń.

Jak utworzyć sieć wirtualną:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć nsg z config zabezpieczeń:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj usługi Azure Policy lub Usługi Azure Security Center, aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów platformy Azure.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu zasad platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Ustanawianie bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki:** Skorzystaj z zalecenia usługi Azure Security Center "Korygowanie luk w zabezpieczeniach konfiguracji na maszynach wirtualnych", aby zachować konfiguracje zabezpieczeń na wszystkich zasobach obliczeniowych.

Jak monitorować zalecenia usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-recommendations

Jak skorygować zalecenia usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure [odmów] i [wdrożyć, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu zasad platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Poznaj efekty zasad platformy Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy. Benchmark jest przeznaczony dla zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych definicji zasad platformy Azure, użyj usługi Azure Reppos, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja repozytorium platformy Azure:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

**Wskazówki**: Nie dotyczy. Wskaźnik porównawczy ma zastosowanie do zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki:** Użyj zasad platformy Azure do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu zasad platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak skonfigurować zasady platformy Azure i zarządzać nimi:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: Nie dotyczy. Wskaźnik porównawczy ma zastosowanie do zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

**Wskazówki:** Użyj usługi Azure Security Center do wykonywania skanowania według planu bazowego dla zasobów platformy Azure.

Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w ramach subskrypcji.

Jak skorygować zalecenia w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Inspekcja zgodności rejestrów kontenerów platformy Azure przy użyciu zasad platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy. Wskaźnik porównawczy ma zastosowanie do zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie kluczami tajnymi dla aplikacji w chmurze.

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć przechowalnię kluczy:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zapewnić uwierzytelnianie usługi Key Vault z tożsamością zarządzaną:https://docs.microsoft.com/azure/key-vault/managed-identity

Użyj tożsamości zarządzanej przez platformę Azure w zadaniach rejestru kontenerów platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Tożsamości zarządzane umożliwia uwierzytelnianie do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, w tym Usługi Key Vault, bez żadnych poświadczeń w kodzie.

Jak skonfigurować tożsamości zarządzane:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Użyj tożsamości zarządzanej do uwierzytelniania w rejestrze kontenerów platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie na poświadczenia

**Wskazówki:** Implementowanie skanera poświadczeń w celu zidentyfikowania poświadczeń w kodzie. Skaner poświadczeń będzie również zachęcać do przenoszenia wykrytych poświadczeń do bezpieczniejszych lokalizacji, takich jak Usługa Azure Key Vault.

Jak skonfigurować Skaner poświadczeń:https://secdevtools.azurewebsites.net/helpcredscan.html


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Korzystanie z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki:** Użyj ochrony przed złośliwym oprogramowaniem firmy Microsoft dla usług w chmurze i maszyn wirtualnych platformy Azure, aby stale monitorować i bronić zasobów. W przypadku systemu Linux użyj rozwiązania ochrony przed złośliwym oprogramowaniem innych firm.

Jak skonfigurować oprogramowanie antywirusowe microsoft dla usług w chmurze i maszyn wirtualnych:https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Przed skanowaniem plików, które mają zostać przesłane do zasobów platformy Azure nieoliczonych

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Usługa Azure Container Registry), jednak nie działa na zawartości klienta.

Wstępnie skanuj wszystkie pliki przekazywane do nieobliczalnych zasobów platformy Azure, takich jak usługa App Service, magazyn usługi Data Lake, magazyn obiektów Blob itp.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie i podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane

**Wskazówki**: Nie dotyczy. Benchmark jest przeznaczony dla zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym oprogramowaniem dla podstawowej platformy.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnienie regularnych automatycznych kopii zapasowych

**Wskazówki:** Dane w rejestrze kontenerów platformy Microsoft Azure są zawsze automatycznie replikowane, aby zapewnić trwałość i wysoką dostępność. Usługa Azure Container Registry kopiuje dane, dzięki czemu są chronione przed planowanymi i nieplanowanymi zdarzeniami

Opcjonalnie replikuj geograficznie rejestr kontenerów w celu obsługi replik rejestru w wielu regionach platformy Azure. 

Replikacja geograficzna w rejestrze kontenerów platformy Azure:https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Opcjonalnie wykonać kopii zapasowej obrazów kontenerów, importując z jednego rejestru do drugiego.

Tworzenie kopii zapasowych kluczy zarządzanych przez klienta w usłudze Azure Key Vault przy użyciu narzędzi wiersza polecenia platformy Azure lub zestawów SDK.

Importowanie obrazów kontenerów do rejestru kontenerów:https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Jak wykonać kopię zapasową kluczy magazynu kluczy na platformie Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Sprawdzanie poprawności wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki:** Przetestuj przywracanie kopii zapasowych kluczy zarządzanych przez klienta w usłudze Azure Key Vault przy użyciu narzędzi wiersza polecenia platformy Azure lub zestawów SDK.

Jak przywrócić klucze usługi Azure Key Vault na platformie Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Można włączyć soft-delete w usłudze Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Jak włączyć usuwanie programowe w przechowalni kluczy:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Reagowanie na incydenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Tworzenie przewodnika po zdarzeniach

**Wskazówki:** Tworzenie przewodnika po zdarzeniach dla twojej organizacji. Upewnij się, że istnieją pisemne plany reagowania na incydenty, które definiują wszystkie role personelu, a także fazy obsługi incydentów/zarządzania od wykrywania do przeglądu po incydencie.

Jak skonfigurować automatyzacje przepływu pracy w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Wskazówki dotyczące tworzenia własnego procesu reagowania na incydenty bezpieczeństwa:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia incydentu w centrum reagowania na zabezpieczenia firmy Microsoft:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Klient może również wykorzystać przewodnik obsługi incydentów związanych z bezpieczeństwem komputerowym NIST, aby pomóc w stworzeniu własnego planu reagowania na incydenty:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania incydentów i ustalania priorytetów

**Wskazówki:** Usługa Azure Security Center przypisuje ważność do każdego alertu, aby ułatwić ustalanie priorytetów alertów, które powinny być najpierw zbadane. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje.

Dodatkowo wyraźnie zaznacz subskrypcje (np. nieprod) i utworzyć system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure.


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki**: Przeprowadzaj ćwiczenia, aby przetestować możliwości reagowania na incydenty w systemach na regularnym rytmie. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu.

Zapoznaj się z publikacją NIST: Przewodnik po testach, szkoleniach i programach ćwiczeń dla planów i możliwości IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Podaj dane kontaktowe zdarzeń bezpieczeństwa i konfiguruj powiadomienia o alertach dla incydentów bezpieczeństwa

**Wskazówki:** Informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dane klienta zostały uzyskiane przez osobę niezgodną z prawem lub nieautoryzowaną.  Przejrzyj incydenty po fakcie, aby upewnić się, że problemy zostały rozwiązane.

Jak ustawić kontakt zabezpieczeń usługi Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

**Wskazówki:** Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji ciągłego eksportowania. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów Sentinel.

Jak skonfigurować eksport ciągły:https://docs.microsoft.com/azure/security-center/continuous-export

Jak przesyłać strumieniowo alerty do usługi Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzacja reakcji na alerty bezpieczeństwa

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Aplikacji logiki" w alertach i zaleceniach dotyczących zabezpieczeń.

Jak skonfigurować automatyzację przepływu pracy i aplikacje logiki:https://docs.microsoft.com/azure/security-center/workflow-automation


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola bezpieczeństwa: Testy penetracyjne i Ćwiczenia zespołu Czerwonego](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Przeprowadzaj regularne testy penetracyjne zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki:** Postępuj zgodnie z regułami zaangażowania firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Więcej informacji na temat strategii firmy Microsoft i wykonywania testów red teaming i testów penetracji witryn na żywo w infrastrukturze, usługach i aplikacjach zarządzanych przez firmę Microsoft można znaleźć tutaj:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

## <a name="next-steps"></a>Następne kroki

- Zobacz [wzorzec zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [planach bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
