---
title: Zabezpieczanie zdalnej administracji bramą w Australii platformy Azure
description: Wskazówki dotyczące konfigurowania bezpiecznego administrowania zdalnego w regionach australijskich w celu spełnienia określonych wymagań australijskich zasad, przepisów i przepisów obowiązujących dla instytucji rządowych.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 1e4c4712312faf2274a4a0737c4fc1f7ce39f98e
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824189"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Zabezpieczanie zdalnej administracji bramą w Australii platformy Azure

Jest to istotne dla dostępności i integralności każdego systemu, że działania administracyjne są wykonywane bezpiecznie i są kontrolowane. Działania administracyjne powinny być wykonywane z poziomu bezpiecznego urządzenia, za pośrednictwem bezpiecznego połączenia i są obsługiwane przez silne procesy uwierzytelniania i autoryzacji. Bezpieczne administrowanie zdalną gwarantuje, że tylko autoryzowane akcje są wykonywane i tylko przez uprawnionych administratorów.

Ten artykuł zawiera informacje na temat implementowania bezpiecznej funkcji administracji zdalnej dla systemu dostępnego dla Internetu hostowanego na platformie Azure, który jest zgodny ze wskazówkami dotyczącymi konsumenta australijskiej cybernetycznymi Security Centre (ACSC) oraz intencją informacji ACSC Ręczna aktualizacja zabezpieczeń (ISM).

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Australijskie wymagania dotyczące cybernetycznymi Security Centre (ACSC)

