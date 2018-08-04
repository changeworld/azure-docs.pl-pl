---
title: Pięć kroków, aby zabezpieczyć swoją infrastrukturę tożsamości w usłudze Azure Active Directory
description: W tym dokumencie przedstawiono listę ważne czynności, które Administratorzy powinny implementować ułatwiające bezpieczne swojej organizacji, korzystając z możliwości usługi Azure AD
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: 2a6c94d902c639a2529e501347876d63a3f56d7e
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505613"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Pięć kroków do zabezpieczania infrastruktury tożsamości

Jeśli czytasz ten dokument, masz świadomość znaczenia zabezpieczeń. Prawdopodobnie już wykonuje odpowiedzialność zabezpieczanie Twojej organizacji. Jeśli zachodzi potrzeba przekonywać znaczenie bezpieczeństwa, wyślij je do Przeczytaj najnowsze [raportu analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/security/intelligence-report).

Ten dokument pomoże Ci stan bezpieczniejsze, przy użyciu funkcji usługi Azure Active Directory za pomocą listy kontrolnej w pięciu krokach Szczepić swoją organizację przed cyberatakami.

Ta lista kontrolna mogą pomóc w szybkim wdrażaniu krytycznych zalecane działania, aby od razu chronić Twoją organizację, wyjaśniając, jak:

* Wzmocnij swoje poświadczenia.
* Zmniejszenie obszaru powierzchni ataku.
* Zautomatyzuj reagowania na zagrożenia.
* Zwiększ świadomość inspekcji i monitorowania.
* Zabezpieczenia dla bardziej przewidywalna i kompletne przez użytkownika końcowego z samodzielnej pomocy.

> [!NOTE]
> Wiele zaleceń w tym dokumencie dotyczą tylko aplikacji, które są skonfigurowane do korzystania z usługi Azure Active Directory jako dostawcy tożsamości. Konfigurowanie aplikacji do logowania jednokrotnego zapewnia korzyści wynikające z zasad dostępu do poświadczeń, wykrywania zagrożeń i inspekcji, rejestrowania i inne funkcje można dodać do tych aplikacji. [Logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) jest Fundacji, w którym wszystkie te zalecenia są oparte.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Przed przystąpieniem do wykonywania: ochronę uprzywilejowanych kont za pomocą usługi MFA

Przed przystąpieniem do wykonywania tej listy kontrolnej, upewnij się, że nie możesz uzyskać złamane, natomiast czytasz tej listy kontrolnej. Najpierw musisz chronić uprzywilejowanych kontach.

