---
title: Pięć kroków w celu zabezpieczenia infrastruktury tożsamości w Azure Active Directory
description: Ten dokument przedstawia listę ważnych działań, które Administratorzy powinni wdrożyć, aby ułatwić im ochronę organizacji przy użyciu funkcji usługi Azure AD
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martinco
ms.openlocfilehash: 0ccd2ee8b2c9f542eabe7a297f4c99a3993a47e9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726754"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Pięć kroków związanych z zabezpieczaniem infrastruktury tożsamości

W przypadku odczytywania tego dokumentu masz świadomość istotności zabezpieczeń. Użytkownik jest już odpowiedzialny za zabezpieczenie organizacji. Jeśli chcesz przekonać inne o znaczeniu zabezpieczeń, wyślij je, aby przeczytać najnowszy [Raport analizy zabezpieczeń firmy Microsoft](https://go.microsoft.com/fwlink/p/?linkid=2073747).

Ten dokument pomoże Ci lepiej zabezpieczyć stan przy użyciu możliwości Azure Active Directory przy użyciu listy kontrolnej z pięcioma krokami, aby zaszczepować organizację przed atakami cybernetycznymi.

Ta lista kontrolna ułatwi szybkie wdrożenie krytycznych zalecanych akcji w celu natychmiastowego zapewnienia ochrony organizacji przez wyjaśnienie, jak:

* Wzmocnij swoje poświadczenia.
* Zmniejsz obszar narażony na ataki.
* Automatyzowanie odpowiedzi na zagrożenia.
* Zwiększ świadomość inspekcji i monitorowania.
* Włącz bardziej przewidywalne i kompletne zabezpieczenia użytkowników końcowych, korzystając z samoobsługowego rozwiązywania problemów.

> [!NOTE]
> Wiele zaleceń zawartych w tym dokumencie dotyczy tylko aplikacji skonfigurowanych do korzystania z Azure Active Directory jako ich dostawcy tożsamości. Skonfigurowanie aplikacji do logowania jednokrotnego zapewnia korzyści wynikające z zasad poświadczeń, wykrywania zagrożeń, inspekcji, rejestrowania i innych funkcji. [Logowanie jednokrotne za pośrednictwem Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) jest podstawą, na której bazują wszystkie te zalecenia.

Zalecenia zawarte w tym dokumencie są wyrównane z [wynikiem bezpiecznego tożsamości](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score), zautomatyzowanej oceny konfiguracji zabezpieczeń tożsamości dzierżawy usługi Azure AD. Organizacje mogą używać strony oceny zabezpieczeń tożsamości w portalu usługi Azure AD, aby znaleźć luki w ich bieżącej konfiguracji, aby upewnić się, że są one zgodne z bieżącymi najlepszymi rozwiązaniami firmy Microsoft dotyczącymi zabezpieczeń. Zaimplementowanie każdego zalecenia na stronie ze wskaźnikiem zabezpieczeń spowoduje zwiększenie oceny i umożliwi śledzenie postępu, a także ułatwia porównanie implementacji z innymi podobnymi organizacjami wielkości lub branżą.

![Wskaźnik bezpieczeństwa tożsamości](./media/steps-secure-identity/azure-ad-sec-steps0.png)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Przed rozpoczęciem: Ochrona kont uprzywilejowanych za pomocą usługi MFA

Przed rozpoczęciem korzystania z tej listy kontrolnej upewnij się, że nie nastąpiło naruszenie zabezpieczeń podczas odczytywania tej listy kontrolnej. Najpierw musisz chronić Twoje konta uprzywilejowane.

Osoby atakujące, które uzyskują kontrolę nad kontami uprzywilejowanymi, mogą mieć ogromne szkody, dlatego należy zapewnić ochronę tych kont jako pierwsze. Włącz i wymagaj uwierzytelniania wieloskładnikowego (MFA) w [usłudze Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) dla wszystkich administratorów w organizacji przy użyciu [ochrony linii bazowej](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Jeśli nie zaimplementowano usługi MFA, zrób to teraz! Jest to ważne.

Czy wszystko jest ustawione? Zacznijmy od listy kontrolnej.

## <a name="step-1---strengthen-your-credentials"></a>Krok 1. wzmocnienie poświadczeń 

Większość naruszeń zabezpieczeń przedsiębiorstwa pochodzi z konta złamanego z jedną z kilku metod, takich jak rozpylanie hasła, odtworzenie naruszenia lub wyłudzanie informacji. Dowiedz się więcej na temat tych ataków w tym filmie wideo (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-use-strong-authentication"></a>Upewnij się, że Twoja organizacja używa silnego uwierzytelniania

Uwzględniając częstotliwość, w której hasła są naliczane, phishing, skradziony i złośliwe oprogramowanie, ma krytyczne znaczenie dla hasła przy użyciu jakiejś formy silnego poświadczenia — Dowiedz się więcej na temat [usługi Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)MFA.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Zacznij zakazywanie często zaatakowane hasła i Wyłącz tradycyjną złożoność oraz reguły wygasania.

W wielu organizacjach jest używana tradycyjna złożoność (wymagające znaków specjalnych, cyfr, wielkich i małych liter) oraz reguł wygasania haseł. [Badania firmy Microsoft](https://aka.ms/passwordguidance) pokazują, że te zasady powodują, że użytkownicy wybierają hasła, które są łatwiejsze do odgadnięcia.

Funkcja [dynamicznego zabronionego hasła](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) usługi Azure AD używa bieżącego zachowania osoby atakującej, aby uniemożliwić użytkownikom ustawianie haseł, które można łatwo złamać. Ta funkcja jest zawsze włączona w przypadku tworzenia użytkowników w chmurze, ale jest teraz również dostępna dla organizacji hybrydowych podczas wdrażania [ochrony hasłem usługi Azure AD dla systemu Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). Ochrona hasłem usługi Azure AD blokuje użytkownikom możliwość wybierania tych typowych haseł i można ją rozszerzyć w celu zablokowania hasła zawierającego niestandardowe słowa kluczowe. Można na przykład uniemożliwić użytkownikom wybieranie haseł zawierających nazwy produktów firmy lub lokalnego zespołu sportowego.

Firma Microsoft zaleca stosowanie następujących nowoczesnych zasad haseł opartych na [wytycznych NIST](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Wymagaj hasła, które mają co najmniej 8 znaków. Nie jest to jeszcze lepsze, ponieważ powodują, że użytkownicy wybierają przewidywalne hasła, zapisują hasła w plikach lub zapisują je w dół.
2. Wyłącz reguły wygasania, które umożliwiają użytkownikom łatwe odgadnięcie haseł, takich jak **Spring2019!**
3. Wyłącz wymagania dotyczące kompozycji znaków i uniemożliwiaj użytkownikom wybranie często zaatakowanych haseł, ponieważ powodują one, że użytkownicy wybierają przewidywalną podstawienia znaków w hasłach.

Możesz użyć [programu PowerShell, aby zapobiec wygaśnięciu haseł](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) dla użytkowników w przypadku bezpośredniego tworzenia tożsamości w usłudze Azure AD. Organizacje hybrydowe powinny zaimplementować te zasady przy użyciu [ustawień zasad grupy domeny](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) lub [programu Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Ochrona przed nieujawnionymi poświadczeniami i zwiększanie odporności przed awarią

Jeśli Twoja organizacja korzysta z rozwiązania do obsługi tożsamości hybrydowej z uwierzytelnianiem przekazującym lub federacyjnym, należy włączyć synchronizację skrótów haseł z następujących dwóch przyczyn:

* Raport [Użytkownicy z ujawnionymi poświadczeniami](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) w usłudze Azure AD Management ostrzega użytkownika o parach nazw i hasła, które zostały uwidocznione w "ciemnej sieci Web". Niezwykła ilość haseł jest wycieka za pośrednictwem wyłudzania informacji, złośliwego oprogramowania i ponownego użycia haseł w witrynach innych firm, które zostały później naruszone. Firma Microsoft odszuka wiele z tych nieujawnionych poświadczeń i poinformuje użytkownika, że w tym raporcie są one zgodne z poświadczeniami w organizacji, ale tylko w przypadku [włączenia synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).
* W przypadku awarii lokalnej (na przykład w przypadku ataku z użyciem oprogramowania wymuszającego okup) można przełączyć się do korzystania z [uwierzytelniania w chmurze przy użyciu funkcji synchronizacji skrótów haseł](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Ta metoda uwierzytelniania kopii zapasowej umożliwi kontynuowanie dostępu do aplikacji skonfigurowanych do uwierzytelniania za pomocą Azure Active Directory, w tym pakietu Office 365. W takim przypadku pracownicy IT nie będą musieli korzystać z osobistych kont e-mail w celu udostępniania danych, dopóki nie zostanie rozwiązane lokalne przestoje.

Dowiedz się więcej o tym, jak działa [Synchronizacja skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) .

> [!NOTE]
> W przypadku włączenia synchronizacji skrótów haseł i korzystania z usług Azure AD Domain Services skróty protokołu Kerberos (AES 256) i opcjonalnie NTLM (RC4, bez soli) również będą szyfrowane i synchronizowane z usługą Azure AD. 

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementowanie blokady inteligentnej AD FS ekstranetu

Organizacje, które konfigurują aplikacje do uwierzytelniania bezpośrednio w usłudze Azure AD z poziomu [inteligentnego blokowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Jeśli używasz AD FS w systemie Windows Server 2012R2, zaimplementuj AD FS [Ochrona blokowania ekstranetu](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Jeśli używasz AD FS w systemie Windows Server 2016, zaimplementuj [inteligentne blokady ekstranetu](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS inteligentnego blokowania ekstranetu chroni przed atakami polegającymi na rozdziałach, które są ukierunkowane AD FS, uniemożliwiając zablokowanie użytkowników w Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Korzystaj z zalet bezpiecznego, łatwiejszego w użyciu poświadczeń

Za pomocą funkcji [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)można zastąpić hasła silnym uwierzytelnianiem dwuskładnikowym na komputerach i urządzeniach przenośnych. To uwierzytelnianie obejmuje nowy typ poświadczeń użytkownika, które są bezpiecznie powiązane z urządzeniem i używa biometrycznych lub PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Krok 2. zredukowanie obszaru ataków

Z uwagi na niebezpieczeństwo hasła, zminimalizowanie obszaru ataków w organizacji ma krytyczne znaczenie. Eliminowanie używania starszych, mniej bezpiecznych protokołów, ograniczanie punktów wejścia dostępu i wykonywanie bardziej znaczącej kontroli nad dostępem administracyjnym do zasobów może pomóc w zmniejszeniu obszaru podatności na ataki.

### <a name="block-legacy-authentication"></a>Blokowanie starszego uwierzytelniania

Aplikacje korzystające z własnych starszych metod do uwierzytelniania w usłudze Azure AD i uzyskiwania dostępu do danych firmowych stanowią inne ryzyko dla organizacji. Przykładami aplikacji korzystających ze starszego uwierzytelniania są klienci POP3, IMAP4 lub SMTP. Starsze aplikacje uwierzytelniania uwierzytelniają się w imieniu użytkownika i uniemożliwiają usłudze Azure AD wykonywanie zaawansowanych ocen zabezpieczeń. Alternatywna, nowoczesne uwierzytelnianie zmniejsza zagrożenie bezpieczeństwa, ponieważ obsługuje uwierzytelnianie wieloskładnikowe i dostęp warunkowy. Zalecamy następujące trzy akcje:

1. Blokuj [starsze uwierzytelnianie, jeśli używasz AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Skonfiguruj [SharePoint Online i usługi Exchange Online do korzystania z nowoczesnego uwierzytelniania](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Użyj [zasad dostępu warunkowego, aby zablokować starsze uwierzytelnianie](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions).

### <a name="block-invalid-authentication-entry-points"></a>Blokuj nieprawidłowe punkty wejścia uwierzytelniania

Korzystając z zakładanego naruszenia warunków, należy zmniejszyć wpływ naruszenia poświadczeń użytkownika. Dla każdej aplikacji w danym środowisku należy wziąć pod uwagę prawidłowe przypadki użycia: które grupy, które sieci, urządzenia i inne elementy są autoryzowane, Zablokuj resztę. Za pomocą [dostępu warunkowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)można kontrolować sposób, w jaki autoryzowani użytkownicy uzyskują dostęp do swoich aplikacji i zasobów na podstawie określonych warunków.

### <a name="block-end-user-consent"></a>Zablokuj zgodę użytkownika końcowego

Domyślnie wszyscy użytkownicy w usłudze Azure AD mogą przyznawać aplikacje, które korzystają z protokołu OAuth 2,0 i uprawnienia do [struktury zgody](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) na tożsamość firmy Microsoft w celu uzyskania dostępu do danych firmowych. Podczas wyrażania zgody użytkownicy mogą łatwo uzyskać użyteczne aplikacje, które integrują się z Microsoft 365 i platformą Azure, może stanowić ryzyko, jeśli nie są używane i monitorowane uważnie. [Wyłączenie wszystkich przyszłych operacji wyrażania zgody użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access) może pomóc w zmniejszeniu obszaru powierzchni i złagodzeniu tego ryzyka. Jeśli zgoda użytkownika końcowego jest wyłączona, poprzednie dotacje będą nadal honorowane, ale wszystkie przyszłe operacje wyrażania zgody muszą być wykonywane przez administratora. Przed wyłączeniem tej funkcji zaleca się zagwarantowanie, że użytkownicy będą zrozumieć, jak należy zażądać zatwierdzenia przez administratora dla nowych aplikacji; Dzięki temu można zmniejszyć liczbę użytkowników, zminimalizować ilość pomocy technicznej i upewnić się, że użytkownicy nie zarejestrują się w aplikacjach przy użyciu poświadczeń niezwiązanych z usługą Azure AD.

### <a name="implement-azure-ad-privileged-identity-management"></a>Implementowanie Azure AD Privileged Identity Management

Innym wpływem "założono naruszenie" jest konieczność zminimalizowania prawdopodobieństwa, że naruszone konto może działać z rolą uprzywilejowaną. 

[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) ułatwia zminimalizowanie uprawnień konta, pomagając:

* Zidentyfikuj użytkowników przypisanych do ról administracyjnych i zarządzaj nimi.
* Informacje o nieużywanych lub nadmiernych rolach uprawnień należy usunąć.
* Ustanów reguły, aby upewnić się, że uprzywilejowane role są chronione przez uwierzytelnianie wieloskładnikowe.
* Ustanów reguły, aby upewnić się, że uprzywilejowane role są przyznawane tylko wystarczająco długo, aby można było wykonać zadanie uprzywilejowane.

Włącz usługę Azure AD PIM, a następnie Wyświetl użytkowników, którym przypisano role administracyjne, i Usuń niepotrzebne konta z tych ról. W przypadku pozostałych uprzywilejowanych użytkowników Przenieś je na stałe, aby kwalifikować się do nich. Na koniec Ustanów odpowiednie zasady, aby upewnić się, że muszą oni uzyskać dostęp do ról uprzywilejowanych, dzięki czemu mogą oni bezpiecznie wykonać te czynności z wymaganą kontrolą zmian.

W ramach wdrażania procesu uprzywilejowanego konta postępuj zgodnie z najlepszymi rozwiązaniami, [Aby utworzyć co najmniej dwa konta awaryjne](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) , aby upewnić się, że masz dostęp do usługi Azure AD w przypadku zablokowania.

## <a name="step-3---automate-threat-response"></a>Krok 3 — Automatyzowanie odpowiedzi na zagrożenia

Azure Active Directory ma wiele funkcji, które automatycznie przechwytuje ataki, aby usunąć opóźnienie między wykrywaniem a odpowiedzią. Obniżenie kosztów i ryzyka pozwala zmniejszyć czas, w którym przestępcy są używane do osadzania się w środowisku. Poniżej przedstawiono konkretne czynności, które można wykonać.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementowanie zasad zabezpieczeń dotyczących ryzyka użytkownika przy użyciu Azure AD Identity Protection

Ryzyko użytkownika wskazuje prawdopodobieństwo naruszenia bezpieczeństwa tożsamości użytkownika i jest obliczana na podstawie [zdarzeń ryzyka użytkownika](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) , które są skojarzone z tożsamością użytkownika. Zasady ryzyka dla użytkowników to zasady dostępu warunkowego, które szacują poziom ryzyka dla określonego użytkownika lub grupy. W oparciu o niski, średni i wysoki poziom ryzyka zasady można skonfigurować w taki sposób, aby blokować dostęp lub wymagały bezpiecznej zmiany hasła przy użyciu usługi uwierzytelniania wieloskładnikowego. Zalecenie firmy Microsoft to wymaganie bezpiecznej zmiany hasła dla użytkowników na wysokim ryzyku.

![Użytkownicy oflagowani w związku z ryzykiem](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementowanie zasad dotyczących ryzyka związanego z logowaniem przy użyciu Azure AD Identity Protection

Ryzyko związane z logowaniem to prawdopodobieństwo, że ktoś inny niż właściciel konta podejmuje próbę zalogowania się przy użyciu tożsamości. [Zasady dotyczące ryzyka związanego](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) z logowaniem to zasady dostępu warunkowego, które szacują poziom ryzyka dla określonego użytkownika lub grupy. W oparciu o poziom ryzyka (wysoki/średni/niski) zasady można skonfigurować w taki sposób, aby blokować dostęp lub wymuszać uwierzytelnianie wieloskładnikowe. Upewnij się, że wymuszsz uwierzytelnianie wieloskładnikowe na średnim lub wyższym ryzyku.

![Logowanie się z anonimowych adresów IP](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Krok 4 — zwiększenie świadomości

Inspekcja i rejestrowanie zdarzeń związanych z zabezpieczeniami oraz powiązanych alertów są istotnymi składnikami strategii skutecznej ochrony. Dzienniki zabezpieczeń i raporty zapewniają elektroniczny rejestr podejrzanych działań i pomagają wykryć wzorce, które mogą wskazywać na próbę lub pomyślne przechodzenie do sieci, a także ataki wewnętrzne. Inspekcji można użyć do monitorowania aktywności użytkownika, dokumentu zgodności z przepisami, przeprowadzenia analizy śledczej i innych. Alerty udostępniają powiadomienia o zdarzeniach zabezpieczeń.

### <a name="monitor-azure-ad"></a>Monitorowanie usługi Azure AD

Usługi Microsoft Azure i funkcje udostępniają konfigurowalne opcje inspekcji zabezpieczeń i rejestrowania, które ułatwiają identyfikację luk w zabezpieczeniach i mechanizmach i rozwiązywanie tych luk w celu uniknięcia naruszeń. [W portalu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)można korzystać z funkcji [rejestrowania i inspekcji na platformie Azure](https://docs.microsoft.com/azure/security/azure-log-audit) oraz raportów dotyczących aktywności inspekcji.

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitoruj Azure AD Connect Health w środowiskach hybrydowych

[AD FS monitorowania z Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) zapewnia lepszy wgląd w potencjalne problemy i widoczność ataków na infrastrukturę AD FS. Azure AD Connect Health dostarcza alerty z szczegółowymi informacjami, krokami rozwiązania i łączami do powiązanej dokumentacji; Analiza użycia dla kilku metryk związanych z ruchem związanym z uwierzytelnianiem; Monitorowanie wydajności i raporty.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorowanie zdarzeń Azure AD Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) to narzędzie do powiadamiania, monitorowania i raportowania, których można użyć do wykrywania potencjalnych luk w zabezpieczeniach, które mają wpływ na tożsamości w organizacji. Wykrywa zdarzenia dotyczące ryzyka, takie jak nieujawnione poświadczenia, niemożliwa podróż i logowania z zainfekowanych urządzeń, anonimowe adresy IP, adresy IP skojarzone z podejrzanymi działaniami oraz nieznane lokalizacje. Włącz alerty powiadomień, aby otrzymywać wiadomości e-mail użytkowników z ryzykiem i/lub tygodniową pocztą e-mail.

Azure AD Identity Protection oferuje dwa ważne raporty, które należy monitorować codziennie:
1. Ryzykowne raporty logowania to działania związane z logowaniem użytkownika, które należy zbadać, ale uprawniony właściciel może nie wykonać logowania.
2. Ryzykowne raporty użytkowników to konta użytkowników, które mogły zostać naruszone, takie jak wykryto wycieki poświadczenia lub użytkownik zalogowany z różnych lokalizacji powodujących niepowodzenie podróży. 

![Użytkownicy oflagowani w związku z ryzykiem](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Inspekcja aplikacji i przyzwoleń do nich

Użytkownicy mogą dochodzić do nawigowania do witryny lub aplikacji sieci Web, które będą miały dostęp do informacji o profilu i danych użytkownika, takich jak wiadomości e-mail. Złośliwy aktor może korzystać z uzyskanych uprawnień, które otrzymały w celu zaszyfrowania zawartości skrzynki pocztowej, i uzyskania dostępu do oprogramowania wymuszającego okup w celu odzyskania danych skrzynki pocztowej. [Administratorzy powinni przeglądać i przeprowadzać inspekcję](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) uprawnień określonych przez użytkowników.

## <a name="step-5---enable-end-user-self-help"></a>Krok 5 — Włączanie samoobsługowej pomocy użytkownika końcowego

Tak dużo, jak to możliwe, należy zrównoważyć zabezpieczenia z wydajnością. W tych samych wierszach zbliżamy się do sposób myślenia, że tworzysz podstawę do zabezpieczeń w długim przebiegu, możesz usunąć tarcie z organizacji, zwiększając możliwości użytkowników, a pozostałe czujność. 

### <a name="implement-self-service-password-reset"></a>Implementowanie samoobsługowego resetowania hasła

Funkcja samoobsługowego [resetowania haseł (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) na platformie Azure zapewnia administratorom IT prosty sposób, aby użytkownicy mogli resetować lub odblokowywać hasła lub konta bez interwencji administratora. System obejmuje szczegółowe raporty pozwalające śledzić, kiedy użytkownicy korzystają z systemu, oraz powiadomienia ostrzegające o jego nieprawidłowym użyciu lub nadużyciach związanych z zabezpieczeniami. 

### <a name="implement-self-service-group-management"></a>Implementowanie samoobsługowego zarządzania grupami

Usługa Azure AD umożliwia zarządzanie dostępem do zasobów przy użyciu grup zabezpieczeń i grup pakietu Office 365. Tymi grupami można zarządzać za pomocą właścicieli grup zamiast administratorów IT. Ta funkcja jest znana jako samoobsługowe [Zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), dzięki czemu właściciele grup nie mają przypisanej roli administracyjnej do tworzenia grup i zarządzania nimi bez polegania na administratorach do obsługi swoich żądań.

### <a name="implement-azure-ad-access-reviews"></a>Zaimplementuj przeglądy dostępu usługi Azure AD

Za pomocą [przeglądów dostępu w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)można zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról uprzywilejowanych w celu zapewnienia, że utrzymujesz Standard zabezpieczeń, który nie zapewnia użytkownikom dostępu do dłuższych okresów czasu, gdy nie potrzebują go.

## <a name="summary"></a>Podsumowanie

Istnieje wiele aspektów bezpiecznej infrastruktury tożsamości, ale ta lista kontrolna z pięcioma krokami pomoże Ci szybko wykonać bezpieczniejsze i bezpieczne infrastruktury tożsamości:

* Wzmocnij swoje poświadczenia.
* Zmniejsz obszar narażony na ataki.
* Automatyzowanie odpowiedzi na zagrożenia.
* Zwiększ świadomość inspekcji i monitorowania.
* Włącz bardziej przewidywalne i kompletne zabezpieczenia użytkowników końcowych, korzystając z samoobsługowego rozwiązywania problemów.

Doceniamy, jak poważnie ponosisz bezpieczeństwo tożsamości i mamy nadzieję, że ten dokument jest przydatny do bardziej bezpiecznego stan organizacji.

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz pomocy w zaplanowaniu i wdrożeniu zaleceń, zapoznaj się z [planami wdrażania projektu usługi Azure AD](https://aka.ms/deploymentplans) , aby uzyskać pomoc.