Ogólne wymagania dotyczące zabezpieczeń systemów Wspólnoty są zdefiniowane w ISM. Aby pomóc organom Wspólnoty w dostarczaniu bezpiecznego administrowania, ACSC [opublikował ACSC Protect: Bezpieczna Administracja](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

W tym dokumencie omówiono znaczenie bezpiecznego administrowania i zaproponowano jedną z metod implementowania bezpiecznego środowiska administracyjnego. W tym dokumencie opisano elementy bezpiecznego rozwiązania do administrowania w następujący sposób:

|Element   |Opis   |
|---|---|
|Uprzywilejowana kontrola dostępu   |Kontrolowanie dostępu do uprzywilejowanych kont to podstawowa kontrola zabezpieczeń, która umożliwia ochronę uprzywilejowanych kont przed nieprawidłowym użyciem. Metodologia kontroli dostępu obejmie koncepcje "najniższych uprawnień" i "potrzeba", jak również procesów i procedur do zarządzania kontami usług i przesunięciami pracowników.   |
|Uwierzytelnianie wieloskładnikowe   |Zaimplementowanie dodatkowych czynników uwierzytelniania poza nazwami użytkowników i hasłami, takimi jak tokeny fizyczne lub karty inteligentne, może pomóc w ochronie najważniejszych zasobów. Jeśli atakującej nastąpi naruszenie poświadczeń dla kont uprzywilejowanych, ponieważ wszystkie akcje administracyjne chcą najpierw wykonać kilka form uwierzytelniania wieloskładnikowego, konsekwencje mogą być znacznie ograniczone.|
|Uprzywilejowane stacje robocze|Użycie znanego bezpiecznego środowiska na potrzeby zadań administracyjnych może skutkować mniejszym ryzykiem naruszenia bezpieczeństwa sieci z powodu implementacji dodatkowych kontroli zabezpieczeń.|
|Rejestrowanie i inspekcja   |Automatyczne generowanie, zbieranie i analiza zdarzeń związanych z zabezpieczeniami i administracyjnem ze stacji roboczych, serwerów, urządzeń sieciowych i przeskoków umożliwi wykrywanie kompromisów i próby złamania zabezpieczeń. Automatyzacja umożliwia organizacjom szybkie reagowanie, zmniejszając wpływ naruszenia.|
|Segmentacja sieci i rozdzielenie|Segmentacja sieci na strefy logiczne, takie jak różne domeny zabezpieczeń, i dalsze segregowanie tych sieci logicznych przez ograniczenie typów danych, które są przesyłane z jednej strefy do drugiej, ogranicza ruchy boczne. Segmentacja uniemożliwia atakującejemu uzyskanie dostępu do dodatkowych zasobów.|
|Pola skoku|Pole skoku to serwer dostępu zdalnego ze wzmocnionymi zabezpieczeniami, powszechnie korzystający z oprogramowania Usługi pulpitu zdalnego lub Secure Shell (SSH) firmy Microsoft. Pola skoku działają jako punkt taktowania dla administratorów uzyskujących dostęp do krytycznych systemów ze wszystkimi działaniami administracyjnymi wykonywanymi z poziomu dedykowanego hosta.|

Ten artykuł zawiera architekturę referencyjną, w której powyższe elementy mogą być używane do bezpiecznego administrowania systemami wdrożonymi na platformie Azure.

## <a name="architecture"></a>Architektura

Zapewnienie bezpiecznego zarządzania wymaga wielu składników, które współpracują ze sobą w celu utworzenia spójnego rozwiązania. W przypadku udostępnionej architektury referencyjnej składniki są mapowane na elementy opisane w [ACSC Chroń: Bezpieczna Administracja](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

![Architektura bezpiecznej administracji zdalnej platformy Azure](media/remote-admin.png)

## <a name="components"></a>Składniki

Architektura została zaprojektowana w celu zapewnienia, że konto uprzywilejowane ma przyznane tylko niezbędne uprawnienia, jest bezpiecznie identyfikowane i zapewnia dostęp do interfejsów administracyjnych tylko z autoryzowanego urządzenia i za pośrednictwem bezpiecznej komunikacji mechanizmy, które są kontrolowane i objęte inspekcją.

|Rozwiązanie| Składniki|Elementy|
|---|---|---|
|Zabezpieczanie urządzeń |<ul><li>Uprzywilejowana stacja robocza</li><li>Urządzenie przenośne</li><li>Microsoft Intune</li><li>Zasady grupy</li><li>Serwer skoku/Host bastionu</li><li>Administracja just in Time (JIT)</li></ul> |<ul><li>Uprzywilejowane stacje robocze</li><li>Pola skoku</li></ul>|
|Komunikacja zabezpieczona |<ul><li>Azure Portal</li><li>Azure VPN Gateway</li><li>Brama Pulpit zdalny (RD)</li><li>Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)</li></ul> |<ul><li>Segmentacja sieci i rozdzielenie</li></ul>|
|Silne uwierzytelnianie |<ul><li>Kontroler domeny (DC)</li><li>Azure Active Directory (Azure AD)</li><li>Serwer zasad sieciowych (NPS)</li><li>Azure MFA</li></ul> |<ul><li>Uwierzytelnianie wieloskładnikowe</li></ul> |
|Silne autoryzacja |<ul><li>Zarządzanie tożsamościami i dostępem (IAM)</li><li>Privileged Identity Management (PIM)</li><li>Dostęp warunkowy</li></ul>|<ul><li>Uprzywilejowana kontrola dostępu</li></ul>|
|||

>[!NOTE]
>Aby uzyskać więcej informacji dotyczących elementów rejestrowania i inspekcji, zobacz artykuł dotyczący [rejestrowania, inspekcji i widoczności bramy](gateway-log-audit-visibility.md) .

## <a name="administration-workflow"></a>Przepływ pracy administracji

Administrowanie systemami wdrożonymi na platformie Azure jest podzielone na dwie różne kategorie, administrując konfiguracją platformy Azure i administrowaniem obciążeniami wdrożonymi na platformie Azure. Konfiguracja platformy Azure odbywa się za pośrednictwem Azure Portal i administrowanie obciążeniami odbywa się za pośrednictwem mechanizmów administracyjnych, takich jak Remote Desktop Protocol (RDP), Secure Shell (SSH) lub PaaS, przy użyciu narzędzi, takich jak SQL Management Studio.

Uzyskanie dostępu do administracji polega na wieloetapowym procesie obejmującym składniki wymienione w architekturze i wymaganiu dostępu do Azure Portal i konfiguracji platformy Azure przed uzyskaniem dostępu do obciążeń platformy Azure.

>[!NOTE]
> Kroki opisane w tym miejscu to ogólny proces przy użyciu składników graficznego interfejsu użytkownika (GUI) platformy Azure. Te kroki można również wykonać przy użyciu innych interfejsów, takich jak PowerShell.

### <a name="azure-configuration-and-azure-portal-access"></a>Konfiguracja platformy Azure i dostęp Azure Portal

|Krok |Opis |
|---|---|
|Logowanie uprzywilejowanych stacji roboczej |Administrator loguje się na uprzywilejowanej stacji roboczej przy użyciu poświadczeń administracyjnych. Formanty zasady grupy uniemożliwiają uwierzytelnianie kont innych niż administracyjne na uprzywilejowanej stacji roboczej i uniemożliwiają uwierzytelnianie kont administracyjnych na stacjach roboczych nieuprzywilejowanych. Microsoft Intune zarządza zgodnością uprzywilejowanej stacji roboczej, aby upewnić się, że jest ona aktualna przy użyciu poprawek oprogramowania, ochrony przed złośliwym kodem i innych wymagań dotyczących zgodności. |
|Azure Portal logowanie |Administrator otworzy w przeglądarce internetowej Azure Portal, który jest szyfrowany przy użyciu Transport Layer Security (TLS) i loguje się przy użyciu poświadczeń administracyjnych. Żądanie uwierzytelniania jest przetwarzane przez Azure Active Directory bezpośrednio lub za pomocą mechanizmów uwierzytelniania, takich jak Active Directory Federation Services (AD FS) lub uwierzytelniania przekazywanego. |
|Azure MFA |Usługa Azure MFA wysyła żądanie uwierzytelnienia do zarejestrowanego urządzenia przenośnego konta uprzywilejowanego. Urządzenie przenośne jest zarządzane przez usługę Intune, aby zapewnić zgodność z wymaganiami dotyczącymi zabezpieczeń. Administrator musi najpierw przeprowadzić uwierzytelnienie na urządzeniu przenośnym, a następnie do aplikacji Microsoft Authenticator przy użyciu numeru PIN lub systemu biometrycznego przed przypisaniem próby uwierzytelnienia do usługi Azure MFA. |
|Dostęp warunkowy |Zasady dostępu warunkowego sprawdzają próbę uwierzytelnienia, aby upewnić się, że spełnia wymagania wymagane, takie jak adres IP, z którego pochodzi połączenie, członkostwo w grupie dla konta uprzywilejowanego oraz stan zarządzania i zgodności uprzywilejowana stacja robocza zgłoszona przez usługę Intune. |
|Privileged Identity Management (PIM) |Za pomocą Azure Portal administrator może teraz aktywować lub zażądać aktywacji ról uprzywilejowanych, dla których mają autoryzację za pomocą usługi PIM. PIM gwarantuje, że konta uprzywilejowane nie mają żadnych stałych uprawnień administracyjnych i że wszystkie żądania dostępu uprzywilejowanego są przeznaczone tylko dla czasu wymaganego do wykonywania zadań administracyjnych. Program PIM udostępnia również rejestrowanie wszystkich żądań i aktywacji na potrzeby inspekcji. |
|Tożsamość i zarządzanie dostępem|Po pomyślnym zidentyfikowaniu uprzywilejowanego konta i aktywowaniu ról Administrator uzyskuje dostęp do subskrypcji i zasobów platformy Azure, do których przypisano uprawnienia za pomocą zarządzania tożsamościami i dostępem.|

Po zakończeniu przez konto uprzywilejowane kroków w celu uzyskania dostępu administracyjnego do Azure Portal można skonfigurować dostęp do obciążeń i można nawiązać połączenia administracyjne.

### <a name="azure-workload-administration"></a>Administracja obciążenia platformy Azure

|Krok |Opis|
|---|---|
|Dostęp just in Time (JIT)|Aby uzyskać dostęp do maszyn wirtualnych, administrator używa JIT, aby zażądać dostępu do protokołu RDP na serwerze skoku od adresu IP bramy usług pulpitu zdalnego oraz protokołu RDP lub SSH z serwera skoku do odpowiednich maszyn wirtualnych obciążeń.|
|Azure VPN Gateway|Administrator nawiązuje teraz połączenie sieci VPN typu punkt-lokacja z przyuprzywilejowaną stacją roboczą do VPN Gateway platformy Azure, która wykonuje uwierzytelnianie certyfikatu w celu nawiązania połączenia.|
|Brama usług pulpitu zdalnego|Administrator próbuje teraz nawiązać połączenie RDP z serwerem skoku z bramą usług pulpitu zdalnego określoną w konfiguracji Podłączanie pulpitu zdalnego. Brama usług pulpitu zdalnego ma prywatny adres IP, który jest dostępny za pomocą połączenia usługi Azure VPN Gateway. Zasady na bramie usług pulpitu zdalnego kontrolują, czy uprzywilejowane konto ma autoryzację dostępu do żądanego serwera skoku. Brama usług pulpitu zdalnego monituje administratora o poświadczenia i przekazuje żądanie uwierzytelnienia do serwera zasad sieciowych (NPS).|
|Serwer zasad sieciowych (NPS)|Serwer NPS odbiera żądanie uwierzytelnienia od bramy usług pulpitu zdalnego i sprawdza nazwę użytkownika i Active Directory hasło przed wysłaniem żądania do Azure Active Directory, aby wyzwolić żądanie uwierzytelnienia usługi Azure MFA.|
|Azure MFA|Usługa Azure MFA wysyła żądanie uwierzytelnienia do zarejestrowanego urządzenia przenośnego konta uprzywilejowanego. Urządzenie przenośne jest zarządzane przez usługę Intune, aby zapewnić zgodność z wymaganiami dotyczącymi zabezpieczeń. Administrator musi najpierw przeprowadzić uwierzytelnienie na urządzeniu przenośnym, a następnie do aplikacji Microsoft Authenticator przy użyciu numeru PIN lub systemu biometrycznego przed przypisaniem próby uwierzytelnienia do usługi Azure MFA.|
|Serwer skoku|Po pomyślnym uwierzytelnieniu połączenie RDP jest szyfrowane przy użyciu Transport Layer Security (TLS), a następnie wysyłane przez zaszyfrowanego tunelu IPSec do usługi Azure VPN Gateway za pośrednictwem bramy usług pulpitu zdalnego i na serwerze przekazującym. Z serwera szybkiego dostępu administrator może teraz nawiązać połączenie z maszynami wirtualnymi, które są określone w żądaniu JIT, przy użyciu protokołu RDP lub SSH.|

## <a name="general-guidance"></a>Ogólne wytyczne

Podczas wdrażania składników wymienionych w tym artykule są stosowane następujące ogólne wskazówki:

* Zweryfikuj dostępność regionów usług, upewniając się, że wszystkie dane pozostają w zatwierdzonych lokalizacjach i wdróż je w trybie AU Central lub AU Central 2 jako pierwsze preferencje dotyczące chronionych obciążeń

* Zapoznaj się z *raportem certyfikacji platformy Azure-ACSC — chronioną* publikacją 2018 dla stanu certyfikacji poszczególnych usług i wykonaj własne oceny dotyczące wszelkich odpowiednich składników, które nie zostały uwzględnione w raporcie zgodnie z przewodnikiem *klienta ACSC. Microsoft Azure w OCHRONie*

* Zapewnianie łączności sieciowej oraz wszelkich niezbędnych konfiguracji serwera proxy w celu uzyskania dostępu do niezbędnych składników uwierzytelniania, takich jak Azure AD, ADFS i PTA

* Używanie Azure Policy do monitorowania i wymuszania zgodności z wymaganiami

* Upewnij się, że maszyny wirtualne, szczególnie domena usługi Active Directory kontrolery, są przechowywane na zaszyfrowanych kontach magazynu i wykorzystuj Azure Disk Encryption

* Twórz i obsługuj niezawodne procesy zarządzania tożsamościami i uprawnieniami administracyjnymi oraz zarządzanie, aby podwyższyć poziom kontroli technicznej wymienionej w tym artykule

|Resource|URL|
|---|---|
|Dokumenty zgodności z przepisami i zasadami|[Dokumenty zgodności z przepisami i zasadami](https://aka.ms/au-irap)|
|Produkty platformy Azure — regiony australijskie i inne niż regionalne|[Produkty platformy Azure — regiony australijskie i inne niż regionalne](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Strategie ograniczające cybernetycznymi zdarzeń zabezpieczeń|[Strategie ograniczające cybernetycznymi zdarzeń zabezpieczeń](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC ochronę: Bezpieczna Administracja|[ACSC ochronę: Bezpieczna Administracja](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|Instrukcje: Integrowanie infrastruktury bramy usług pulpitu zdalnego przy użyciu rozszerzenia serwera zasad sieciowych (NPS) i Azure AD|[Integracja bramy usług pulpitu zdalnego z serwerem NPS i usługą Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-rdg)|

## <a name="component-guidance"></a>Wskazówki dotyczące składników

Ta sekcja zawiera informacje o przeznaczeniu każdego składnika i jego roli w ogólnej architektury bezpiecznej administracji zdalnej. Dodatkowe linki są udostępniane w celu uzyskania dostępu do przydatnych zasobów, takich jak dokumentacja dokumentacji, przewodniki i samouczki.

## <a name="secure-devices"></a>Zabezpieczanie urządzeń

Urządzenia fizyczne używane przez uprzywilejowanych użytkowników do wykonywania funkcji administracyjnych są cennymi celami dla złośliwych podmiotów. Utrzymywanie bezpieczeństwa i integralności urządzeń fizycznych oraz zapewnianie, że nie są one wolne od złośliwego oprogramowania i chroniąc je przed naruszeniem, jest kluczową częścią zapewniającą bezpieczną zdalną administrację. Obejmuje to konfigurację zabezpieczeń o wysokim priorytecie, która została określona w ACSCych, osiem strategii w celu ograniczenia zdarzeń zabezpieczeń cybernetycznymi, takich jak Application listy dozwolonych, poprawianie aplikacji, zabezpieczanie aplikacji i stosowanie poprawek w systemach operacyjnych. Te możliwości muszą być zainstalowane, skonfigurowane, poddane inspekcji, sprawdzone i zgłaszane w celu zapewnienia, że stan urządzenia jest zgodny z wymaganiami organizacji.

### <a name="privileged-workstation"></a>Uprzywilejowana stacja robocza

Uprzywilejowana stacja robocza to komputer z ograniczoną funkcjonalnością, który może służyć do wykonywania obowiązków administracyjnych i jest dostępny tylko dla kont administracyjnych. W przypadku uprzywilejowanych stacji roboczej należy skonfigurować zasady i konfigurację, aby ograniczyć oprogramowanie, które może być uruchamiane, jego dostęp do zasobów sieciowych oraz Internet i poświadczenia powinny być chronione w przypadku kradzieży lub złamania zabezpieczeń urządzenia. |

|Zasoby|Łącze|
|---|---|
|Omówienie architektury stacji roboczych dostępu uprzywilejowanego|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)|
|Zabezpieczanie uprzywilejowanego dostępu do materiału referencyjnego|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|

### <a name="mobile-device"></a>Urządzenie przenośne

Urządzenie przenośne jest w większym stopniu ryzyko przypadkowej utraty lub kradzieży ze względu na jego przenośność i rozmiar i musi być odpowiednio zabezpieczony. Urządzenie przenośne zapewnia silny dodatkowy czynnik uwierzytelniania, dzięki któremu można wymusić uwierzytelnianie dostępu do urządzeń, możliwość śledzenia za pomocą usług lokalizacyjnych, funkcji szyfrowania i możliwość zdalnego czyszczenia danych. W przypadku korzystania z urządzenia przenośnego jako dodatkowego czynnika uwierzytelniania na platformie Azure urządzenie powinno być skonfigurowane do korzystania z aplikacji Microsoft Authenticator z użyciem numeru PIN lub uwierzytelniania biometrycznego, a nie za pomocą połączeń telefonicznych lub wiadomości SMS.

|Zasoby|Łącze|
|---|---|
|Metody uwierzytelniania usługi Azure AD|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)|
|Jak korzystać z aplikacji Microsoft Authenticator|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|

### <a name="microsoft-intune"></a>Microsoft Intune

Usługa Intune to składnik Enterprise Mobility + Security, który zarządza urządzeniami przenośnymi i aplikacjami. Integruje się ściśle z innymi składnikami, takimi jak Azure Active Directory na potrzeby kontroli tożsamości i dostępu oraz Azure Information Protection ochrony danych. Usługa Intune udostępnia zasady dla stacji roboczych i urządzeń przenośnych w celu określenia wymagań zgodności w celu uzyskania dostępu do zasobów oraz zapewnia możliwości raportowania i inspekcji w celu wglądu w informacje o stanie urządzeń administracyjnych.

|Zasoby|Łącze|
|---|---|
|Dokumentacja Microsoft Intune|[https://docs.microsoft.com/intune/](https://docs.microsoft.com/intune/)|
|Wprowadzenie do zgodności urządzeń w usłudze Intune|[https://docs.microsoft.com/intune/device-compliance-get-started](https://docs.microsoft.com/intune/device-compliance-get-started)|

### <a name="group-policy"></a>Zasady grupy

Zasady grupy służy do kontrolowania konfiguracji systemów operacyjnych i aplikacji. Zasady zabezpieczeń kontrolują uwierzytelnianie, autoryzację i ustawienia inspekcji systemu. Zasady grupy służy do zabezpieczenia uprzywilejowanej stacji roboczej, ochrony poświadczeń administracyjnych i ograniczania nieuprzywilejowanych kont dostępu do uprzywilejowanych urządzeń.

|Zasoby|Łącze|
|---|---|
|Zezwalaj na logowanie lokalne zasady grupy ustawienie|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|

### <a name="jump-server--bastion-host"></a>Serwer skoku/Host bastionu

Serwer skoku/Host bastionu to scentralizowany punkt do administracji. Zawiera narzędzia wymagane do wykonywania obowiązków administracyjnych, ale również ma dostęp do sieci konieczny do łączenia się z zasobami na portach administracyjnych. Serwer szybkiego dostępu jest punktem centralnym do administrowania obciążeniami maszyny wirtualnej w tym artykule, ale można go również skonfigurować jako uprawniony punkt do administrowania funkcjami platformy jako usługi (PaaS), takimi jak SQL. Dostęp do możliwości PaaS można ograniczyć dla poszczególnych usług przy użyciu tożsamości i kontroli sieci.

|Zasoby|Łącze|
|---|---|
|Implementowanie bezpiecznych hostów administracyjnych|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|

### <a name="just-in-time-jit-access"></a>Dostęp just in Time (JIT)

JIT jest możliwością Azure Security Center, która wykorzystuje sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) w celu blokowania dostępu do protokołów administracyjnych, takich jak RDP i SSH na Virtual Machines. Aplikacje hostowane na Virtual Machines nadal działają jako normalne, ale w celu uzyskania dostępu administracyjnego należy żądać tego żądania tylko przez określony czas. Wszystkie żądania są rejestrowane na potrzeby inspekcji.

|Zasoby |Łącze |
|---|---|
|Zarządzanie dostępem just in Time (JIT)|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|
|Automatyzowanie dostępu just in Time do maszyny wirtualnej platformy Azure|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access)|

## <a name="secure-communication"></a>Bezpieczna komunikacja

Ruch komunikacyjny dla działań administracyjnych może zawierać wysoce poufne informacje, takie jak poświadczenia administracyjne, i musi być odpowiednio zarządzany i chroniony. Zapewnienie bezpiecznej komunikacji obejmuje niezawodne funkcje szyfrowania, które uniemożliwiają podsłuchiwanie i segmentację sieci oraz ograniczenia, które ograniczają ruch administracyjny do zatwierdzonych punktów końcowych i kontrolują ruch poprzeczny w przypadku naruszenia bezpieczeństwa systemu.

### <a name="azure-portal"></a>Azure Portal

Komunikacja z Azure Portalami jest zaszyfrowana przy użyciu Transport Layer Security (TLS), a korzystanie z Azure Portal zostało certyfikowane przez ACSC. Jednostki Wspólnoty powinny postępować zgodnie z zaleceniami w *podręczniku klienta ACSC* i konfigurować swoje przeglądarki sieci Web, aby upewnić się, że korzystają z najnowszej wersji protokołu TLS i obsługiwanych algorytmów kryptograficznych.

|Zasoby |Łącze |
|---|---|
|Omówienie usługi Azure Encryption — szyfrowanie podczas przesyłania|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit)|

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

Usługa Azure VPN Gateway zapewnia bezpieczne szyfrowanie z poziomu uprzywilejowanej stacji roboczej do platformy Azure. Usługa Azure VPN Gateway była certyfikowana przez ACSC w celu zapewnienia bezpiecznej komunikacji IPSec. Jednostki Wspólnoty powinny skonfigurować VPN Gateway platformy Azure zgodnie z przewodnikiem klienta ACSC, raportem o certyfikacji ACSC i innymi szczegółowymi wskazówkami.

|Zasoby |Łącze |
|---|---|
|Połączenia punkt-lokacja — informacje|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about)|
|Szczegóły kryptograficzne VPN Gateway platformy Azure|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto)|
|Konfiguracja usługi Azure VPN Gateway|[Konfiguracja usługi Azure VPN Gateway](vpn-gateway.md)|