Osoby atakujące, które Pełna kontrola nad wykorzystaniem kont uprzywilejowanych może zrobić ogromną szkody, koniecznie najpierw ochronę tych kont. Włącz i wymagają [usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) dla wszystkich administratorów w organizacji za pomocą [ochronę](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Jeśli jeszcze nie zaimplementowano uwierzytelniania Wieloskładnikowego, należy to zrobić teraz! Jest to ważne.

Wszystko jest gotowe? Zacznijmy listy kontrolnej.

## <a name="step-1---strengthen-your-credentials"></a>Krok 1 — zwiększanie poświadczeń 

Większości naruszeń zabezpieczeń przedsiębiorstwa pochodzą z kontem naruszona za pomocą jednej z kilku metod, takich jak hasła osłony, powtarzanie naruszenia lub wyłudzania informacji. Dowiedz się więcej na temat tych ataków, w tym filmie pokazano:
> [!VIDEO https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3016/player]

Jeśli użytkownicy w Twoim systemie tożsamości korzysta słabe hasła i nie wzmocnienia je przy użyciu uwierzytelniania wieloskładnikowego, nie jest kwestią lub gdy można pobrać naruszenia zabezpieczeń — po prostu "jak często."

### <a name="make-sure-your-organization-use-strong-authentication"></a>Upewnić się, że Twoja organizacja Użyj silnego uwierzytelniania

Biorąc pod uwagę częstotliwość hasła odgadnąć, phished, kradzieży ze złośliwym oprogramowaniem lub używane ponownie, ma kluczowe znaczenie dla kopii hasło z pewnego rodzaju silnych poświadczeń — Dowiedz się więcej o [usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="turn-off-traditional-complexity-expiration-rules-and-start-banning-commonly-attacked-passwords-instead"></a>Wyłącz tradycyjnych złożoność, zasady wygasania i rozpoczęcia blokowanie często zaatakowane zamiast hasła

Wiele organizacji korzysta z tradycyjnych złożoności (na przykład, znaków specjalnych) i zasady wygasania haseł. Badania firmy Microsoft wykazało, że te zasady są szkodliwe, powodują, że użytkownicy wybiorą hasła, które są łatwiejsze do odgadnięcia.

Zalecenia firmy Microsoft, zgodne z [wskazówki NIST](https://pages.nist.gov/800-63-3/sp800-63b.html), są następujące trzy implementacji:

1. Wymaga hasła mają co najmniej 8 znaków. Już nie jest koniecznie lepsze, ponieważ mogą spowodować, że użytkownicy wybiorą hasła przewidywalne, zapisywać hasła w plikach lub zapisywania ich w dół.
2. Wyłącz zasady wygasania, które zachęcić użytkowników do łatwe do odgadnięcia haseł, takich jak **Summer2018!**.
3. Wyłącz wymagania dotyczące znaków kompozycji i uniemożliwić użytkownikom wybór często zaatakowane hasła, ponieważ powodują użytkownikom na wybór podstawienia przewidywalne znaków w haśle.

Możesz użyć [programu PowerShell, aby zapobiec wygaśnięciu hasła](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) na użytkowników, jeśli tworzysz tożsamości w usłudze Azure AD bezpośrednio. Organizacje przy użyciu lokalnej AD z usługą Azure AD Connect do synchronizacji tożsamości do usługi Azure AD (określana także jako wdrożenie hybrydowe), należy wdrożyć w środowisku lokalnym [zasady haseł inteligentne](https://aka.ms/passwordguidance) przy użyciu [zasad grupy dla domeny ustawienia](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) lub [programu Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

Usługa Azure Active Directory [dynamiczne zakazanych haseł](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) funkcja używa bieżącego zachowania osoba atakująca, aby uniemożliwić użytkownikom ustawianie haseł, które można łatwo złamać. Ta funkcja jest zawsze włączone i organizacje we wdrożeniu hybrydowym mogą korzystać z tej funkcji, włączając [funkcji zapisywania zwrotnego haseł](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-writeback) lub mogą wdrożyć [ochrona za pomocą hasła usługi Azure AD dla systemu Windows Server Active Katalog](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). Ochrony hasłem w usłudze Azure AD blokuje użytkownikom wybór typowe hasła ogólnie rzecz biorąc, a niestandardowe haseł można skonfigurować.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Ochrona przed ujawnionymi poświadczeniami i dodać odporność na awarie

Jeśli Twoja organizacja korzysta z rozwiązania z tożsamością hybrydową, powinien Włączanie synchronizacji skrótów haseł z następujących dwóch powodów:

* [Użytkownicy z ujawnionymi poświadczeniami](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) raportu w usłudze Azure AD management ostrzega par nazwa użytkownika i hasło, które zostały ujawnione w "ciemnych sieci web." Za pośrednictwem ponownemu użyciu starych wyłudzania informacji, złośliwe oprogramowanie i hasło w witrynach innych firm, które są później naruszają przeciek radykalnie ilości hasła. Firma Microsoft umożliwia znalezienie wiele z nich wyciek poświadczeń i poinformuje, w tym raporcie, jeśli spełniają poświadczeń w organizacji —, ale tylko wtedy, gdy [Włączanie synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* W przypadku awarii lokalnej (na przykład w na atak z wykorzystaniem oprogramowania wymuszającego okup) będziesz mieć możliwość przełączania do [uwierzytelniania w chmurze za pomocą synchronizacji skrótów haseł](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Ta metoda uwierzytelniania kopii zapasowej pozwoli nadal mieć dostęp do aplikacji skonfigurowanych do uwierzytelniania przy użyciu usługi Azure Active Directory, w tym usługi Office 365.

Dowiedz się więcej o tym, jak [synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) działa.

### <a name="implement-ad-fs-extranet-lockout"></a>Blokowanie ekstranetu w implementacji usługi AD FS

Organizacje, które skonfigurowanie aplikacji bezpośrednie uwierzytelnianie usługi Azure AD, korzystać z [usługi Azure AD inteligentnej blokady](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Jeśli używasz usług AD FS w systemie Windows Server 2012 R2, należy zaimplementować usługi AD FS [ochrony blokady ekstranetu](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Jeśli używasz usług AD FS w systemie Windows Server 2016, należy zaimplementować [blokada ekstranetu — inteligentne](https://support.microsoft.com/en-us/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Usługi AD FS inteligentne ekstranetu który blokady chroni przed atakami ataki, która docelowa usług AD FS podczas uniemożliwiając użytkownikom zablokowania w usłudze Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Skorzystaj z zalet wewnętrznie bezpieczny i łatwiejsze do korzystania z poświadczeń

Za pomocą [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), możesz zastąpić hasła silnego uwierzytelniania dwuskładnikowego na komputerach i urządzeniach przenośnych. To uwierzytelnianie składa się z nowym typem poświadczeń użytkownika, który jest powiązany z urządzeniem i używa biometryczne lub numer PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Krok 2 — zmniejszyć obszar ataków

Biorąc pod uwagę wszechobecność złamania hasła, minimalizując obszar narażony na ataki w Twojej organizacji ma krytyczne znaczenie. Wyeliminowanie protokołów starsze, mniej bezpieczna opcja, ograniczanie dostępu do zapisu wskazuje i wykonywania bardziej znaczące kontrolę nad dostęp administracyjny do zasobów może zmniejszyć obszar powierzchni ataku.

### <a name="block-legacy-authentication"></a>Blokuj starsze uwierzytelnianie

Aplikacje przy użyciu starszej wersji metody uwierzytelniania w usłudze Azure AD i dostęp do danych firmowych stanowić innego ryzyka dla organizacji. Przykłady aplikacji przy użyciu starszej wersji uwierzytelniania są klientami POP3, IMAP4 lub SMTP. Uwierzytelnianie starszych aplikacji uwierzytelnianie w imieniu użytkownika i uniemożliwić to zaawansowane oceny zabezpieczeń usługi Azure AD. Alternatywne, nowoczesnego uwierzytelniania, zmniejsza zagrożenie bezpieczeństwa, ponieważ obsługuje ona uwierzytelnianie wieloskładnikowe i dostępu warunkowego. Zalecamy następujące trzy czynności:

1. Blok [starszych uwierzytelniania, jeśli używasz usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Instalator [usługi SharePoint Online i Exchange Online, aby korzystała z nowoczesnego uwierzytelniania](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Użyj [zasady dostępu warunkowego, aby zablokować starsze uwierzytelnianie](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication).

### <a name="block-invalid-authentication-entry-points"></a>Punkty wejścia nieprawidłowe uwierzytelnienie bloku

Za pomocą mentality naruszenia przyjmowanie, należy zmniejszyć wpływ poświadczenia użytkownika ze złamanymi zabezpieczeniami ich. Dla każdej aplikacji w danym środowisku należy wziąć pod uwagę przypadki użycia prawidłowy: grupy, które sieci, które urządzenia i inne elementy są autoryzowane — a następnie zablokować pozostałe. Pamiętaj, aby ograniczyć stosowanie [wysoce uprzywilejowanych lub usługi kont](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices). Za pomocą [dostępu warunkowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), można kontrolować, jak autoryzowanym użytkownikom ich aplikacji i zasobów na podstawie określonych warunków, które należy zdefiniować.

Zwróć szczególną uwagę na konta usługi (konta używane do wykonywania zadań w zautomatyzowany sposób). Przy użyciu dostępu warunkowego, można upewnij się, takie konta można uruchamiać tylko z usługą z adresu IP, oraz porze dnia, który jest odpowiedni.

### <a name="implement-azure-ad-privileged-identity-management"></a>Implementowanie usługi Azure AD Privileged Identity Management

Inny wpływ "założenie złamania zabezpieczeń" jest konieczność zminimalizować prawdopodobieństwo pracują przez o naruszeniu bezpieczeństwa konta przy użyciu ról uprzywilejowanych. 

[Usługa Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) pomaga zminimalizować uprawnień konta, która ułatwia:

* Identyfikowanie i zarządzaj nimi użytkowników przypisanych do ról administracyjnych.
* Dowiedz się, role nieużywany lub nadmierne uprawnień, które należy usunąć.
* Ustanowienie reguły, aby upewnić się, że uprzywilejowanych ról są chronione przez usługę uwierzytelnianie wieloskładnikowe.
* Ustanów zasady, aby upewnić się, że ról uprzywilejowanych są udzielane tylko czas wystarczający do wykonania uprzywilejowanych zadania.

Włączanie usługi Azure AD PIM, a następnie wyświetlić użytkowników, którzy są przypisane role administracyjne i usunąć niepotrzebne konta w ramach tych ról. Dla pozostałych użytkowników uprzywilejowanych przenieś je ze stałych na kwalifikujących. Na koniec ustanowić odpowiednich zasad, aby upewnić się, kiedy ich potrzebują uzyskać dostęp do tych ról uprzywilejowanych, znacznie im to bezpieczne.

W ramach wdrażania proces konta uprzywilejowanego, postępuj zgodnie z [najlepsze rozwiązanie, aby utworzyć co najmniej dwa konta awaryjne](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) się upewnić, że masz dostęp do usługi Azure AD, jeśli samodzielnie zablokowania.

## <a name="step-3---automate-threat-response"></a>Krok 3 — Automatyzowanie reagowania na zagrożenia

Usługa Azure Active Directory zawiera wiele funkcji, które automatycznie przechwytuje ataków, aby usunąć opóźnienie między wykrywania i reagowania. Można zmniejszyć koszty i zagrożenia, jeśli zmniejszysz przestępców czasu umożliwia osadzanie się w środowisku. Oto konkretne kroki, które można wykonać.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementowanie zasad zabezpieczeń ryzyka użytkownika przy użyciu usługi Azure AD Identity Protection

Ryzyko związane z użytkownikiem oznacza prawdopodobieństwo tożsamości użytkownika zostało naruszone i jest obliczana na podstawie [zdarzeń o podwyższonym ryzyku użytkownika](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) skojarzonych z tożsamością użytkownika. Zasad ryzyka dla użytkownika jest zasady dostępu warunkowego, które ocenia poziom ryzyka do określonego użytkownika lub grupy. Oparte na małej, średnie, wysoki poziom ryzyka, zasady można skonfigurować do blokowania dostępu lub wymagają zmiany bezpieczne hasło, za pomocą uwierzytelniania wieloskładnikowego. Firma Microsoft zaleca się wymagać zmiany hasła bezpiecznego dla użytkowników o wysokim ryzyku.

![Użytkownicy oflagowani w związku z ryzykiem](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementowanie zasad ryzyka dla logowania za pomocą usługi Azure AD Identity Protection

Ryzyko logowania jest prawdopodobieństwo, którą ktoś inny niż właściciel konta próbuje zalogować się przy użyciu tożsamości. A [zasad ryzyka logowania](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) to zasady dostępu warunkowego, które ocenia poziom ryzyka do określonego użytkownika lub grupy. Zależnie od poziomu ryzyka (wysoka/średni/niska), zasad można skonfigurować do blokowania dostępu lub wymusić uwierzytelnianie wieloskładnikowe. Upewnij się, że możesz wymusić uwierzytelnianie wieloskładnikowe, na nośniku lub powyżej ryzyka logowania.

![Zaloguj się z anonimowych adresów IP](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Krok 4 — Zwiększ świadomość

Inspekcja i rejestrowanie zdarzeń związanych z zabezpieczeniami i powiązanych alertów są niezbędnych składników strategii skutecznej ochrony. Dzienniki zabezpieczeń i raporty umożliwiają rekord elektronicznych podejrzanych działań i wykrywać wzorce, które mogą wskazywać próbę lub niepowodzenia zewnętrznych penetracji sieci i atakami wewnętrznymi pomocy. Inspekcja służy do monitorowania aktywności użytkownika, zgodność z przepisami dokumentu, wykonywać analizy śledczej i nie tylko. Alerty zapewniają powiadomienia zdarzeń zabezpieczeń.

### <a name="monitor-azure-ad"></a>Monitorowanie usługi Azure AD

Funkcje i usługi Microsoft Azure umożliwiają konfigurowalne zabezpieczeń, inspekcji i rejestrowania opcji, aby pomóc w identyfikowaniu luk w Twojej zasad i mechanizmów zabezpieczeń i rozwiązać te luki, aby uniknąć naruszeń. Możesz użyć [Azure rejestrowanie i inspekcja](https://docs.microsoft.com/azure/security/azure-log-audit) i użyj [raporty dotyczące inspekcji w portalu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorowanie usługi Azure AD Connect Health w środowiskach hybrydowych

[Monitorowanie usług AD FS za pomocą usługi Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) zapewnia większą wglądu w potencjalne problemy i widoczność ataków na infrastrukturę usług AD FS. Program Azure AD Connect Health zapewnia alerty o szczegółowe informacje, kroki rozwiązania i linki do powiązanej dokumentacji; analizy użycia dotyczące kilka metryk związane z ruchu uwierzytelniania. Monitorowanie wydajności i raporty.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorowanie zdarzeń dotyczących usługi Azure AD Identity Protection

[Usługa Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) znajduje się powiadomienie z monitorowaniem i raportowaniem narzędzie do wykrywania potencjalnych luk w zabezpieczeniach wpływających na tożsamości w organizacji. Wykrycia zdarzenia o podwyższonym ryzyku, takich jak ujawnione poświadczenia, więc podróż jest wykluczona, i logowania z zainfekowanych urządzeń anonimowych adresów IP adresy IP skojarzone z podejrzanych działań i nieznanych lokalizacjach. Włącz alerty Powiadomienie, aby otrzymywać wiadomości e-mail, obejmującym użytkowników narażonych na ryzyko i/lub co tydzień wiadomość e-mail z podsumowaniem.

![Użytkownicy oflagowani w związku z ryzykiem](media/azure-ad/azure-ad-sec-steps3.png)

## <a name="step-5---enable-end-user-self-help"></a>Krok 5 — Włączanie użytkowników końcowych w samodzielnej pomocy

Jak najszerzej będziesz chciał równoważyć zabezpieczeń z produktywności. Wzdłuż tych samych wierszy zbliża się do pracy na platformie sposób myślenia, że ustawiasz podstawa zabezpieczeń w perspektywie długoterminowej możesz usunąć Ogranicz liczbę problemów z Twojej organizacji, umożliwiając użytkownikom pozostając czujność. 

### <a name="implement-self-service-password-reset"></a>Implementowanie samoobsługowego resetowania haseł

Azure [samoobsługowego resetowania haseł (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) oferuje prosty oznacza, że dla administratorów IT umożliwić użytkownikom możliwość resetowania lub odblokowywania ich haseł lub kont bez interwencji administratora. System obejmuje szczegółowe raporty pozwalające śledzić, kiedy użytkownicy korzystają z systemu, oraz powiadomienia ostrzegające o jego nieprawidłowym użyciu lub nadużyciach związanych z zabezpieczeniami. 

### <a name="implement-self-service-group-management"></a>Implementowanie Samoobsługowe zarządzanie grupami

Usługa Azure AD zapewnia możliwość zarządzania dostępem do zasobów przy użyciu grup zabezpieczeń i grup usługi Office 365. Tych grup mogą być zarządzane przez właścicieli grupy zamiast Administratorzy IT. Znane jako [Samoobsługowe zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), ta funkcja umożliwia właściciele grupy, którzy nie mają przypisanych rola administracyjna do tworzenia grup i zarządzanie nimi bez konieczności polegania na administratorach w sprawie ich żądania obsługi.

### <a name="implement-azure-ad-access-reviews"></a>Przeglądy dostępu wdrożenie usługi Azure AD

Za pomocą [przeglądy dostępu w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), można zarządzać członkostwem w grupach, dostępem do aplikacji przedsiębiorstwa i przypisaniami ról uprzywilejowanych, aby upewnić się, obsługa standardu zabezpieczeń, który nie zapewnia użytkownikom dostęp do rozszerzonych okresów czas, kiedy ich nie potrzebują.

## <a name="summary"></a>Podsumowanie

Istnieje wiele aspektów, które bezpiecznej infrastruktury tożsamości, ale tej listy kontrolnej w pięciu krokach pomoże szybko wykonać infrastruktury tożsamości bezpieczniejsze i bezpiecznego:

* Wzmocnij swoje poświadczenia.
* Zmniejszenie obszaru powierzchni ataku.
* Zautomatyzuj reagowania na zagrożenia.
* Zwiększ świadomość inspekcji i monitorowania.
* Zabezpieczenia dla bardziej przewidywalna i kompletne przez użytkownika końcowego z samodzielnej pomocy.

Jesteśmy wdzięczni za jak poważny bezpieczeństwa tożsamości i mamy nadzieję, że ten dokument jest przydatne plan w celu bardziej bezpieczne działania dla Twojej organizacji.

## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz pomocy, planowanie i wdrażanie zaleceń, zobacz [wdrożenia planów projektów w usłudze Azure AD](http://aka.ms/deploymentplans) Aby uzyskać pomoc.
