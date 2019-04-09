---
title: Planowanie i wykonywanie wdrożenia usługi Azure Multi-Factor Authentication — usługi Azure Active Directory
description: Planowanie wdrożenia systemu Microsoft Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b91af553c402cc1cb241e51e2bb2289bf45b1825
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269031"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication"></a>Planowanie oparte na chmurze usługi Azure Multi-Factor Authentication

Osoby łączysz się do zasobów organizacji w coraz bardziej skomplikowane sytuacje. Osoby, nawiąż połączenie z urządzeń należących do organizacji, osobistych i publicznych włączać i wyłączać firmową siecią przy użyciu smartfonach, tabletach, komputery i laptopy, często na wielu platformach. W tym świecie zawsze podłączonej do wielu urządzeń i dla wielu platform bezpieczeństwa kont użytkowników jest ważniejsze niż kiedykolwiek wcześniej. Hasła, niezależnie od ich złożoności, stosować w przypadku urządzeń, sieci i platform nie są wystarczające, aby zapewnić bezpieczeństwo konta użytkownika, a zwłaszcza w przypadku, gdy użytkownicy często wykonują ponowne używanie haseł na kontach. Zaawansowane techniki wyłudzania informacji oraz innych inżynierii społecznej, który ataków skutkujących nazwy użytkowników i hasła są opublikowane i sprzedawany przez Internet.

[Usługa Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) pomaga w zabezpieczeniu dostępu do danych i aplikacji. Zapewnia dodatkową warstwę zabezpieczeń przy użyciu drugiej formy uwierzytelniania. Organizacje mogą używać [dostępu warunkowego](../conditional-access/overview.md) że rozwiązanie nie będzie mieści się w ich własnych potrzeb.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wdrażania usługi Azure Multi-Factor Authentication, istnieją elementy wymagane wstępnie, które należy rozważyć.

| Scenariusz | Wymagania wstępne |
| --- | --- |
| **Tylko w chmurze** środowiska tożsamości za pomocą nowoczesnego uwierzytelniania | **Brak dodatkowych wymagań wstępnych zadań** |
| **Hybrydowe** scenariuszy tożsamości | [Program Azure AD Connect](../hybrid/whatis-hybrid-identity.md) jest wdrażana i tożsamości użytkowników są synchronizowanych lub Sfederowanych z lokalnej usługi Active Directory domeny za pomocą usługi Azure Active Directory. |
| W środowisku lokalnym starsze aplikacje opublikowane, aby uzyskać dostęp do chmury | Usługa Azure AD [serwera Proxy aplikacji](../manage-apps/application-proxy.md) jest wdrażana. |
| Korzystanie z usługi Azure MFA przy użyciu uwierzytelniania usługi RADIUS | A [serwera zasad sieciowych (NPS)](howto-mfa-nps-extension.md) jest wdrażana. |
| Użytkownicy mają pakiet Microsoft Office 2010 lub starszy lub Apple Mail dla systemu iOS 11 lub starszy | Uaktualnianie do [pakietu Microsoft Office 2013 lub nowszy](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) i Apple mail dla systemu iOS 12 lub nowszy. Dostęp warunkowy nie jest obsługiwana przez protokoły uwierzytelniania w starszej wersji. |

## <a name="plan-user-rollout"></a>Planowanie wdrożenia użytkownika

Plan wdrożenia usługi MFA powinno obejmować wdrożenia pilotażowego następuje etapach wdrażania, które znajdują się w pojemności usługi pomocy technicznej. Rozpocznij wdrożenia, stosując zasady dostępu warunkowego dla małej grupy użytkowników pilotażowych. Po dokonaniu oceny wpływu na użytkowników pilotażowych, proces używany i zachowania rejestracji, możesz dodać więcej grup do zasad lub dodać większą liczbę użytkowników do istniejącej grupy.

### <a name="user-communications"></a>Komunikacja użytkownika

Koniecznie informować użytkowników, w przypadku komunikacji planowane o nadchodzących zmianach, wymagań dotyczących rejestracji usługi Azure MFA i wszelkie akcje niezbędne użytkownika. Zaleca się, że wiadomości są opracowywane w połączeniu z przedstawicieli w Twojej organizacji, takich jak działów komunikacji, zarządzanie zmianami i zarządzania zasobami ludzkimi.

