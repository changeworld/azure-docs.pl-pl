---
title: Azure Active Directory często zadawane pytania dotyczące zarządzania urządzeniami | Microsoft Docs
description: Azure Active Directory często zadawane pytania dotyczące zarządzania urządzeniami.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbba3f1b753738de57aa311387e522bae1b7b523
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499793"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory często zadawane pytania dotyczące zarządzania urządzeniami

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Pyt.: Urządzenie zostało ostatnio zarejestrowane. Dlaczego nie widzę urządzenia w obszarze Moje informacje o użytkowniku w Azure Portal? Lub dlaczego właściciel urządzenia jest oznaczony jako N/A dla urządzeń, które są dołączone do hybrydowego Azure Active Directory (Azure AD)?

**Odp.:** Urządzenia z systemem Windows 10, które są dołączone do hybrydowej usługi Azure AD, nie są wyświetlane w obszarze **urządzenia użytkowników**.
Użyj widoku **wszystkie urządzenia** w Azure Portal. Można również użyć polecenia cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) programu PowerShell.

W obszarze **urządzenia użytkowników**są wyświetlane tylko następujące urządzenia:

- Wszystkie urządzenia osobiste, które nie są dołączone do hybrydowej usługi Azure AD. 
- Wszystkie urządzenia z systemem innym niż Windows 10 lub Windows Server 2016.
- Wszystkie urządzenia z systemem innym niż Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Pyt.: Jak mogę wiedzieć, jaki jest stan rejestracji urządzenia klienta?

**Odp.:** W Azure Portal przejdź do pozycji **wszystkie urządzenia**. Wyszukaj urządzenie przy użyciu identyfikatora urządzenia. Sprawdź wartość w kolumnie Typ sprzężenia. Czasami urządzenie może być resetowane lub odtworzenie obrazu. W związku z tym należy również sprawdzić stan rejestracji urządzenia na urządzeniu:

- W przypadku urządzeń z systemem Windows 10 i Windows Server 2016 lub `dsregcmd.exe /status`nowszym Uruchom polecenie.
- W przypadku wcześniejszych wersji systemu operacyjnego Uruchom `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`polecenie.

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Pyt.: Wyświetlany jest rekord urządzenia w obszarze informacje o UŻYTKOWNIKu w Azure Portal. I widzę stan zarejestrowany na urządzeniu. Czy poprawnie skonfigurowano korzystanie z dostępu warunkowego?

**Odp.:** Stan dołączania urządzenia, wyświetlany przez **deviceID**, musi być zgodny z stanem w usłudze Azure AD i spełniać wszystkie kryteria oceny dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Wymagaj zarządzanych urządzeń dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices-"></a>Pyt.: Dlaczego mój użytkownicy widzą komunikat o błędzie "Twoja organizacja usunęła urządzenie" lub "Twoja organizacja wyłączyła urządzenie" na swoich urządzeniach z systemem Windows 10?

**Odp.:** Na urządzeniach z systemem Windows 10 dołączonych lub zarejestrowanych w usłudze Azure AD użytkownicy są wystawiani jako [podstawowy token odświeżania (PRT)](concept-primary-refresh-token.md) , który umożliwia logowanie jednokrotne. Ważność PRT jest oparta na validaity samego urządzenia. Ten komunikat jest wyświetlany, jeśli urządzenie zostało usunięte lub wyłączone w usłudze Azure AD bez inicjowania akcji z samego urządzenia. Urządzenie można usunąć lub wyłączyć w usłudze Azure AD w jednym z następujących scenariuszy: 

- Użytkownik wyłącza urządzenie w portalu My Apps. 
- Administrator (lub użytkownik) usuwa lub wyłącza urządzenie w Azure Portal lub przy użyciu programu PowerShell
- Tylko dołączona hybrydowa usługa Azure AD: Administrator usuwa z zakresu niezsynchronizowanych urządzenia jednostki organizacyjnej, co spowoduje usunięcie urządzeń z usługi Azure AD

