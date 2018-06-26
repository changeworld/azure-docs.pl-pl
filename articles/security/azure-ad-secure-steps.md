---
title: Zabezpieczanie infrastruktury tożsamości w usłudze Azure Active Directory w pięciu krokach
description: W tym dokumencie przedstawiono listę ważne czynności, które Administratorzy powinien implementować ułatwia ich bezpieczne w organizacji przy użyciu możliwości usługi Azure AD
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: b15fff6e868bfac973f9d2a7277f0fac1e29d845
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938468"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Zabezpieczanie infrastruktury tożsamości w pięciu krokach

Jeśli czytasz ten dokument, znane znaczenia zabezpieczeń. Użytkownik prawdopodobnie już przenoszenia odpowiedzialność za zabezpieczanie Twojej organizacji. Jeśli chcesz przekonać inne znaczenie bezpieczeństwa wysyłać je odczytać najnowszą [raportu analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/security/intelligence-report).

Ten dokument pomogą Ci rozpocząć bezpieczniejsze strukturę, przy użyciu możliwości usługi Azure Active Directory przy użyciu listy kontrolnej pięciu krokach inokulację organizacji przed atakami przez.

Ta lista kontrolna pomoże Ci szybko wdrożyć krytyczne zalecane działania, aby chronić swoją organizację natychmiast przez wyjaśniający, jak:

* Wzmocnienie swoje poświadczenia.
* Zmniejsz obszaru powierzchni ataku.
* Automatyzowanie odpowiedzi zagrożeń.
* Podnoszenie Twojej Inspekcja i monitorowanie.
* Włącz bardziej przewidywalna i pełne użytkownika końcowego zabezpieczeń z samodzielnej pomocy.

> [!NOTE]
> Wiele zaleceń w tym dokumencie mają zastosowanie tylko do aplikacji, które są skonfigurowane do używania usługi Azure Active Directory jako dostawcy tożsamości. Konfigurowanie aplikacji do logowania jednokrotnego zapewnia korzyści zasad poświadczeń, wykrywanie zagrożeń inspekcji, rejestrowanie i inne funkcje, Dodaj do tych aplikacji. [Logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) jest podstawą — wszystkie te zalecenia są podstawę.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Przed rozpoczęciem: ochronę uprzywilejowanych kont za pomocą usługi MFA

Przed rozpoczęciem tej liście kontrolnej, upewnij się, że użytkownik nie zostać naruszone, będą podczas czytasz tej liście kontrolnej. Najpierw należy chronić swoje konta uprzywilejowanego.