### <a name="remote-desktop-rd-gateway"></a>Brama Pulpit zdalny (RD)

Brama usług pulpitu zdalnego jest bezpiecznym mechanizmem do kontrolowania i zatwierdzania połączeń RDP z systemami. Działa przez hermetyzację ruchu RDP w protokole HTTPS (HyperText Transfer Protocol Secure) i szyfrowany przy użyciu protokołu TLS. Protokół TLS zapewnia dodatkową warstwę zabezpieczeń dla ruchu administracyjnego.

|Zasoby |Łącze |
|---|---|
|Architektura Usługi pulpitu zdalnego|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|

### <a name="network-security-groups-nsgs"></a>Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)

Funkcja sieciowych grup zabezpieczeń jako listy Access Control (ACL) dla ruchu sieciowego wprowadzający lub opuszczający podsieci lub maszyny wirtualne. Sieciowych grup zabezpieczeń zapewniają segmentację sieci i zapewniają mechanizm kontroli i ograniczania przepływów komunikacji dozwolonych między systemami. Sieciowych grup zabezpieczeń są głównym składnikiem administracji just in Time (JIT) w celu zezwalania na dostęp do protokołów administracyjnych lub odmowy dostępu.

|Zasoby |Łącze |
|---|---|
|Omówienie grup zabezpieczeń platformy Azure|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Instrukcje: Planowanie sieci wirtualnych|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|

