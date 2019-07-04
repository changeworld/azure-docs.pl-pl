---
title: Zarządzanie urządzeniami w usłudze Azure Active Directory — często zadawane pytania | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory Zarządzanie urządzeniami — często zadawane pytania.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8802f9e5c84078725675d961ada7f8183c91c0ec
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481748"
---
# <a name="azure-active-directory-device-management-faq"></a>Zarządzanie urządzeniami w usłudze Azure Active Directory — często zadawane pytania

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Pyt.: Czy ostatnio zarejestrowane urządzenia. Dlaczego nie widzę urządzenia w obszarze Moje informacje o użytkowniku w witrynie Azure portal? Lub dlaczego jest właściciel urządzenia oznaczony jako urządzenia przyłączone do n/d dla hybrydowych usługi Azure Active Directory (Azure AD)?

**Odp.:** Urządzenia z systemem Windows 10, które są hybrydowe przyłączone do usługi Azure AD nie pojawiają się w **urządzenia użytkowników**.
Użyj **urządzeniom** widok w witrynie Azure portal. Możesz również użyć programu PowerShell [Get MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) polecenia cmdlet.

Następujące urządzenia są wyświetlane w obszarze **urządzenia użytkowników**:

- Przyłączone do wszystkich urządzeń osobistych, które nie są hybrydowej usługi Azure AD. 
- Wszystkie inne niż Windows 10 lub urządzeń z systemem Windows Server 2016.
- Wszystkie urządzenia inne niż Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Pyt.: Jak sprawdzić stanu rejestracji urządzenia klienta jest?

**Odp.:** W witrynie Azure portal przejdź do **urządzeniom**. Wyszukaj urządzenia za pomocą identyfikatora urządzenia. Sprawdź wartości w kolumnie Typ sprzężenia. Czasami urządzenie może zresetować lub odtworzony z obrazu. Dlatego istotne jest również sprawdzić stanu rejestracji urządzenia na urządzeniu:

- W przypadku systemu Windows 10 i Windows Server 2016 lub nowszym, systemem `dsregcmd.exe /status`.
- W przypadku wcześniejszych wersji systemu operacyjnego, systemem `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Pyt.: Czy mogę zobaczyć rekordem urządzenia w obszarze informacje o użytkowniku w witrynie Azure portal. I wyświetlić stan, ponieważ zarejestrowany na urządzeniu. Jestem I prawidłowo skonfigurowany do korzystania z dostępu warunkowego?

**Odp.:** Stan dołączania urządzenia, wyświetlane według **deviceID**musi odpowiadać stan w usłudze Azure AD i spełniać wszystkie kryteria oceny dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [wymagają zarządzanych urządzeń, aby uzyskać dostęp do aplikacji w chmurze przy użyciu dostępu warunkowego](../conditional-access/require-managed-devices.md).

---

### <a name="q-i-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered"></a>Pyt.: Po usunięciu urządzenia w witrynie Azure portal lub za pomocą programu Windows PowerShell. Ale stan lokalnego na urządzeniu jest wyświetlany komunikat, że pozostaje on zarejestrowany.

**Odp.:** Ta operacja jest celowe. Urządzenie nie ma dostępu do zasobów w chmurze. 

Jeśli chcesz ponownie zarejestrować, należy wykonać akcję ręczną na urządzeniu. 

Aby wyczyścić stanu dołączania do, z systemem Windows 10 i Windows Server 2016, które są przyłączone do domeny usługi Active Directory w środowisku lokalnym, wykonaj następujące czynności:

1. Otwórz wiersz polecenia jako administrator.
1. Wprowadź polecenie `dsregcmd.exe /debug /leave`.
1. Wyloguj się i zaloguj się w celu wyzwolenia zaplanowane zadanie, które rejestruje urządzenie ponownie przy użyciu usługi Azure AD. 

W wersjach systemu operacyjnego Windows niższego poziomu, które są przyłączone do domeny usługi Active Directory w środowisku lokalnym wykonaj następujące czynności:

1. Otwórz wiersz polecenia jako administrator.
1. Wprowadź polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
1. Wprowadź polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Pyt.: Dlaczego są wyświetlane urządzenia zduplikowanych wpisów w witrynie Azure portal?

**Odp.:**

- Dla systemu Windows 10 i Windows Server 2016 powtarzanych próbuje Odłącz i ponownie dołączyć w tym samym urządzeniu może spowodować zduplikowane wpisy. 
- Każdy użytkownik Windows **dodać pracy konta firmowego lub szkolnego** tworzy nowy rekord urządzenia o takiej samej nazwie urządzenia.
- Dla wersji systemu operacyjnego Windows niższego poziomu, które są przyłączone do domeny usługi Azure Directory w środowisku lokalnym automatycznej rejestracji tworzy nowy rekord urządzenia z taką samą nazwę urządzenia dla każdego użytkownika domeny, który loguje się do urządzenia. 
- Komputerze dołączonym do usługi Azure AD, który wyczyszczone, ponowna instalacja i ponowne dołączenie o takiej samej nazwie jest wyświetlany jako innego rekordu o takiej samej nazwie urządzenia.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Pyt.: Rejestracja urządzenia systemu Windows 10 w usłudze Azure AD obsługuje moduły TPM w trybie FIPS?

**Odp.:** Nie, obecnie rejestracji urządzeń w systemie Windows 10 dla wszystkich urządzeń — dołączenie do hybrydowej usługi Azure AD, dołączania do usługi Azure AD i usługi Azure AD zarejestrowana — nie obsługuje moduły TPM w trybie FIPS. Aby pomyślnie przyłączyć się lub Zarejestruj się w usłudze Azure AD, musi zostać wyłączone dla modułów TPM na tych urządzeniach trybie FIPS

---

**Pyt.: Dlaczego może użytkownik nadal dostęp do zasobów z urządzenia, wyłączenia w witrynie Azure portal?**

**Odp.:** Zajmuje się do godziny odwołania mają być stosowane.

>[!NOTE] 
>Dla zarejestrowanych urządzeń firma Microsoft zaleca wyczyszczeniu urządzenia, aby upewnić się, że użytkownicy nie mogą uzyskiwać dostęp do zasobów. Aby uzyskać więcej informacji, zobacz [co to jest rejestrowanie urządzenia?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Funkcja Azure AD join — często zadawane pytania

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Pyt.: Jak I odłączenia urządzenia przyłączone do usługi Azure AD lokalnie na urządzeniu?

**Odp.:** 
- Do hybrydowej usługi Azure AD dołączonym do urządzenia, upewnij się wyłączyć funkcję automatycznej rejestracji. Następnie zaplanowane zadanie nie ponownie zarejestrować urządzenie. Następnie otwórz wiersz polecenia jako administrator i wprowadź `dsregcmd.exe /debug /leave`. Lub uruchom to polecenie jako skrypt przez kilka urządzeń odłączyć zbiorczo.
- Dla czystych usługi Azure AD dołączonym do urządzenia, upewnij się, masz konto administratora lokalnego w trybie offline lub utworzyć. Nie możesz zalogować się przy użyciu dowolnej poświadczeń użytkownika usługi Azure AD. Następnie przejdź do **ustawienia** > **kont** > **dostęp do zasobów służbowych**. Wybierz swoje konto i wybierz **rozłączenia**. Postępuj zgodnie z monitami i podaj poświadczenia administratora lokalnego po wyświetleniu monitu. Ponowne uruchomienie urządzenia, aby zakończyć proces odłączania.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Pyt.: Moi użytkownicy zalogować się do urządzeń przyłączonych do usługi Azure AD, które usunięte lub wyłączone w usłudze Azure AD?

**Odp.:** Tak. Windows ma pamięci podręcznej nazwy użytkownika i hasło możliwości, który umożliwia użytkownikom, którzy wcześniej zalogowano się do pulpitu szybko nawet bez połączenia z siecią. 

Gdy urządzenie jest usunięte lub wyłączone w usłudze Azure AD, nie jest znany dla urządzeń Windows. Dlatego użytkownicy, którzy zalogowano się wcześniej jest nadal dostęp do pulpitu z pamięci podręcznej nazwy użytkownika i hasła. Ale jak urządzenie ma usunięte lub wyłączone, użytkownicy nie mogą korzystać wszystkie zasoby chronione przez dostęp warunkowy oparty na urządzeniu. 

Użytkownicy, którzy nie został wcześniej Zaloguj się w nie może uzyskać dostęp do urządzenia. Istnieje nie pamięci podręcznej nazwy użytkownika i hasło, które włączono dla nich. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Pyt.: Można wyłączone lub usunięte użytkownika logowania się na urządzeniach przyłączonych do usługi Azure AD

**Odp.:** Tak, ale tylko przez ograniczony czas. Gdy użytkownik jest usunięte lub wyłączone w usłudze Azure AD, nie od razu wiadomo na urządzeniu Windows. Dlatego użytkowników, którzy wcześniej podpisany w można uzyskać dostęp do pulpitu z pamięci podręcznej nazwy użytkownika i hasła. 

Zazwyczaj urządzenia jest świadome danych stanu użytkownika w mniej niż cztery godziny. Następnie Windows blokuje dostęp do tych użytkowników, na pulpicie. Użytkownik jest usunięte lub wyłączone w usłudze Azure AD, wszystkie tokeny są odwoływane. Nie można ich więc dostęp do wszystkich zasobów. 

Usunięte lub wyłączone użytkowników, którzy nie został wcześniej Zaloguj się w nie może uzyskać dostęp do urządzenia. Istnieje nie pamięci podręcznej nazwy użytkownika i hasło, które włączono dla nich. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Pyt.: Dlaczego moja użytkownicy mają problemy na urządzeniach przyłączonych do usługi Azure AD po zmianie ich nazwy UPN?

**Odp.:** Zmiany nazwy UPN nie są obecnie w pełni obsługiwane na urządzeniach przyłączonych do usługi Azure AD. Dlatego ich uwierzytelniania przy użyciu usługi Azure AD nie powiedzie się po zmianie ich nazwy UPN. W rezultacie użytkownicy mają logowania jednokrotnego i dostępu warunkowego problemy na swoich urządzeniach. W tej chwili użytkownicy muszą logować się do Windows za pośrednictwem kafelka "Innego użytkownika", przy użyciu nowych nazw UPN, aby rozwiązać ten problem. Obecnie pracujemy nad tego problemu. Jednak użytkownikom logowanie się przy użyciu Windows Hello dla firm nie stoją w obliczu ten problem. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Pyt.: Moje użytkownicy nie może wyszukać drukarki z urządzeniami dołączonymi do usługi Azure AD. Jak włączyć drukowanie z tych urządzeń?

**Odp.:** Aby wdrożyć drukarki dla usługi Azure AD urządzenia przyłączone do, zobacz [wdrażania systemu Windows dla serwera hybrydowego Cloud Print przy użyciu wstępnego uwierzytelniania](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Należy na lokalnym serwerze systemu Windows do wdrożenia drukowania chmury hybrydowej. Obecnie usługa oparta na chmurze usługi drukowania nie jest dostępna. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Pyt.: Jak połączyć do zdalnej usługi Azure AD dołączonym do urządzenia?

**Odp.:** Zobacz [nawiązywanie połączenia z Komputerem zdalnym przyłączonych do usługi Azure Active Directory](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Pyt.: Dlaczego Moi użytkownicy widzą *nie możesz tam przejść stąd*?

**Odp.:** Czy skonfigurować niektóre zasady dostępu warunkowego będą musieli stan określonego urządzenia? Jeśli urządzenie nie spełnia kryteria, użytkownikom zostanie zablokowany, a zobaczą ten komunikat. Oceń zasady dostępu warunkowego. Upewnij się, że urządzenie spełnia kryteria, aby uniknąć wiadomości.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Pyt.: Dlaczego nie niektóre z moich użytkowników uzyskasz monitów usługi Azure Multi-Factor Authentication na urządzeniach przyłączonych do usługi Azure AD?

**Odp.:** Użytkownik może dołączyć lub zarejestrować urządzenia z usługą Azure AD przy użyciu usługi Multi-Factor Authentication. Samo urządzenie staje się drugi składnik zaufany dla tego użytkownika. Zawsze, gdy ten sam użytkownik loguje się do urządzenia i uzyskuje dostęp do aplikacji, usługi Azure AD uwzględnia urządzenia jako drugiego składnika. Umożliwia użytkownikowi uzyskiwanie dostępu do aplikacji bez dodatkowych monitów uwierzytelniania Multi-Factor Authentication. 

To zachowanie:

- Jest dołączony mające zastosowanie do usługi Azure AD i urządzeń — zarejestrowanych w usłudze Azure AD, ale nie dla hybrydowych urządzeń do usługi Azure AD.
- Nie ma zastosowania do żadnego innego użytkownika, który loguje się do tego urządzenia. Dlatego wszystkich innych użytkowników, którzy uzyskują dostęp tego urządzenia Uzyskaj wezwanie do uwierzytelnienia Multi-Factor Authentication. Następnie one dostęp do aplikacji, które wymagają uwierzytelniania wieloskładnikowego.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Pyt.: Dlaczego warto uzyskać *nazwy użytkownika lub hasło jest niepoprawne* komunikatu dla urządzenia, czy mogę po prostu przyłączone do usługi Azure AD?

**Odp.:** W tym scenariuszu typowe przyczyny są następujące:

- Poświadczenia użytkownika nie są już prawidłowe.
- Komputer nie może komunikować się z usługą Azure Active Directory. Sprawdź, czy wszystkie problemy z połączeniem sieciowym.
- Logowania federacyjnego wymagają serwerze federacyjnym w celu obsługi punktów końcowych usługi WS-Trust, które są włączone i dostępne. 
- Włączono uwierzytelnianie przekazywane. Dlatego tymczasowe hasło musi zostać zmienione podczas logowania.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Pyt.: Dlaczego widzę *Niestety... Wystąpił błąd!* okno dialogowe, gdy próbuję z usługą Azure AD join komputera?

**Odp.:** Ten błąd występuje podczas konfigurowania usługi Azure Active Directory rejestracji w usłudze Intune. Upewnij się, że użytkownik, który próbuje dołączania do usługi Azure AD ma przypisaną odpowiednią licencję usługi Intune. Aby uzyskać więcej informacji, zobacz [konfigurowania rejestracji dla urządzeń Windows](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Pyt.: Dlaczego próba usługi Azure AD join niepowodzeń komputera, mimo że nie mogę uzyskać informacje o błędzie?

**Odp.:** Prawdopodobną przyczyną jest to, że użytkownik zalogowany do urządzenia przy użyciu konta wbudowanego konta administratora lokalnego. Utwórz inne konto lokalne, zanim użyjesz usługi Azure Active Directory join na zakończenie instalacji. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Pytanie: co to są certyfikaty P2P dostęp, MS organizacji w-znajduje się na naszych urządzeń z systemem Windows 10?

**Odp.:** Certyfikaty P2P dostęp, MS organizacji w-są wydawane przez usługę Azure AD do obu przyłączonych do usługi Azure AD i urządzeń przyłączonych do hybrydowej usługi Azure AD. Te certyfikaty są używane, aby ustanowić zaufanie między urządzeniami w ramach tej samej dzierżawy dla scenariuszy usług pulpitu zdalnego. Jeden certyfikat jest wystawiony dla urządzenia, a inny wystawionego dla użytkownika. Certyfikat urządzenie znajduje się w `Local Computer\Personal\Certificates` i jest ważny przez jeden dzień. Ten certyfikat zostanie odnowiony (przez wystawienie nowego certyfikatu) Jeśli urządzenie jest nadal aktywne w usłudze Azure AD. Certyfikat użytkownika znajduje się w `Current User\Personal\Certificates` ten certyfikat również jest ważny przez jeden dzień, ale jest wystawiony na żądanie, gdy użytkownik próbuje sesji usług pulpitu zdalnego na innym urządzeniu dołączonym do usługi Azure AD. Nie zostanie odnowiony po upływie. Oba te certyfikaty są wystawiane przy użyciu certyfikatu P2P dostęp, MS organizacji w-obecne w `Local Computer\AAD Token Issuer\Certificates`. Certyfikat został wystawiony przez usługę Azure AD podczas rejestracji urządzenia. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why wielu wygasłe certyfikaty wydane przez MS-organizacji-P2P-dostęp na urządzeniach z naszego systemu Windows 10 są widoczne? Jak można je usunąć?

**Odp.:** Wystąpił problem podczas identyfikowane w systemie Windows 10 w wersji 1709 i niższe, gdzie wygasłe certyfikaty MS-organizacji-P2P-dostęp, nadal istnieją w magazynie komputera z powodu problemów kryptograficznych. Użytkownikom można stoją w obliczu problemów z łącznością sieciową, jeśli używasz wszystkich klientów sieci VPN (na przykład, Cisco AnyConnect), którzy nie może obsługiwać dużą liczbę wygasłe certyfikaty. Ten problem został rozwiązany w wersji 10 1803 systemu Windows, aby automatycznie usuwać takie wygasłe certyfikaty MS-organizacji-P2P — dostęp. Aby rozwiązać ten problem, należy zaktualizować urządzenia do systemu Windows 10 w wersji 1803. Jeśli nie można zaktualizować, możesz usunąć te certyfikaty, bez żadnych negatywnych skutków.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Przyłączanie do hybrydowej usługi Azure AD — często zadawane pytania

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Pyt.: Gdzie mogę znaleźć, rozwiązywanie problemów z informacje do diagnozowania niepowodzeń przyłączanie do hybrydowej usługi Azure AD?

**Odp.:** Aby uzyskać informacje dotyczące rozwiązywania problemów, zobacz następujące artykuły:

- [Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory urządzenia z systemem Windows 10 i Windows Server 2016 przyłączone do](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory urządzenia niskiego poziomu przyłączone do](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Pyt.: Dlaczego są wyświetlane zduplikowane usługi Azure AD zarejestrowanej rekord dla mojego systemu Windows 10 hybrydowej usługi Azure AD urządzenia połączonego z listy urządzeń usługi Azure AD?

**Odp.:** Gdy użytkownicy dodać swoje konta do aplikacji na urządzeniu przyłączonym do domeny, ich może zostać wyświetlony monit o **dodać konto do Windows?** Jeśli użytkownik podał **tak** w wierszu polecenia, rejestruje urządzenie, za pomocą usługi Azure AD. Typ zaufania jest oznaczona jako zarejestrowana z usługi Azure AD. Po włączeniu dołączenie do hybrydowej usługi Azure AD w organizacji, urządzenie pobiera również hybrydowe przyłączone do usługi Azure AD. Następnie dwa stany urządzeń widoczna dla tego samego urządzenia. 

Dołączenie do hybrydowej usługi Azure AD mają pierwszeństwo przed stan usługi Azure AD zarejestrowany. Dlatego Twoje urządzenie jest uznawane za hybrydowe do uwierzytelniania i oceny dostępu warunkowego usługi Azure AD. Możesz bezpiecznie usunąć rekord urządzenia zarejestrowane z usługi Azure AD z portalu usługi Azure AD. Dowiedz się, jak [unikać lub wyczyścić ten stan podwójną na komputerze z systemem Windows 10](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Pyt.: Dlaczego Moi użytkownicy mają problemy z urządzeniach z systemem Windows 10 hybrydowego przyłączony Azure AD po zmianie ich nazwy UPN?

**Odp.:** Zmiany nazwy UPN nie są obecnie w pełni obsługiwane z urządzeniami dołączonymi do usługi Azure AD hybrydowych. Gdy użytkownicy mogą zalogować się do urządzenia i uzyskiwać dostęp do swoich aplikacji w środowisku lokalnym, uwierzytelniania za pomocą usługi Azure AD nie powiedzie się po zmianie nazwy UPN. W rezultacie użytkownicy mają logowania jednokrotnego i dostępu warunkowego problemy na swoich urządzeniach. W tej chwili należy odłączyć urządzenie z usługi Azure AD (Uruchom "dsregcmd /leave" z podniesionymi uprawnieniami) i dołączyć go (wykonywane automatycznie) Aby rozwiązać ten problem. Obecnie pracujemy nad tego problemu. Jednak użytkownikom logowanie się przy użyciu Windows Hello dla firm nie stoją w obliczu ten problem. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Pyt.: Czy urządzeń przyłączonych do usługi Azure AD hybrydowego systemu Windows 10 będą wymagały linii wzroku do kontrolera domeny, aby uzyskać dostęp do zasobów w chmurze?

**Odp.:** Nie, z wyjątkiem sytuacji, gdy zostanie zmienione hasło użytkownika. Po zakończeniu sprzężenia Azure AD hybrydowego systemu Windows 10, a użytkownik zalogował się w co najmniej raz, urządzenie nie wymaga linii wzroku do kontrolera domeny, dostęp do zasobów w chmurze. Windows 10 można uzyskać logowanie jednokrotne do aplikacji usługi Azure AD z dowolnego miejsca przy użyciu połączenia internetowego, z wyjątkiem sytuacji, gdy zostanie zmienione hasło. Użytkownicy, którzy logują się przy użyciu Windows Hello dla firm w dalszym ciągu uzyskać pojedynczego logowania jednokrotnego do aplikacji usługi Azure AD nawet w przypadku, po zmianie hasła, nawet jeśli nie mają linii wzroku do ich kontrolera domeny. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Pyt.: Co się stanie, jeśli użytkownik nie zmieni hasła i spróbuje zalogować się do swojego systemu Windows 10 hybrydowej usługi Azure AD przyłączone urządzenie poza siecią firmową?

**Odp.:** Jeśli hasło zostało zmienione poza siecią firmową (na przykład przy użyciu usługi Azure AD SSPR), następnie zaloguj się nowe hasło użytkownika zakończy się niepowodzeniem. W przypadku urządzeń przyłączonych do usługi Azure AD hybrydowe w lokalnej usłudze Active Directory jest podstawowej urzędu. Urządzenie nie ma bezpośredni kontakt z kontrolerem domeny, to nie można zweryfikować nowe hasło. Tak, użytkownik musi nawiązać połączenie z kontrolerem domeny (za pośrednictwem sieci VPN lub w sieci firmowej) przed mogą logować się do urządzenia przy użyciu nowego hasła. W przeciwnym razie tylko zalogować się przy użyciu starego hasła ze względu na pamięci podręcznej logowania funkcję w wersji Windows. Jednak stare hasło zostaje unieważniony przez usługę Azure AD podczas żądania tokenu i dlatego uniemożliwia logowanie jednokrotne i kończy się niepowodzeniem, wszystkie zasady dostępu warunkowego opartego na urządzeniach. Ten problem nie występuje, jeśli używasz Windows Hello dla firm. 

---

## <a name="azure-ad-register-faq"></a>Usługa Azure AD rejestru — często zadawane pytania

### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Pyt.: Czy mogę zarejestrować urządzenia BYOD systemu Android lub iOS

**Odp.:** Tak, ale tylko przy użyciu usługi rejestracji urządzeń na platformie Azure oraz dla klientów hybrydowych. Nie jest obsługiwana przy użyciu usługi rejestracji urządzeń lokalnych w Active Directory Federation Services (AD FS).

### <a name="q-how-can-i-register-a-macos-device"></a>Pyt.: Jak mogę zarejestrować urządzenia z systemem macOS

**Odp.:** Wykonaj następujące kroki:

1.  [Tworzenie zasad zgodności](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [Definiowanie zasad dostępu warunkowego dla urządzeń z systemem macOS](../active-directory-conditional-access-azure-portal.md) 

**Uwagi:**

- Użytkownicy, zawarte w Twoich potrzeb zasad dostępu warunkowego [obsługiwana wersja pakietu Office dla systemu macOS](../conditional-access/technical-reference.md#client-apps-condition) uzyskują dostęp do zasobów. 
- Podczas pierwszej próby dostępu do usługi Użytkownicy są monitowani o zarejestrowanie urządzenia przy użyciu portalu firmy.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [urządzeń zarejestrowanych w usłudze Azure AD](concept-azure-ad-register.md)
- Dowiedz się więcej o [urządzeń przyłączonych do usługi Azure AD](concept-azure-ad-join.md)
- Dowiedz się więcej o [urządzeń przyłączonych do hybrydowej usługi Azure AD](concept-azure-ad-join-hybrid.md)