Osoby atakujące, którzy uzyskać kontrolę nad kont uprzywilejowanych może zrobić bardzo szkody, jest bardzo istotne, aby chronić te konta, najpierw. Włącz i wymagają [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) dla wszystkich administratorów w organizacji za pomocą [ochrony linii bazowej](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Jeśli jeszcze nie zaimplementowano MFA, zrób to teraz! Jest to ważne.

Wszystko gotowe? Rozpocznijmy na liście kontrolnej.

## <a name="step-1---strengthen-your-credentials"></a>Krok 1 — wzmocnienie poświadczeń 

Większość naruszeń zabezpieczeń organizacji pochodzi przy użyciu konta naruszenia zabezpieczeń jeden z kilku metod, takich jak hasła osłony, powtarzania naruszenia lub wyłudzaniem informacji. Dowiedz się więcej na temat tego rodzaju ataki w tym wideo:
> [!VIDEO https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3016/player]

Jeśli użytkownicy w systemie tożsamości korzysta słabe hasła i nie wzmocnienia je przy użyciu uwierzytelniania wieloskładnikowego, nie jest kwestią lub w przypadku można zostać naruszone, będą — wystarczy "częstotliwość."

### <a name="make-sure-your-organization-use-strong-authentication"></a>Upewnij się, że Twojej organizacji użyj silnego uwierzytelniania

Podana częstotliwość hasła odgadnąć, phished, kradzieży ze złośliwym oprogramowaniem lub używane ponownie, bardzo ważne jest kopię hasła z jakiegoś silnych poświadczeń — Dowiedz się więcej o [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="turn-off-traditional-complexity-expiration-rules-and-start-banning-commonly-attacked-passwords-instead"></a>Wyłącz tradycyjnych złożoności, zasady wygasania i zakaz powszechnie początku ataku zamiast hasła

Wiele organizacji używa tradycyjnych złożoności (na przykład znaki specjalne) i zasady wygasania haseł. Badania firmy Microsoft wykazały, że te zasady są szkodliwe, co powoduje użytkownicy wybiorą hasła, które są łatwiejsze do odgadnięcia.

Zalecenia firmy Microsoft zgodnie z [wskazówki NIST](https://pages.nist.gov/800-63-3/sp800-63b.html), są następujące trzy implementacji:

1. Wymagaj hasła mają co najmniej 8 znaków. Już nie jest zawsze lepsze, zgodnie z ich spowodować, że użytkownicy wybiorą hasła przewidywalną, zapisywanie haseł w plikach lub Zapisz.
2. Wyłącz zasady wygaśnięcia, które dysków użytkownikom łatwe do odgadnięcia haseł, takich jak **Summer2018!**.
3. Wyłącz wymagania znak kompozycji i uniemożliwić użytkownikom często zaatakowane hasła, ponieważ powodują użytkownikom wybór podstawień przewidywalną znaków w haśle.

Można użyć [programu PowerShell, aby zapobiec wygaśnięciu hasła](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) dla użytkowników w przypadku utworzenia tożsamości w usłudze Azure AD bezpośrednio. Organizacji przy użyciu lokalnej usługi Active Directory z usługi Azure AD Connect do synchronizacji tożsamości usługi Azure AD (określane również jako hybrydowej), należy zaimplementować lokalnymi [zasady haseł inteligentnego](https://aka.ms/passwordguidance) przy użyciu [zasad grupy domeny ustawienia](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) lub [programu Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

Azure Active Directory w [dynamiczne hasła zabronione](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) funkcja używa bieżącej zachowanie atakującego aby uniemożliwić użytkownikom ustawianie hasła, które można łatwo złamać. Ta funkcja jest zawsze włączone i organizacji we wdrożeniu hybrydowym mogą korzystać z tej funkcji, należy włączyć [funkcji zapisywania zwrotnego haseł](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-writeback) lub mogą wdrożyć [ochrony hasłem usługi Azure AD dla systemu Windows Server Active Katalog](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). Ochrony hasłem w usłudze Azure AD uniemożliwia użytkownikom wybór ogólnie wspólnego hasła i niestandardowych haseł można skonfigurować.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Ochrona przed ujawnione poświadczenia i Dodaj odporność na awarie

Jeśli Twoja organizacja korzysta z rozwiązania z tożsamością hybrydową, należy włączyć synchronizacji skrótu hasła dla następujących dwóch powodów:

* [Użytkownikom ujawnione poświadczenia](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) raportu w pakiecie administracyjnym usługi Azure AD ostrzega par nazwa użytkownika i hasło, które zostały udostępnione w "ciemny sieci web." Za pomocą ponownemu wyłudzaniem informacji i złośliwego oprogramowania oraz prawidłowe hasło w witrynach innych firm, które są później naruszają przeciek niesamowite liczby hasła. Firma Microsoft umożliwia znalezienie wielu takich ujawnione poświadczenia i informuje, w tym raporcie spełniające poświadczeń w organizacji —, ale tylko wtedy, gdy użytkownik [Włączanie synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* W przypadku awarii lokalnymi (na przykład w celu ransomware) będzie możliwe przełączyć się do [uwierzytelniania w chmurze przy użyciu synchronizacji skrótu hasła](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Ta metoda uwierzytelniania kopii zapasowej pozwoli nadal uzyskiwać dostęp do aplikacji skonfigurowanych do uwierzytelniania w usłudze Azure Active Directory, łącznie z usługi Office 365.

Dowiedz się więcej na temat [synchronizacji skrótu hasła](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) działa.

### <a name="implement-ad-fs-extranet-lockout"></a>Blokowanie ekstranetu w implementacji usługi AD FS

Organizacje, które konfiguracji aplikacji, aby uwierzytelnić bezpośrednio do usługi Azure AD korzystać z [blokady inteligentnej usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Jeśli używasz usług AD FS, wdrożenie usług AD FS [blokowanie ekstranetu w usługach](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection). Blokowanie ekstranetu w usługach chroni przed atakami siłowymi, która docelowa usług AD FS podczas uniemożliwia użytkownikom są zablokowane w usłudze Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Korzystać z bardzo bezpieczny, łatwiej używać poświadczeń

Przy użyciu [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), można zastąpić hasła silnego uwierzytelniania dwuskładnikowego na komputerach i urządzeniach przenośnych. To uwierzytelnianie składa się z nowym typem poświadczeń użytkownika, który jest powiązany z urządzeniem i używa biometryczne lub numeru PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Krok 2 — zmniejszyć obszar narażony na ataki

Biorąc pod uwagę wszechobecność złamania hasła, minimalizując podatność na ataki w organizacji jest krytyczna. Wyeliminowanie protokołów starszej, mniej bezpieczne, ograniczanie dostępu do zapisu wskazuje i wykonywania bardziej znaczących kontroli dostępu administracyjnego do zasobów mogą pomóc w zmniejszeniu powierzchni ataku.

### <a name="block-legacy-authentication"></a>Uwierzytelnianie starszych bloku

Aplikacje przy użyciu starszej wersji metod uwierzytelniania za pomocą usługi Azure AD i dostępu do firmowych danych, stanowić innego ryzyka dla organizacji. Przykładów aplikacji przy użyciu starszej wersji uwierzytelniania klientów protokołu POP3, IMAP4 lub SMTP. Uwierzytelnianie starszych aplikacji uwierzytelnianie w imieniu użytkownika i uniemożliwić usługi Azure AD to zaawansowane oceny zabezpieczeń. Alternatywne, nowoczesnego uwierzytelniania, zmniejsza zagrożenie bezpieczeństwa, ponieważ obsługuje on uwierzytelniania wieloskładnikowego i dostępu warunkowego. Zaleca się następujące trzy czynności:

1. Blok [starszych uwierzytelniania, jeśli używasz usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Instalator [SharePoint Online i Exchange Online, aby korzystać z nowoczesnego uwierzytelniania](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Użyj [zasady dostępu warunkowego, aby zablokować starsze uwierzytelnianie](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication).

### <a name="block-invalid-authentication-entry-points"></a>Punkty wejścia nieprawidłowego uwierzytelniania bloku

Przy użyciu mentality naruszenia Przyjmij, należy zmniejszyć wpływ przejęciem poświadczeń gdy wystąpią. Dla każdej aplikacji w danym środowisku należy wziąć pod uwagę przypadki użycia prawidłową: grupy, które sieci, które urządzenia i inne elementy są autoryzowane — a następnie zablokować pozostałe. Należy ograniczyć stosowanie [kont wysoce uprzywilejowane lub service](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices). Z [dostępu warunkowego dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), można kontrolować, zdefiniuj sposób autoryzowanym użytkownikom ich aplikacji i zasobów na podstawie określonych warunków.

Należy zwrócić szczególną uwagę na konta usług (konta używane do wykonywania zadań w zautomatyzowany sposób). Przy użyciu dostępu warunkowego, można upewnij się, tych kont można uruchomić tylko z usługą z adresu IP, a porze dnia, który jest odpowiedni.

### <a name="implement-azure-ad-privileged-identity-management"></a>Wdrożenie usługi Azure AD Privileged Identity Management

Inny wpływ "założono naruszenia" jest potrzeba, aby zminimalizować prawdopodobieństwo, który zagrożone konto mogą pracować z ról uprzywilejowanych. 

[Azure AD Privileged Identity zarządzania (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) pomaga zminimalizować uprawnień konta przez pomaga:

* Identyfikowania i zarządzania nim użytkowników przypisanych do ról administracyjnych.
* Zrozumienie nieużywane lub ról nadmiernych uprawnień należy usunąć.
* Ustanowienie reguły, aby upewnić się, że uprzywilejowanych ról są chronione przez usługę Multi-Factor authentication.
* Ustanów zasady upewnij się, że role uprzywilejowane są przyznawane tylko tyle długo do wykonania uprzywilejowanych zadania.

Włączanie usługi Azure AD PIM, a następnie Wyświetl użytkowników, którym przypisano role administracyjne oraz usunąć niepotrzebne konta w ramach tych ról. Dla pozostałych użytkownicy o odpowiednich uprawnieniach przenieś je z stałych do kwalifikujących się. Na koniec ustanowienia odpowiednich zasad, aby upewnić się, gdy konieczne jest uzyskanie dostępu do tych ról uprzywilejowanych, ich zrobić bezpieczne.

W ramach wdrażania procesu konta uprzywilejowanego, wykonaj [najlepiej utworzyć co najmniej dwóch kont awaryjnych](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) się upewnić, że masz dostęp do usługi Azure AD, jeśli samodzielnie zablokowania.

## <a name="step-3---automate-threat-response"></a>Krok 3 — zautomatyzować odpowiedzi zagrożeń

Usługa Azure Active Directory ma wiele funkcji, które automatycznie przechwycić ataków, aby usunąć opóźnienie między wykrywania i odpowiedzi. Można zmniejszyć koszty i ryzyka, gdy zmniejszyć przestępców czasu umożliwia osadzanie się w środowisku. Poniżej przedstawiono konkretne kroki, które można wykonać.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Wdrożenie zasad zabezpieczeń ryzyka użytkownika przy użyciu usługi Azure AD Identity Protection

Ryzyko użytkownika wskazuje prawdopodobieństwo tożsamość użytkownika została naruszona poufność i jest obliczana na podstawie [zdarzenia o podwyższonym ryzyku użytkownika](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) skojarzonych z tożsamości użytkownika. Zasad ryzyka dla użytkownika jest zasady dostępu warunkowego, które ocenia poziom ryzyka do określonego użytkownika lub grupy. Oparte na niski, Średni, wysoki poziom ryzyka, zasady można skonfigurować blokującą dostęp lub zmień bezpieczne hasło, przy użyciu usługi Multi-Factor authentication. Zalecenia firmy Microsoft jest wymagane zmiany bezpiecznego hasła dla użytkowników o wysokim ryzyku.

![Użytkownicy oflagowani w związku z ryzykiem](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Wdrożenie zasad ryzyka logowania za pomocą usługi Azure AD Identity Protection

Logowanie ryzyko jest prawdopodobieństwo przez kogoś innego niż właściciela konta próbuje zalogować się przy użyciu tożsamości. A [zasad logowania ryzyka](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) są zasady dostępu warunkowego, które ocenia poziom ryzyka do określonego użytkownika lub grupy. Oparte na poziom ryzyka (wysokiej i średniej/niskim), zasady można skonfigurować do blokowania dostępu lub wymusić uwierzytelnianie wieloskładnikowe. Upewnij się, że możesz wymusić uwierzytelnianie wieloskładnikowe na nośniku lub powyżej logowania ryzyka.

![Zaloguj się z anonimowych adresów IP](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Krok 4 — podnoszenie użytkownika

Inspekcja i rejestrowanie zdarzeń związanych z zabezpieczeniami i powiązanych alertów są niezbędne składniki strategii skutecznej ochrony. Dzienniki zabezpieczeń i raporty umożliwiają elektronicznych rekordu podejrzanych działań i wykrywać wzorce wskazujące i nieudane próby zewnętrznych penetracji sieci i atakami wewnętrznymi pomocy. Inspekcja służy do monitorowania aktywności użytkownika, zgodność z przepisami dokumentu, dokonać analizy śledczej i inne. Alerty udostępniają powiadomienia zdarzeń zabezpieczeń.

### <a name="monitor-azure-ad"></a>Monitor usługi Azure AD

Funkcje i usługi Microsoft Azure umożliwiają zabezpieczeń można skonfigurować inspekcji i rejestrowania opcje ułatwiające identyfikowanie luk w zasadach zabezpieczeń i mechanizmów i Rozwiąż te luki, aby uniknąć naruszenia. Można użyć [Azure rejestrowanie i inspekcja](https://docs.microsoft.com/azure/security/azure-log-audit) i użyj [inspekcji raporty dotyczące działań w portalu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorowanie usługi Azure AD Connect Health w środowiskach hybrydowych

[Monitorowanie usług AD FS z usługi Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) zapewnia większą wgląd w potencjalne problemy i widoczności ataków na infrastrukturę usług AD FS. Azure AD Connect Health zapewnia alerty o informacje, kroki rozwiązania i linki do powiązanej dokumentacji; Analiza użycia kilka miar związane z uwierzytelniania ruchu. Monitorowanie wydajności i raportów.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorowanie zdarzeń usługi Azure AD Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) to powiadomienie, monitorowania i raportowania narzędzia można użyć do wykrywania potencjalne luki tożsamości organizacji. Wykrycia zdarzenia o podwyższonym ryzyku, takich jak ujawnione poświadczenia, niemożliwa podróż i logowania z zainfekowanych urządzeń anonimowych adresów IP, adresy IP skojarzone z podejrzanych działań i lokalizacje nieznany. Włącz alerty powiadomień do odbierania wiadomości e-mail z zagrożonych użytkowników i/lub co tydzień wiadomość e-mail z podsumowaniem.

![Użytkownicy oflagowani w związku z ryzykiem](media/azure-ad/azure-ad-sec-steps3.png)

## <a name="step-5---enable-end-user-self-help"></a>Krok 5. Włączanie użytkownika końcowego w samodzielnej pomocy

Możliwie można równoważyć zabezpieczeń dzięki produktywności. Wzdłuż tej samej linii zbliża się podróży z mindset, że ustawiasz podstawę do zabezpieczenia w dłuższej perspektywie należy usunąć tarcie z organizacji przy zachowując czujność nadawania uprawnień użytkowników. 

### <a name="implement-self-service-password-reset"></a>Implementowanie samoobsługowego resetowania hasła

Azure [samoobsługowego resetowania hasła (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) oferuje prosty oznacza dla administratorów IT umożliwić użytkownikom Resetowanie lub odblokować ich hasła lub kont bez interwencji administratora. System obejmuje szczegółowe raporty pozwalające śledzić, kiedy użytkownicy korzystają z systemu, oraz powiadomienia ostrzegające o jego nieprawidłowym użyciu lub nadużyciach związanych z zabezpieczeniami. 

### <a name="implement-self-service-group-management"></a>Implementowanie Samoobsługowe zarządzanie grupami

Usługa Azure AD zapewnia możliwość zarządzania dostępem do zasobów przy użyciu grup zabezpieczeń i grup usługi Office 365. Te grupy może być zarządzane przez właścicieli grupy zamiast Administratorzy IT. Znany jako [Samoobsługowe zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), ta funkcja umożliwia właścicieli grupy, którzy nie są przypisane do tworzenia grup i zarządzanie nimi bez polegania na administratorów do obsługi żądań ich roli administracyjnej.

### <a name="implement-azure-ad-access-reviews"></a>Wdrożenie usługi Azure AD dostęp przeglądów

Z [sprawdza dostęp do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), można zarządzać członkostwa w grupach, dostęp do aplikacji przedsiębiorstwa i okresy rozszerzony przypisań ról uprzywilejowanych do upewnij się, że obsługa standard zabezpieczeń, które nie zapewniają użytkownikom dostęp do czas, kiedy nie potrzebują.

## <a name="summary"></a>Podsumowanie

Istnieje wiele aspektów, które bezpiecznej infrastruktury tożsamości, ale ta lista kontrolna pięciu krokach pomoże Ci szybkie wykonywanie infrastruktury tożsamości bezpieczniejsze i bezpiecznego:

* Wzmocnienie swoje poświadczenia.
* Zmniejsz obszaru powierzchni ataku.
* Automatyzowanie odpowiedzi zagrożeń.
* Podnoszenie Twojej Inspekcja i monitorowanie.
* Włącz bardziej przewidywalna i pełne użytkownika końcowego zabezpieczeń z samodzielnej pomocy.

Dziękujemy za jak poważny bezpieczeństwa tożsamości i nadzieję, że ten dokument jest przydatne plan do bardziej bezpieczne strukturę dla Twojej organizacji.

## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz pomocy, aby zaplanować i wdrożyć zalecenia, można skorzystać z [wdrażania planów projektu usługi Azure AD](http://aka.ms/deploymentplans) Aby uzyskać pomoc.