## <a name="strong-authentication"></a>Silne uwierzytelnianie

Bezpieczne Identyfikowanie użytkowników uprzywilejowanych przed przyznaniem im dostępu do systemów jest głównym składnikiem bezpiecznego administrowania. Muszą być stosowane mechanizmy ochrony poświadczeń skojarzonych z uprzywilejowanym kontem i zapobiegania złośliwym uczestnikom uzyskiwania dostępu do systemów za pomocą personifikacji lub kradzieży poświadczeń.

### <a name="domain-controller-dc"></a>Kontroler domeny (DC)

Na wysokim poziomie kontroler domeny obsługuje kopię bazy danych Active Directory, która zawiera wszystkich użytkowników, komputery i grupy w domenie. Kontrolery domeny wykonują uwierzytelnianie dla użytkowników i komputerów. Kontrolery domeny w tej architekturze są hostowane jako maszyny wirtualne na platformie Azure i zapewniają usługi uwierzytelniania dla kont uprzywilejowanych łączących się z serwerami skoku i obciążeniami maszynami wirtualnymi.

|Zasoby |Łącze |
|---|---|
|Przegląd Active Directory Domain Services|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Azure AD to usługa uwierzytelniania dla systemu Azure. Zawiera chmurę

tożsamości i zapewniają uwierzytelnianie i autoryzację dla środowiska platformy Azure. Usługa Azure AD może zostać zsynchronizowana z Active Directory przez Azure AD Connect i może zapewnić uwierzytelnianie federacyjne za pomocą Active Directory Federation Services (AD FS) i Azure AD Connect. Usługa Azure AD jest głównym składnikiem bezpiecznego administrowania.