Poniżej znajdują się informacje o tym, jak można skorygować te akcje.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>Pyt.: Moje urządzenie zostało wyłączone lub usunięte w Azure Portal lub przy użyciu programu Windows PowerShell. Jednak stan lokalny na urządzeniu wskazuje, że jest on nadal zarejestrowany. Co mam zrobić?

**Odp.:** Ta operacja jest zaprojektowana. W takim przypadku urządzenie nie ma dostępu do zasobów w chmurze. Administratorzy mogą wykonać tę akcję w przypadku starych, zagubionych lub skradzionych urządzeń, aby zapobiec nieautoryzowanemu dostępowi. Jeśli ta akcja została wykonana przypadkowo, należy ponownie włączyć lub ponownie zarejestrować urządzenie zgodnie z poniższym opisem

- Jeśli urządzenie zostało wyłączone w usłudze Azure AD, administrator z odpowiednimi uprawnieniami może je włączyć z poziomu portalu usługi Azure AD.  

 - Jeśli urządzenie zostało usunięte w usłudze Azure AD, należy ponownie zarejestrować urządzenie. Aby ponownie zarejestrować, musisz wykonać akcję ręczną na urządzeniu. Poniżej znajdują się instrukcje dotyczące ponownej rejestracji w zależności od stanu urządzenia. 

      Aby ponownie zarejestrować hybrydowe urządzenia z systemem Windows 10 i Windows Server 2016/2019 dołączone do usługi Azure AD, wykonaj następujące czynności:

      1. Otwórz wiersz polecenia jako administrator.
      1. Wprowadź polecenie `dsregcmd.exe /debug /leave`.
      1. Wyloguj się i zaloguj się, aby wyzwolić zaplanowane zadanie, które ponownie rejestruje urządzenie w usłudze Azure AD. 

      W przypadku wersji systemu operacyjnego Windows niższego poziomu, które są dołączone do hybrydowej usługi Azure AD, wykonaj następujące czynności:

      1. Otwórz wiersz polecenia jako administrator.
      1. Wprowadź polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Wprowadź polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      W przypadku urządzeń z systemem Windows 10 przyłączonych do usługi Azure AD wykonaj następujące czynności:

      1. Otwórz wiersz polecenia jako administrator
      1. Wprowadź `dsregcmd /forcerecovery` (Uwaga: Aby wykonać tę akcję, musisz być administratorem.
      1. W oknie dialogowym kliknij pozycję "Zaloguj się" i Kontynuuj proces logowania.
      1. Wyloguj się i zaloguj się ponownie na urządzeniu, aby zakończyć odzyskiwanie.

      W przypadku urządzeń z systemem Windows 10 zarejestrowanych w usłudze Azure AD wykonaj następujące czynności:

      1. Przejdź do pozycji **Ustawienia** > **konta** > **dostęp do**zasobów służbowych. 
      1. Wybierz konto i wybierz pozycję **Rozłącz**.
      1. Kliknij pozycję "+ Połącz" i ponownie Zarejestruj urządzenie, przechodząc przez proces logowania.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Pyt.: Dlaczego w Azure Portal są widoczne zduplikowane wpisy urządzeń?

**Odp.:**

- W przypadku systemów Windows 10 i Windows Server 2016 powtórzone próby odłączenia i ponownego dołączenia do tego samego urządzenia mogą spowodować zduplikowane wpisy. 
- Każdy użytkownik systemu Windows korzystający z **dodawania konta służbowego** tworzy nowy rekord urządzenia o tej samej nazwie.
- W przypadku wersji systemu Windows niższego poziomu, które są przyłączone do lokalnej domeny usługi Azure Directory, automatyczna rejestracja powoduje utworzenie nowego rekordu urządzenia o tej samej nazwie dla każdego użytkownika domeny, który loguje się na urządzeniu. 
- Komputer przyłączony do usługi Azure AD, który został wyczyszczony, ponownie zainstalowany i z tą samą nazwą, będzie wyświetlany jako inny rekord o tej samej nazwie.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Pyt.: Czy rejestracja urządzeń z systemem Windows 10 w usłudze Azure AD obsługuje moduły TPM w trybie FIPS?

**Odp.:** Nie, obecnie rejestracja urządzeń w systemie Windows 10 dla wszystkich stanów urządzeń — dołączanie hybrydowej usługi Azure AD, usługa Azure AD Join i usługa Azure AD zarejestrowane — nie obsługuje moduły TPM w trybie FIPS. Aby pomyślnie dołączyć lub zarejestrować się w usłudze Azure AD, należy wyłączyć tryb FIPS dla moduły TPM na tych urządzeniach.

---

**Pyt.: Dlaczego użytkownik może nadal uzyskiwać dostęp do zasobów z urządzenia, które zostało wyłączone w Azure Portal?**

**Odp.:** Zastosowanie odwołania może zająć do godziny.

>[!NOTE] 
>W przypadku zarejestrowanych urządzeń zalecamy wyczyszczenie urządzenia, aby upewnić się, że użytkownicy nie będą mogli uzyskać dostępu do zasobów. Aby uzyskać więcej informacji, zobacz [co to jest rejestracja urządzenia?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Usługa Azure AD Join — często zadawane pytania

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Pyt.: Jak mogę odłączania urządzenia dołączonego do usługi Azure AD lokalnie na urządzeniu?

**Odp.:** 
- W przypadku urządzeń przyłączonych do hybrydowej usługi Azure AD upewnij się, że funkcja automatycznej rejestracji została wyłączona. Następnie zaplanowane zadanie nie będzie ponownie rejestrować urządzenia. Następnie otwórz wiersz polecenia jako administrator i wprowadź `dsregcmd.exe /debug /leave`. Lub Uruchom to polecenie jako skrypt na kilku urządzeniach, aby rozłączyć zbiorczo.
- W przypadku urządzeń z czystym usługą Azure AD upewnij się, że masz konto administratora lokalnego w trybie offline lub utwórz je. Nie można zalogować się przy użyciu żadnych poświadczeń użytkownika usługi Azure AD. Następnie przejdź do pozycji **Ustawienia** > **konta** > **dostęp do**zasobów służbowych. Wybierz swoje konto i wybierz pozycję Rozłącz. Po wyświetleniu monitu postępuj zgodnie z monitami i podaj poświadczenia administratora lokalnego. Uruchom ponownie urządzenie, aby zakończyć proces rozłączania.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Pyt.: Czy użytkownicy mogą logować się do urządzeń przyłączonych do usługi Azure AD, które są usuwane lub wyłączone w usłudze Azure AD?

**Odp.:** Tak. System Windows ma wbudowaną funkcję username i Password, która umożliwia użytkownikom zalogowanym wcześniej dostęp do pulpitu nawet bez połączenia sieciowego. 

Gdy urządzenie zostanie usunięte lub wyłączone w usłudze Azure AD, nie jest znane jako urządzenie z systemem Windows. Użytkownicy, którzy wcześniej zalogowani, nadal uzyskują dostęp do pulpitu przy użyciu buforowanej nazwy użytkownika i hasła. Jednak po usunięciu lub wyłączeniu urządzenia użytkownicy nie mogą uzyskać dostępu do żadnych zasobów chronionych przez dostęp warunkowy oparty na urządzeniach. 

Użytkownicy, którzy nie zalogować się wcześniej, nie mogą uzyskać dostępu do urządzenia. Nie włączono dla nich buforowanej nazwy użytkownika i hasła. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Pyt.: Czy użytkownik może być zalogowany lub usuwany na urządzeniach dołączonych do usługi Azure AD

**Odp.:** Tak, ale tylko przez ograniczony czas. Gdy użytkownik zostanie usunięty lub wyłączony w usłudze Azure AD, nie jest on natychmiast znany na urządzeniu z systemem Windows. Wcześniej zalogowani użytkownicy mogą uzyskać dostęp do pulpitu przy użyciu buforowanej nazwy użytkownika i hasła. 

Zazwyczaj urządzenie ma świadomość stanu użytkownika w czasie krótszym niż cztery godziny. Następnie system Windows blokuje dostęp tych użytkowników do pulpitu. Gdy użytkownik jest usuwany lub wyłączony w usłudze Azure AD, wszystkie ich tokeny są odwoływane. Nie mogą uzyskać dostępu do żadnych zasobów. 

Usunięci lub wyłączni użytkownicy, którzy nie zalogują się wcześniej, nie mogą uzyskać dostępu do urządzenia. Nie włączono dla nich buforowanej nazwy użytkownika i hasła. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Pyt.: Dlaczego Moi użytkownicy mają problemy z urządzeniami przyłączonymi do usługi Azure AD po zmianie nazwy UPN?

**Odp.:** Obecnie zmiany nazw UPN nie są w pełni obsługiwane na urządzeniach dołączonych do usługi Azure AD. Uwierzytelnianie za pomocą usługi Azure AD kończy się niepowodzeniem po zmianie nazwy UPN. W związku z tym użytkownicy mają problemy z logowaniem jednokrotnym i dostępem warunkowym na swoich urządzeniach. W tej chwili użytkownicy muszą zalogować się do systemu Windows za pomocą kafelka "inny użytkownik" przy użyciu nowej nazwy UPN, aby rozwiązać ten problem. Obecnie pracujemy nad tym problemem. Jednak użytkownicy logujący się przy użyciu usługi Windows Hello dla firm nie będą napotykać tego problemu. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Pyt.: Moi użytkownicy nie mogą przeszukiwać drukarek z urządzeń przyłączonych do usługi Azure AD. Jak mogę włączyć drukowanie z tych urządzeń?

**Odp.:** Aby wdrożyć drukarki dla urządzeń przyłączonych do usługi Azure AD, zobacz [Wdrażanie chmury hybrydowej systemu Windows Server przy użyciu funkcji wstępnego uwierzytelniania](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Do wdrożenia w chmurze hybrydowej jest potrzebny lokalny serwer systemu Windows. Obecnie usługa drukowania oparta na chmurze jest niedostępna. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Pyt.: Jak mogę połączyć się ze zdalnym urządzeniem przyłączonym do usługi Azure AD?

**Odp.:** Zobacz [nawiązywanie połączenia z komputerem przyłączonym do zdalnego Azure Active Directory](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Pyt.: Dlaczego widzę *, że nie możesz w tym miejscu uzyskać od*użytkowników?

**Odp.:** Czy skonfigurowano pewne reguły dostępu warunkowego, aby wymagały określonego stanu urządzenia? Jeśli urządzenie nie spełnia kryteriów, użytkownicy są blokowani i widzą ten komunikat. Oceń reguły zasad dostępu warunkowego. Upewnij się, że urządzenie spełnia kryteria, aby uniknąć komunikatu.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Pyt.: Dlaczego niektórzy użytkownicy nie otrzymują załączonych do usługi Azure AD postanowień dotyczących uwierzytelniania wieloskładnikowego?

**Odp.:** Użytkownik może dołączyć lub zarejestrować urządzenie w usłudze Azure AD przy użyciu uwierzytelniania wieloskładnikowego. Następnie samo urządzenie będzie zaufanym drugim czynnikiem dla tego użytkownika. Za każdym razem, gdy ten sam użytkownik loguje się na urządzeniu i uzyskuje dostęp do aplikacji, usługa Azure AD traktuje urządzenie jako drugi czynnik. Dzięki temu użytkownik może bezproblemowo uzyskiwać dostęp do aplikacji bez dodatkowych pytań dotyczących uwierzytelniania wieloskładnikowego. 

Takie zachowanie:

- Ma zastosowanie do przyłączonych do usługi Azure AD i zarejestrowanych urządzeń usługi Azure AD — ale nie dla urządzeń przyłączonych do hybrydowej usługi Azure AD.
- Nie dotyczy żadnych innych użytkowników, którzy logują się na tym urządzeniu. Wszyscy inni użytkownicy, którzy uzyskują dostęp do tego urządzenia, otrzymują wyzwanie usługi uwierzytelniania wieloskładnikowego. Następnie mogą uzyskiwać dostęp do aplikacji, które wymagają uwierzytelniania wieloskładnikowego.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Pyt.: Dlaczego otrzymuję *nazwę użytkownika lub hasło jest niepoprawny* dla urządzenia dołączonego do usługi Azure AD?

**Odp.:** Typowe przyczyny tego scenariusza są następujące:

- Twoje poświadczenia użytkownika nie są już prawidłowe.
- Komputer nie może komunikować się z Azure Active Directory. Sprawdź, czy występują problemy z łącznością sieciową.
- Logowania federacyjne wymagają, aby serwer federacyjny obsługiwał punkty końcowe usługi WS-Trust, które są włączone i dostępne. 
- Włączono uwierzytelnianie przekazywane. Należy zmienić hasło tymczasowe po zalogowaniu się.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Pyt.: Dlaczego widzę *... Wystąpił błąd* . Czy podczas próby dołączenia komputera do usługi Azure AD?

**Odp.:** Ten błąd występuje podczas konfigurowania Azure Active Directory rejestracji w usłudze Intune. Upewnij się, że użytkownik, który próbuje nawiązać połączenie z usługą Azure AD, ma przypisaną poprawną licencję usługi Intune. Aby uzyskać więcej informacji, zobacz [Konfigurowanie rejestracji dla urządzeń z systemem Windows](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Pyt.: Dlaczego moja próba dołączenia komputera do usługi Azure AD kończy się niepowodzeniem, mimo że nie zostały wyświetlone żadne informacje o błędzie?

**Odp.:** Przyczyną jest to, że zalogowano się na urządzeniu przy użyciu lokalnego wbudowanego konta administratora. Przed rozpoczęciem instalacji Azure Active Directory należy utworzyć inne konto lokalne. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:What to certyfikaty MS-Organization-P2P-Access obecne na naszych urządzeniach z systemem Windows 10?

**Odp.:** Certyfikaty MS-Organization-P2P-Access są wystawiane przez usługę Azure AD zarówno do usługi Azure AD, jak i hybrydowych urządzeń przyłączonych do usługi Azure AD. Te certyfikaty służą do włączania zaufania między urządzeniami w tej samej dzierżawie na potrzeby scenariuszy usług pulpitu zdalnego. Jeden certyfikat jest wystawiony dla urządzenia, a drugi jest wystawiony dla użytkownika. Certyfikat urządzenia jest obecny w `Local Computer\Personal\Certificates` usłudze i jest ważny przez jeden dzień. Ten certyfikat zostanie odnowiony (przez wystawienie nowego certyfikatu), jeśli urządzenie jest nadal aktywne w usłudze Azure AD. Certyfikat użytkownika jest obecny w programie `Current User\Personal\Certificates` i ten certyfikat jest również ważny przez jeden dzień, ale jest wystawiany na żądanie, gdy użytkownik próbuje wykonać sesję pulpitu zdalnego na innym urządzeniu przyłączonym do usługi Azure AD. Nie jest odnawiana po wygaśnięciu. Oba te certyfikaty są wystawiane przy użyciu certyfikatu MS-Organization-P2P-Access obecnego w `Local Computer\AAD Token Issuer\Certificates`. Ten certyfikat jest wystawiany przez usługę Azure AD podczas rejestracji urządzenia. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why mogę zobaczyć wiele wygasłych certyfikatów wystawionych przez firmę MS-Organization-P2P — dostęp na naszych urządzeniach z systemem Windows 10? Jak można je usunąć?

**Odp.:** Wystąpił problem opisany w systemie Windows 10 w wersji 1709 i niższym, gdzie wygasłe certyfikaty MS-Organization-P2P są nadal dostępne w magazynie komputer z powodu problemów kryptograficznych. Użytkownicy mogą mieć problemy z łącznością sieciową, jeśli używasz dowolnego klienta sieci VPN (na przykład Cisco AnyConnect), który nie może obsłużyć dużej liczby wygasłych certyfikatów. Ten problem został rozwiązany w wersji Windows 10 1803, aby automatycznie usunąć wszystkie takie wygasłe certyfikaty MS-Organization-P2P-Access. Możesz rozwiązać ten problem, aktualizując urządzenia do systemu Windows 10 1803. Jeśli nie możesz zaktualizować programu, możesz usunąć te certyfikaty bez żadnego niekorzystnego wpływu.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Sprzężenie hybrydowe usługi Azure AD — często zadawane pytania

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Pyt.: Gdzie mogę znaleźć informacje dotyczące rozwiązywania problemów w celu zdiagnozowania niepowodzeń dołączania hybrydowej usługi Azure AD?

**Odp.:** Informacje dotyczące rozwiązywania problemów można znaleźć w następujących artykułach:

- [Rozwiązywanie problemów z Azure Active Directory hybrydowych dołączonych do urządzeń z systemami Windows 10 i Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z Azure Active Directory hybrydowymi podłączonymi do urządzeń niższego poziomu](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Pyt.: Dlaczego na liście urządzeń usługi Azure AD znajduje się zduplikowany zarejestrowany rekord usługi Azure AD dla urządzenia dołączonego do hybrydowej usługi Azure AD systemu Windows 10?

**Odp.:** Gdy użytkownicy dodają swoje konta do aplikacji na urządzeniu przyłączonym do domeny, może zostać wyświetlony monit o **dodanie konta do systemu Windows?** Jeśli w monicie wprowadzisz **wartość tak** , urządzenie zostanie zarejestrowane w usłudze Azure AD. Typ zaufania jest oznaczony jako zarejestrowany w usłudze Azure AD. Po włączeniu hybrydowego sprzężenia usługi Azure AD w organizacji urządzenie uzyskuje także przyłączoną do hybrydowej usługi Azure AD. Następnie dwa Stany urządzeń są wyświetlane dla tego samego urządzenia. 

Dołączanie hybrydowej usługi Azure AD ma pierwszeństwo przed zarejestrowanym stanem usługi Azure AD. Dlatego Twoje urządzenie jest uznawane za dołączoną hybrydową usługę Azure AD na potrzeby oceny uwierzytelniania i dostępu warunkowego. Możesz bezpiecznie usunąć zarejestrowany rekord urządzenia usługi Azure AD z portalu usługi Azure AD. Zapoznaj się z tym, aby [uniknąć tego podwójnego stanu na komputerze z systemem Windows 10](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Pyt.: Dlaczego Moi użytkownicy mają problemy dotyczące hybrydowych urządzeń z systemem Windows 10, które przyłączone do usługi Azure AD po zmianie nazwy UPN?

**Odp.:** Obecnie zmiany nazw UPN nie są w pełni obsługiwane w przypadku urządzeń przyłączonych do hybrydowej usługi Azure AD. Użytkownicy mogą logować się na urządzeniu i uzyskiwać dostęp do aplikacji lokalnych, więc uwierzytelnianie za pomocą usługi Azure AD kończy się niepowodzeniem po zmianie nazwy UPN. W związku z tym użytkownicy mają problemy z logowaniem jednokrotnym i dostępem warunkowym na swoich urządzeniach. W tej chwili musisz odłączyć urządzenie od usługi Azure AD (Uruchom polecenie "dsregcmd/Leave" z podniesionymi uprawnieniami) i ponownie Dołącz (automatycznie), aby rozwiązać ten problem. Obecnie pracujemy nad tym problemem. Jednak użytkownicy logujący się przy użyciu usługi Windows Hello dla firm nie będą napotykać tego problemu. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Pyt.: Czy hybrydowe urządzenia z systemem Windows 10 muszą być dołączone do kontrolera domeny w celu uzyskania dostępu do zasobów w chmurze?

**Odp.:** Nie, z wyjątkiem sytuacji, gdy hasło użytkownika zostanie zmienione. Po zakończeniu dołączania hybrydowego usługi Azure AD w systemie Windows 10, gdy użytkownik zalogował się co najmniej raz, urządzenie nie będzie wymagało dostępu do zasobów w chmurze do kontrolera domeny. System Windows 10 może pobrać Logowanie jednokrotne do aplikacji usługi Azure AD z dowolnego miejsca i z połączeniem internetowym, z wyjątkiem sytuacji, gdy hasło zostanie zmienione. Użytkownicy logujący się przy użyciu funkcji Windows Hello dla firm będą nadal korzystać z funkcji logowania jednokrotnego do aplikacji usługi Azure AD nawet po zmianie hasła, nawet jeśli nie mają wglądu w dane kontrolera domeny. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Pyt.: Co się stanie, jeśli użytkownik zmieni hasło i podejmie próbę zalogowania się do swojego urządzenia dołączonego do hybrydowej usługi Azure AD systemu Windows 10 poza siecią firmową?

**Odp.:** Jeśli hasło zostanie zmienione poza siecią firmową (na przykład za pomocą usługi Azure AD SSPR), użytkownik zaloguje się przy użyciu nowego hasła zakończy się niepowodzeniem. W przypadku urządzeń przyłączonych do hybrydowej usługi Azure AD jest to główny urząd Active Directory. Jeśli urządzenie nie ma linii wglądu do kontrolera domeny, nie można zweryfikować nowego hasła. Dlatego użytkownik musi nawiązać połączenie z kontrolerem domeny (za pośrednictwem sieci VPN lub w sieci firmowej), zanim będzie mógł zalogować się do urządzenia przy użyciu nowego hasła. W przeciwnym razie może zalogować się tylko przy użyciu starego hasła ze względu na możliwość logowania w systemie Windows. Jednak stare hasło jest unieważnione przez usługę Azure AD podczas żądań tokenów, a tym samym uniemożliwia logowanie jednokrotne i niepowodzenie wszystkich zasad dostępu warunkowego opartego na urządzeniach. Ten problem nie występuje, jeśli używasz usługi Windows Hello dla firm. 

---

## <a name="azure-ad-register-faq"></a>Rejestracja w usłudze Azure AD — często zadawane pytania

### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Pyt.: Czy można rejestrować urządzenia z systemem Android lub iOS BYOD?

**Odp.:** Tak, ale tylko w przypadku usługi rejestracji urządzeń platformy Azure i klientów hybrydowych. Usługa rejestracji urządzeń lokalnych w Active Directory Federation Services (AD FS) nie jest obsługiwana.

### <a name="q-how-can-i-register-a-macos-device"></a>Pyt.: Jak zarejestrować urządzenie macOS?

**Odp.:** Wykonaj następujące kroki:

1.  [Tworzenie zasad zgodności](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [Definiowanie zasad dostępu warunkowego dla urządzeń macOS](../active-directory-conditional-access-azure-portal.md) 

**Uwagi**

- Użytkownicy włączeni do zasad dostępu warunkowego potrzebują obsługiwanej [wersji pakietu Office dla programu macOS](../conditional-access/technical-reference.md#client-apps-condition) w celu uzyskania dostępu do zasobów. 
- Podczas pierwszego dostępu spróbuj użyć monitu o zarejestrowanie urządzenia przy użyciu portalu firmy.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarejestrowanych urządzeniach usługi Azure AD](concept-azure-ad-register.md)
- Dowiedz się więcej o urządzeniach przyłączonych do [usługi Azure AD](concept-azure-ad-join.md)
- Dowiedz się więcej o urządzeniach przyłączonych do [hybrydowej usługi Azure AD](concept-azure-ad-join-hybrid.md)