Firma Microsoft udostępnia [szablony do komunikacji](https://aka.ms/mfatemplates) i [dokumentację użytkownika końcowego](../user-help/security-info-setup-signin.md) ułatwiające wersję roboczą komunikacji. Możesz wysłać użytkownikom [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com) można zarejestrować bezpośrednio wybierając **zabezpieczające** linki na tej stronie.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Usługa Azure Multi-Factor Authentication jest wdrażana przez wymuszenie zasad przy użyciu dostępu warunkowego. A [zasad dostępu warunkowego](../conditional-access/overview.md) mogą wymagać od użytkowników przeprowadzać uwierzytelnianie wieloskładnikowe w przypadku spełnienia określonych kryteriów takich jak:

* Wszyscy użytkownicy, określonego użytkownika, członek grupy lub przypisanej roli
* Dostęp do aplikacji w chmurze
* Platforma urządzeń
* Stan urządzenia
* Lokalizacja sieciowa lub adres IP zlokalizowane geograficznie
* Aplikacje klienckie
* Ryzyko logowania (wymaga Identity Protection)
* Zgodne urządzenie
* Urządzenia przyłączone do hybrydowej usługi Azure AD
* Aplikacja kliencka zatwierdzone
 

Umożliwia plakaty można dostosowywać i szablonów wiadomości e-mail [materiałów wdrożenie uwierzytelniania wieloskładnikowego] Wdrażanie uwierzytelniania wieloskładnikowego dla Twojej organizacji. (https://www.microsoft.com/en-us/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Włącz uwierzytelnianie wieloskładnikowe przy użyciu dostępu warunkowego

Zasady dostępu warunkowego wymuszać rejestrację, wymagając od użytkowników niezarejestrowanych ukończyć rejestrację, podczas pierwszego logowania, kwestią istotną.


[Usługa Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) przyczynia się zasady rejestracji i ryzyko automatycznego wykrywania i korygowania zasad do użycia usługi Azure Multi-Factor Authentication. Zasady mogą być tworzone, aby wymusić zmiany hasła, gdy istnieje zagrożenie, których bezpieczeństwo zostało naruszone tożsamości lub wymagać uwierzytelniania Wieloskładnikowego podczas logowania jest uznawany za ryzykowne przez następujące [zdarzenia](../reports-monitoring/concept-risk-events.md):

* Ujawnione poświadczenia
* Logowania z anonimowych adresów IP
* Niemożliwa podróż do nietypowych lokalizacji
* Logowania z nieznanych lokalizacji
* Logowania z zainfekowanych urządzeń
* Logowania z adresów IP z podejrzanymi działaniami

Niektóre zdarzenia o podwyższonym ryzyku wykrywane przez usługi Azure Active Directory Identity Protection występują w czasie rzeczywistym, a niektóre wymagane przetwarzanie w trybie offline. Administratorzy mogą wybrać zablokować użytkowników, którzy wykazują ryzykownego zachowania i korygowanie ręcznie, Wymagaj zmiany hasła lub wymagać usługi Multi-Factor authentication jako część swoich zasad dostępu warunkowego.

## <a name="define-network-locations"></a>Definiują lokalizacje sieciowe

Zaleca się, że do definiowania ich przy użyciu sieci organizacji korzystanie z dostępu warunkowego [lokalizacje z nazwą](../conditional-access/location-condition.md#named-locations). Jeśli Twoja organizacja używa Identity Protection, należy wziąć pod uwagę przy użyciu zasad opartych na ryzykach zamiast nazwane lokalizacje.

### <a name="configuring-a-named-location"></a>Konfigurowanie nazwanych lokalizacji

1. Otwórz **usługi Azure Active Directory** w witrynie Azure portal
2. Kliknij przycisk **dostępu warunkowego**
3. Kliknij przycisk **lokalizacje z nazwą**
4. Kliknij przycisk **nowej lokalizacji**
5. W **nazwa** , podaj opisową nazwę
6. Wybierz, czy definiujesz lokalizacji za pomocą zakresów adresów IP lub kraje/regiony
   1. Jeśli za pomocą zakresów adresów IP
      1. Zdecyduj, czy do oznaczania miejsca jako zaufany. Logowanie z zaufanej lokalizacji obniża ryzyko użytkownika związane z logowaniem. Oznacz tę lokalizację jako zaufany tylko, jeśli wiesz, że wprowadzone zakresy adresów IP są uznane i wiarygodne w Twojej organizacji.
      2. Określ zakresy adresów IP
   2. Jeśli przy użyciu kraje/regiony
      1. Rozwiń menu rozwijane i wybierz kraje lub regiony, które chcesz zdefiniować dla tej lokalizacji o nazwie.
      2. Zdecyduj, czy Uwzględnij nieznane obszary. Nieznane obszary to adresy IP, których nie można mapować na kraj/region.
7. Kliknij przycisk **Utwórz**

## <a name="plan-authentication-methods"></a>Planowanie metody uwierzytelniania

Administratorzy mogą wybrać [metod uwierzytelniania](../authentication/concept-authentication-methods.md) czy mają być dostępne dla użytkowników. Jest ważne zezwolić na więcej niż metoda uwierzytelniania pojedynczego, aby użytkownicy mieli dostępnej metody tworzenia kopii zapasowych w przypadku, gdy ich podstawową metodą jest niedostępny. Dostępne dla administratorów umożliwić są następujące metody:

### <a name="notification-through-mobile-app"></a>Powiadomienie przez aplikację mobilną

Powiadomienia wypychane są wysyłane do aplikacji Microsoft Authenticator na swoim urządzeniu przenośnym. Użytkownik przegląda powiadomienia i wybiera **Zatwierdź** aby ukończyć weryfikację. Powiadomienia wypychane za pośrednictwem aplikacji mobilnej udostępniają opcję najmniej istotnych dla użytkowników. Są one również opcja najbardziej niezawodną i bezpieczną, ponieważ używają one połączeń danych zamiast telefonii.

### <a name="verification-code-from-mobile-app"></a>Kod weryfikacyjny z aplikacji mobilnej

Aplikacja mobilna, takie jak aplikacja Microsoft Authenticator generuje nowego kodu weryfikacyjnego OATH co 30 sekund. Użytkownik wprowadza kod weryfikacyjny w interfejsie logowania. Opcję aplikacja mobilna może służyć informację określającą, czy numer telefonu ma danych lub w sieci komórkowej sygnału.

### <a name="call-to-phone"></a>Połączenie z telefonem

Automatyczne połączenie głosowe znajduje się użytkownik. Użytkownik odbierze połączenie i naciska klawisz **#** na klawiaturze telefonu w celu zatwierdzenia proces uwierzytelniania. Wywołania na telefon jest doskonałą metodę tworzenia kopii zapasowej dla powiadomienia lub weryfikacji kodu z aplikacji mobilnej.

### <a name="text-message-to-phone"></a>SMS na telefon

Wiadomość SMS zawierającą kod weryfikacyjny jest wysyłany do użytkownika, użytkownik jest monitowany o podanie kodu weryfikacyjnego w interfejsie logowania.

### <a name="choose-verification-options"></a>Wybierz opcje weryfikacji

1. Przejdź do **usługi Azure Active Directory**, **użytkowników**, **uwierzytelnianie wieloskładnikowe**.

   ![Dostęp do portalu usługi Multi-Factor Authentication z bloku użytkownicy usługi Azure AD w witrynie Azure portal](media/howto-mfa-getstarted/users-mfa.png)

1. W nowej karcie, która zostanie otwarta, przejdź do **ustawienia usługi**.
1. W obszarze **opcje weryfikacji**, zaznacz wszystkie pola dla metody dostępne dla użytkowników.

   ![Konfigurowanie metod weryfikacji, na karcie Ustawienia usługi Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Kliknij przycisk **Zapisz**.
1. Zamknij **ustawienia usługi** kartę.

## <a name="plan-registration-policy"></a>Planowanie zasad rejestracji

Administratorzy należy określić, jak użytkownicy będą rejestrować się w ich metod. Organizacje powinny wybierać [włączyć nowe środowisko rejestracji połączone](howto-registration-mfa-sspr-combined.md) dla usługi Azure MFA i haseł resetowania (SSPR). Samoobsługowe Resetowanie HASEŁ umożliwia użytkownikom do zresetowania swojego hasła w bezpieczny sposób za pomocą tych samych metod, których używają do uwierzytelniania wieloskładnikowego. Zalecane jest ona połączona rejestracji, obecnie dostępna w publicznej wersji zapoznawczej, ponieważ jest to doskonałe środowisko dla użytkownikom możliwość zarejestrowania się jeden raz dla obu usług. Włączenie tych samych metod dla funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania Wieloskładnikowego Azure umożliwi użytkownikom być zarejestrowana do używania obie funkcje.

### <a name="registration-with-identity-protection"></a>Rejestracja w usłudze Identity Protection

Jeśli Twoja organizacja używa usługi Azure Active Directory Identity Protection, [Konfigurowanie zasad rejestracji MFA](../identity-protection/howto-mfa-policy.md) na monitowanie użytkowników, aby zarejestrować podczas następnego logowania interakcyjnego.

### <a name="registration-without-identity-protection"></a>Rejestracja bez użycia produktu identity Protection

Jeśli Twoja organizacja nie ma licencji, które włączyć usługę Identity Protection, użytkownicy są monitowani o zarejestrować przy następnym uruchomieniu usługa MFA jest wymagana podczas logowania. Użytkowników nie może zostać zarejestrowana usługi MFA, jeśli nie korzystają z aplikacji chronionych za pomocą usługi MFA. Jest ważne dla wszystkich użytkowników zarejestrowane, tak aby nieupoważnione osoby nie można odgadnąć hasło użytkownika i rejestrowania dla usługi MFA w ich imieniu skutecznie przejmowaniu konta.

#### <a name="enforcing-registration"></a>Wymuszanie rejestracji

Wykonując następujące kroki zasad dostępu warunkowego można wymusić użytkownicy rejestrowali się w celu uwierzytelniania wieloskładnikowego

1. Utwórz grupę, dodać wszystkich użytkowników, nie jest obecnie zarejestrowany.
2. Przy użyciu dostępu warunkowego, wymuszanie uwierzytelniania wieloskładnikowego dla tej grupy, aby uzyskać dostęp do wszystkich zasobów.
3. Okresowo ponownie oceń członkostwa w grupie, a następnie usuń użytkowników, którzy zarejestrowali z grupy.

Zarejestrowanych i niezarejestrowanych użytkowników usługi Azure MFA może zidentyfikować za pomocą poleceń programu PowerShell, które zależą od [modułu MSOnline PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identyfikowanie użytkowników zarejestrowanych

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identyfikowanie użytkowników niezarejestrowanych

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

## <a name="plan-conditional-access-policies"></a>Planowanie zasady dostępu warunkowego

Zaplanuj strategię zasad dostępu warunkowego, który ustali, kiedy są wymagane uwierzytelnianie wieloskładnikowe i inne kontrolki, znaleźć [co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md).

Należy zapobiegać przypadkowemu zablokowania dostępu do dzierżawy usługi Azure AD. Pozwala ograniczyć wpływ przypadkowego braku dostępu administracyjnego przez [tworzenie dwóch lub więcej kont dostępu awaryjnego w Twojej dzierżawie](../users-groups-roles/directory-emergency-access.md) i wyłączając je z zasady dostępu warunkowego.

### <a name="create-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
1. Przejdź do **usługi Azure Active Directory**, **dostępu warunkowego**.
1. Wybierz **nowe zasady**.
1. Podaj znaczącą nazwę zasady.
1. W obszarze **użytkowników i grup**:
   * Na **Include** zaznacz **wszyscy użytkownicy** przycisku radiowego
   * Na **wykluczyć** kartę, zaznacz pole **użytkowników i grup** i wybierz pozycję konta dostępu awaryjnego.
   * Kliknij przycisk **Gotowe**.
1. W obszarze **aplikacje w chmurze**, wybierz opcję **wszystkie aplikacje w chmurze** przycisku radiowego.
   * OPCJONALNIE: Na **wykluczyć** karty, wybierz aplikacje w chmurze, które Twoja organizacja nie wymaga uwierzytelniania Wieloskładnikowego dla.
   * Kliknij przycisk **Gotowe**.
1. W obszarze **warunki** sekcji:
   * OPCJONALNIE: Po włączeniu usługi Azure Identity Protection można oceny ryzyka logowania jako część zasad.
   * OPCJONALNIE: Jeśli skonfigurowano zaufanych lokalizacji lub lokalizacje z nazwą, można określić zostać dołączone lub wykluczone z tych lokalizacji z zasad.
1. W obszarze **Grant**, upewnij się, że **udzielić dostępu** przycisk radiowy zostanie wybrany.
    * Pole wyboru dla **Wymagaj uwierzytelniania wieloskładnikowego**.
    * Kliknij pozycję **Wybierz**.
1. Pomiń **sesji** sekcji.
1. Ustaw **Włącz zasady** Przełącz, aby **na**.
1. Kliknij pozycję **Utwórz**.

![Tworzenie zasad dostępu warunkowego, aby włączyć usługę MFA dla użytkowników portalu platformy Azure w grupie pilotażowej](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>Planowanie integracji z lokalnymi systemami

Niektórych starszych i lokalnych aplikacji, które nie są uwierzytelniane bezpośrednio w odniesieniu do usługi Azure AD wymagają dodatkowych czynności w celu użycia usługi MFA w tym:

* Starsze lokalne aplikacje, które będą musieli używać serwera proxy aplikacji.
* Lokalne aplikacje usługi RADIUS, które będą musieli używać karty MFA za pomocą serwera NPS.
* Lokalnych aplikacji usług AD FS, które będą musieli używać usługi MFA karty przy użyciu usługi AD FS 2016.

Aplikacje, które uwierzytelniać się bezpośrednio z usługą Azure AD i korzystają z nowoczesnego uwierzytelniania (WS-Fed, SAML, OAuth, OpenID Connect) może być bezpośrednio korzystać z zasad dostępu warunkowego.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Usługa Azure MFA za pomocą serwera Proxy aplikacji usługi Azure AD

Aplikacje mogą być publikowane przechowywanych lokalnie do usługi Azure AD dzierżawy za pośrednictwem [serwera Proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md) i korzystać z zalet usługi Azure Multi-Factor Authentication, jeśli są one skonfigurowane do używania usługi Azure AD wstępnego uwierzytelniania.

Te aplikacje mogą ulec zasady dostępu warunkowego, które wymuszają usługi Azure Multi-Factor Authentication, podobnie jak wszystkie inne aplikacji platformy Azure zintegrowanych z usługą AD.

Podobnie jeśli uwierzytelnianie wieloskładnikowe systemu Azure jest wymuszane dla operacji logowania użytkownika, lokalnych aplikacji opublikowanych przy użyciu serwera Proxy aplikacji usługi Azure AD będą chronione.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrowanie usługi Azure Multi-Factor Authentication z serwera zasad sieciowych

Rozszerzenia serwera zasad sieciowych (NPS) dla usługi Azure MFA dodaje oparte na chmurze usługa MFA możliwości do infrastruktury uwierzytelniania przy użyciu istniejących serwerów. Za pomocą rozszerzenia serwera NPS można dodać połączenie telefoniczne, wiadomość SMS lub weryfikacji aplikacji telefonicznej do istniejącego przepływu uwierzytelniania. Integracja ta ma następujące ograniczenia:

* Przy użyciu protokołu CHAPv2 obsługiwane są tylko powiadomienia push aplikacji uwierzytelniania i połączenie głosowe.
* Nie można zastosować zasady dostępu warunkowego.

Rozszerzenia serwera NPS działa jako karty między RADIUS i oparte na chmurze usługi Azure MFA, aby zapewnić drugi składnik uwierzytelniania do ochrony [VPN](howto-mfa-nps-extension-vpn.md), [połączenia bramy usług pulpitu zdalnego](howto-mfa-nps-extension-rdg.md), lub inne możliwością RADIUS aplikacje. Użytkowników, że rejestrowanie na potrzeby usługi Azure MFA w tym środowisku zostaną wezwaniem dla wszystkich prób uwierzytelnienia, Brak oznacza zasady dostępu warunkowego usługi MFA jest zawsze wymagany.

#### <a name="implementing-your-nps-server"></a>Implementowanie serwera NPS

Jeśli masz wdrożonego wystąpienia serwera zasad Sieciowych i jest używane, odwołuje się jeszcze [integrowanie istniejącej infrastruktury NPS przy użyciu usługi Azure Multi-Factor Authentication](howto-mfa-nps-extension.md). Jeśli konfigurujesz serwer zasad Sieciowych po raz pierwszy, zapoznaj się [serwera zasad sieciowych (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) instrukcje. Wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule [komunikatami o błędach z rozszerzenia serwera NPS dla usługi Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Przygotowywanie usług NPS dla użytkowników, którzy nie są zarejestrowane na potrzeby usługi MFA

Wybierz, co się dzieje, gdy do uwierzytelniania użytkowników, którzy nie są zarejestrowane w usłudze MFA. Użyj ustawienia rejestru `REQUIRE_USER_MATCH` w ścieżce rejestru `HKLM\Software\Microsoft\AzureMFA` do sterowania zachowaniem funkcji. To ustawienie jest dostępna opcja jednej konfiguracji.

| Klucz | Wartość | Domyślne |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | PRAWDA / FAŁSZ | Nieustawione (odpowiada to TRUE) |

To ustawienie ma na celu ustalić, co należy zrobić, gdy użytkownik nie zostanie zarejestrowane na potrzeby usługi MFA. Efekty zmiany tego ustawienia są wymienione w poniższej tabeli.

| Ustawienia | Stan usługi MFA użytkownika | Efekty |
| --- | --- | --- |
| Klucz nie istnieje. | Niezarejestrowane | Żądanie uwierzytelniania MFA zakończy się niepowodzeniem |
| Wartość ustawiona na wartość True / nieustawiona | Niezarejestrowane | Żądanie uwierzytelniania MFA zakończy się niepowodzeniem |
| Klucz jest ustawiony na wartość False | Niezarejestrowane | Uwierzytelnianie bez usługi MFA |
| Klucz jest ustawiony na wartość FAŁSZ lub True | Zarejestrowane | Musi uwierzytelniać za pomocą usługi MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integracja z usługą Active Directory Federation Services

Jeśli Twoja organizacja jest sfederowana z usługą Azure AD, możesz użyć [usługi Azure Multi-Factor Authentication, aby zabezpieczyć zasoby usług AD FS](multi-factor-authentication-get-started-adfs.md), zarówno lokalne jak i w chmurze. Usługa Azure MFA umożliwia zmniejszenie hasła i podaj Bezpieczniejszym sposobem uwierzytelniania. Począwszy od systemu Windows Server 2016, można teraz skonfigurować uwierzytelnianie wieloskładnikowe Azure dla uwierzytelniania podstawowego.

W odróżnieniu od z usługami AD FS w systemie Windows Server 2012 R2, adaptera AD FS 2016 usługi Azure MFA integruje się bezpośrednio z usługą Azure AD i nie wymaga na lokalnym serwerze Azure MFA. Karty Azure MFA jest wbudowana w system Windows Server 2016, a nie trzeba, aby uzyskać więcej informacji.

Gdy za pomocą usługi Azure MFA przy użyciu usługi AD FS 2016 i aplikacji docelowej będzie podlegała zasadom dostępu warunkowego, istnieją dodatkowe zagadnienia:

* Dostęp warunkowy jest dostępny, gdy aplikacja jest Sfederowane z usługą AD FS 2016 jednostki uzależnionej firm do usługi Azure AD.
* Dostęp warunkowy nie jest dostępna, gdy aplikacji jest uzależniona do usługi AD FS 2016 i jest zarządzany lub Sfederowanych z usługą AD FS 2016.
* Dostęp warunkowy również nie jest dostępna podczas usługi AD FS 2016 jest skonfigurowany do używania usługi Azure MFA jako podstawową metodę uwierzytelniania.

#### <a name="ad-fs-logging"></a>Usługi AD FS rejestrowania

Standardowa usług AD FS 2016 logowania w dzienniku zabezpieczeń Windows i w dzienniku administratora programu AD FS zawiera informacje dotyczące żądania uwierzytelniania oraz ich powodzenia lub niepowodzenia. Dane dziennika zdarzeń w ramach tych zdarzeń będzie wskazywać, czy użyto usługi Azure MFA. Na przykład może zawierać identyfikator zdarzenia inspekcji FS AD 1200:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Odnawianie certyfikatów i zarządzanie nimi

Na każdym serwerze usług AD FS na komputerze lokalnym My Store będzie istnieć podpisem usługi Azure MFA certyfikatu zatytułowanym OU = Microsoft AD FS Azure MFA, która zawiera datę wygaśnięcia certyfikatu. Sprawdzania okresu ważności certyfikatu na każdym serwerze usług AD FS, aby określić datę wygaśnięcia.

Jeśli okres ważności certyfikatów jest bliskie wygaśnięcia, [rejestrowania i weryfikowania nowego certyfikatu uwierzytelniania Wieloskładnikowego na każdym serwerze usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Poniższe wskazówki opisano, jak zarządzać certyfikaty usługi Azure MFA na serwerach usług AD FS. Podczas konfigurowania usług AD FS z usługą Azure MFA, certyfikaty wygenerowane za pomocą `New-AdfsAzureMfaTenantCertificate` polecenia cmdlet programu PowerShell są ważne przez 2 lata. Odnów i zainstalować certyfikatów przed wygaśnięciem owalny przerw w działaniu usługi MFA.

## <a name="implement-your-plan"></a>Implementowanie własnego planu dotyczącego

Teraz, że zaplanowano rozwiązania, można wdrożyć, wykonując poniższe kroki:

1. Spełnia wszelkich niezbędnych wymagań wstępnych
   1. Wdrażanie [program Azure AD Connect](../hybrid/whatis-hybrid-identity.md) dla dowolnego scenariuszy hybrydowych
   1. Wdrażanie [serwera Proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md) dla dowolnej aplikacji w środowisku lokalnym opublikowana na potrzeby dostępu do chmury
   1. Wdrażanie [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) dla uwierzytelniania usługi RADIUS
   1. Upewnij się, że użytkownicy korzystają z nowoczesnego uwierzytelniania, włączone do obsługiwanych wersji programu Microsoft Office
1. Konfigurowanie wybrane [metody uwierzytelniania](#choose-verification-options)
1. Zdefiniuj swoje [o nazwie lokalizacje sieciowe](../conditional-access/location-condition.md#named-locations)
1. Wybierz grupy, aby rozpocząć wdrażanie usługi MFA.
1. Konfigurowanie usługi [zasady dostępu warunkowego](#create-conditional-access-policy)
1. Skonfiguruj zasady rejestracji usługi MFA
   1. [Połączone uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ](howto-registration-mfa-sspr-combined.md)
   1. Za pomocą [Identity Protection](../identity-protection/howto-mfa-policy.md)
1. Wyślij komunikacji użytkownika i Uzyskaj użytkownikom zarejestrowanie się na stronie [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Informacje o zachować, który jest zarejestrowany](#identify-non-registered-users)

## <a name="manage-your-solution"></a>Rozwiązania do zarządzania

Raporty dotyczące usługi Azure MFA

Usługa Azure Multi-Factor Authentication udostępnia raporty za pomocą witryny Azure portal:

| Raport | Lokalizacja | Opis |
| --- | --- | --- |
| Alerty użycia i nadużyć | Usługa Azure AD > logowania | Zawiera informacje na temat użycia ogólnej, podsumowanie dotyczące użytkowników i szczegóły użytkownika; jak również historię alertów oszustwa przesłanych w zakresie dat., określony. |

## <a name="troubleshoot-mfa-issues"></a>Rozwiązywanie problemów z usługi MFA

Znajdź rozwiązania typowych problemów z usługą Azure MFA na [artykułu Rozwiązywanie problemów z usługą Azure Multi-Factor Authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) na Support Center firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* [Jakie są metody uwierzytelniania?](concept-authentication-methods.md)
* [Włącz konwergentnej rejestracji dla usługi Azure Multi-Factor Authentication i Azure AD samoobsługowego resetowania haseł](concept-registration-mfa-sspr-converged.md)
* Dlaczego został użytkownika zostanie wyświetlony monit lub bez wyświetlania monitu o wykonać uwierzytelnianie wieloskładnikowe? Zobacz sekcję [raport logowania usługi Azure AD w raportach w dokumencie usługi Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