|Zasoby |Łącze |
|---|---|
|Dokumentacja usługi Azure Active Directory|[https://docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)|
|Dokumentacja tożsamości hybrydowej|[https://docs.microsoft.com/azure/active-directory/hybrid](https://docs.microsoft.com/azure/active-directory/hybrid)|

### <a name="network-policy-server-nps"></a>Serwer zasad sieciowych (NPS)

Serwer NPS jest serwerem uwierzytelniania i zasad, który zapewnia zaawansowane procesy uwierzytelniania i autoryzacji. Serwer NPS w tej architekturze zapewnia integrację uwierzytelniania usługi Azure MFA z żądaniami uwierzytelniania bramy usług pulpitu zdalnego. Serwer NPS ma określoną wtyczkę obsługującą integrację z usługą Azure MFA w usłudze Azure AD.

|Zasoby |Łącze |
|---|---|
|Dokumentacja serwera zasad sieciowych|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)|

### <a name="azure-mfa"></a>Azure MFA

Azure MFA jest usługą uwierzytelniania dostępną w ramach Azure Active Directory, aby umożliwić uwierzytelnianie żądań poza nazwą użytkownika i hasłem w celu uzyskania dostępu do zasobów w chmurze, takich jak Azure Portal. Usługa Azure MFA obsługuje różne metody uwierzytelniania, a ta architektura korzysta z aplikacji Microsoft Authenticator, aby zwiększyć bezpieczeństwo i integrację z serwerem zasad sieciowych.

|Zasoby |Łącze |
|---|---|
|Jak to działa: Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)|
|Instrukcje: Wdróż usługę Azure Multi-Factor Authentication bazującą na chmurze|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)|

## <a name="strong-authorisation"></a>Silne autoryzacja

Po pomyślnym zidentyfikowaniu konta uprzywilejowanego można przyznać mu dostęp do zasobów. Kontrola autoryzacji i zarządzanie uprawnieniami przypisanymi do określonego konta. Procesy o silnym zezwoleniu są zgodne z istotną ACSCą ośmioma strategią w celu ograniczenia cybernetycznymi zdarzeń związanych z zabezpieczeniami ograniczających uprawnienia administracyjne.

### <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

Dostęp do wykonywania uprzywilejowanych akcji w ramach platformy Azure jest oparty na rolach przypisanych do tego konta. Platforma Azure obejmuje szeroki i szczegółowy zakres ról z określonymi uprawnieniami do wykonywania określonych zadań. Role te można przyznawać na wielu poziomach, takich jak subskrypcja lub Grupa zasobów. Zarządzanie przypisaniem ról i uprawnieniami odbywa się na podstawie kont i grup w Azure Active Directory i jest zarządzane za pośrednictwem Access Control (IAM) na platformie Azure.

|Zasoby |Łącze |
|---|---|
|Access Control oparte na rolach na platformie Azure|[https://docs.microsoft.com/azure/role-based-access-control](https://docs.microsoft.com/azure/role-based-access-control)|
|Opis definicji ról|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

PIM to składnik Azure Active Directory, który kontroluje dostęp do ról uprzywilejowanych. Konta uprzywilejowane nie wymagają stałego lub stałego dostępu uprzywilejowanego, ale zamiast tego mogą być przyznawane użytkownikowi możliwość żądania uprzywilejowanego dostępu przez pewien czas w celu ukończenia działań uprzywilejowanych. Program PIM oferuje dodatkowe mechanizmy kontroli nad utrzymaniem i ograniczaniem dostępu uprzywilejowanego, a także rejestrowaniem i inspekcją w celu śledzenia wystąpień użycia uprawnień.

|Zasoby |Łącze |
|---|---|
|Dokumentacja Privileged Identity Management (PIM)|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)|
|Rozpoczynanie korzystania z usługi PIM|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started)|

### <a name="conditional-access"></a>Dostęp warunkowy

Dostęp warunkowy jest składnikiem Azure Active Directory, który zezwala na dostęp do zasobów lub go odmawia na podstawie warunków. Te warunki mogą być oparte na lokalizacji sieciowej, typie urządzenia, stanie zgodności, członkostwie w grupie i nie tylko. Dostęp warunkowy służy do wymuszania uwierzytelniania wieloskładnikowego, zarządzania urządzeniami i zgodności przy użyciu usługi Intune oraz przynależności do grupy kont administracyjnych.

|Zasoby |Łącze |
|---|---|
|Dokumentacja dostępu warunkowego|[https://docs.microsoft.com/azure/active-directory/conditional-access](https://docs.microsoft.com/azure/active-directory/conditional-access)|
|Instrukcje: Wymagaj zarządzanych urządzeń dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem dotyczącym zarządzania ruchem przychodzącym przy użyciu [bramy i kontroli](gateway-ingress-traffic.md) w celu uzyskania szczegółowych informacji na temat kontrolowania przepływu ruchu przez składniki bramy na platformie Azure.
