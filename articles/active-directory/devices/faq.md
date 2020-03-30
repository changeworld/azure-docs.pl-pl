---
title: Zarządzania urządzeniami usługi Azure Active Directory – często zadawane pytania | Dokumenty firmy Microsoft
description: Często zadawane pytania dotyczące zarządzania urządzeniami usługi Azure Active Directory.
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
ms.openlocfilehash: c238600d412e53ad665214492e292aa395655b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497523"
---
# <a name="azure-active-directory-device-management-faq"></a>Zarządzanie urządzeniami usługi Azure Active Directory – często zadawane pytania

## <a name="general-faq"></a>Ogólne często zadawane pytania

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>P: Niedawno zarejestrowałem urządzenie. Dlaczego nie widzę urządzenia pod informacjami o użytkowniku w witrynie Azure portal? Lub dlaczego właściciel urządzenia jest oznaczony jako nie dotyczy dla urządzeń hybrydowych usługi Azure Active Directory (Azure AD)?

**Odp.:** Urządzenia z systemem Windows 10, które są przyłączone do usługi Azure AD hybrydowe, nie są wyświetlane w obszarze **Urządzenia UŻYTKOWNIKA.**
Użyj widoku **Wszystkie urządzenia** w witrynie Azure portal. Można również użyć polecenia cmdlet [get-msoldevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) programu PowerShell.

W obszarze **Urządzenia UŻYTKOWNIKA**są wyświetlane tylko następujące urządzenia:

- Wszystkie urządzenia osobiste, które nie są połączone z hybrydową usługą Azure AD. 
- Wszystkie urządzenia z systemem windows 10 lub Windows Server 2016.
- Wszystkie urządzenia inne niż Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Pyt.: Skąd mam wiedzieć, jaki jest stan rejestracji urządzenia klienta?

**Odp.:** W witrynie Azure portal przejdź do **witryny Wszystkie urządzenia**. Wyszukaj urządzenie przy użyciu identyfikatora urządzenia. Sprawdź wartość w kolumnie typu sprzężenia. Czasami urządzenie może zostać zresetowane lub ponownie zaimaged. Dlatego ważne jest również, aby sprawdzić stan rejestracji urządzenia na urządzeniu:

- W przypadku urządzeń z systemem Windows 10 i `dsregcmd.exe /status`Windows Server 2016 lub nowszym uruchom program .
- W przypadku wersji systemu operacyjnego na poziomie down, uruchom . `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`

**Odp.:** Aby uzyskać informacje dotyczące rozwiązywania problemów, zobacz następujące artykuły:
- [Rozwiązywanie problemów z urządzeniami za pomocą polecenia dsregcmd](troubleshoot-device-dsregcmd.md)
- [Rozwiązywanie problemów z hybrydowymi urządzeniami usługi Azure Active Directory przyłączonych do urządzeń z systemem Windows 10 i Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z hybrydowymi urządzeniami usługi Azure Active Directory przyłączanych do urządzeń poziomu sieciowego](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>P: Widzę rekord urządzenia w obszarze informacje o użytkowniku w witrynie Azure portal. I widzę stan jako zarejestrowany na urządzeniu. Czy mogę poprawnie skonfigurować dostęp warunkowy?

**Odp.:** Stan sprzężenia urządzenia, wyświetlany przez **deviceID,** musi być zgodny ze stanem w usłudze Azure AD i spełniać wszystkie kryteria oceny dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Wymaganie zarządzanych urządzeń do dostępu do aplikacji w chmurze za pomocą dostępu warunkowego](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>Pyt.: Dlaczego moi użytkownicy widzą komunikat o błędzie "Twoja organizacja usunęła urządzenie" lub "Twoja organizacja wyłączyła urządzenie" na urządzeniach z systemem Windows 10?

**Odp.:** Na urządzeniach z systemem Windows 10, które zostały połączone lub zarejestrowane w usłudze Azure AD, użytkownicy są wystawiani [podstawowy token odświeżania (PRT),](concept-primary-refresh-token.md) który umożliwia logowanie jednokrotne na. Ważność PRT opiera się na ważności samego urządzenia. Użytkownicy widzą ten komunikat, jeśli urządzenie jest usunięte lub wyłączone w usłudze Azure AD bez inicjowania akcji z samego urządzenia. Urządzenie można usunąć lub wyłączyć w usłudze Azure AD jeden z następujących scenariuszy: 

- Użytkownik wyłącza urządzenie z portalu Moje aplikacje. 
- Administrator (lub użytkownik) usuwa lub wyłącza urządzenie w witrynie Azure portal lub za pomocą programu PowerShell
- Tylko przyłączono do usługi Azure Hybrid AD: administrator usuwa zakres organizacji organizacyjnej urządzeń poza zakresem synchronizacji, co powoduje usunięcie urządzeń z usługi Azure AD
- Uaktualnianie usługi Azure AD połączyć się w wersji 1.4.xx.x. [Opis usługi Azure AD Connect 1.4.xx.x i zniknięcia urządzenia](/azure/active-directory/hybrid/reference-connect-device-disappearance).


Poniżej znajduje się sposób ich poprawiania.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>P: Wyłączono lub usunięto urządzenie w witrynie Azure portal lub przy użyciu programu Windows PowerShell. Ale stan lokalny na urządzeniu mówi, że nadal jest zarejestrowany. Co mam zrobić?

**Odp.:** Ta operacja jest zgodnie z projektem. W takim przypadku urządzenie nie ma dostępu do zasobów w chmurze. Administratorzy mogą wykonać tę akcję w przypadku przestarzałych, utraconych lub skradzionych urządzeń, aby zapobiec nieautoryzowanemu dostępowi. Jeśli ta akcja została wykonana nieumyślnie, musisz ponownie włączyć lub ponownie zarejestrować urządzenie w sposób opisany poniżej

- Jeśli urządzenie zostało wyłączone w usłudze Azure AD, administrator z wystarczającymi uprawnieniami może włączyć je z portalu usługi Azure AD  
  > [!NOTE]
  > Jeśli synchronizujesz urządzenia przy użyciu usługi Azure AD Connect, hybrydowe urządzenia przyłączone do usługi Azure AD zostaną automatycznie ponownie włączone podczas następnego cyklu synchronizacji. Dlatego jeśli musisz wyłączyć hybrydowe urządzenie przyłączone do usługi Azure AD, musisz wyłączyć je z lokalnej usługi AD

 - Jeśli urządzenie zostanie usunięte w usłudze Azure AD, należy ponownie zarejestrować urządzenie. Aby ponownie się zarejestrować, należy podjąć ręczną akcję na urządzeniu. Poniżej znajdują się instrukcje dotyczące ponownej rejestracji na podstawie stanu urządzenia. 

      Aby ponownie zarejestrować hybrydowe urządzenia usługi Azure AD przyłączone do systemów Windows 10 i Windows Server 2016/2019, należy wykonać następujące kroki:

      1. Otwórz wiersz polecenia jako administrator.
      1. Wprowadź polecenie `dsregcmd.exe /debug /leave`.
      1. Wyloguj się i zaloguj, aby wyzwolić zaplanowane zadanie, które ponownie rejestruje urządzenie za pomocą usługi Azure AD. 

      W przypadku wersji systemu operacyjnego Windows o poziomie w dół, które są połączone z hybrydową usługą Azure AD, należy wykonać następujące kroki:

      1. Otwórz wiersz polecenia jako administrator.
      1. Wprowadź polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Wprowadź polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      W przypadku urządzeń przyłączonych do usługi Azure AD urządzeń z systemem Windows 10 należy wykonać następujące kroki:

      1. Otwieranie wiersza polecenia jako administrator
      1. Enter `dsregcmd /forcerecovery` (Uwaga: Aby wykonać tę akcję, musisz być administratorem).
      1. Kliknij przycisk "Zaloguj się" w oknie dialogowym, które otwiera się i kontynuuj proces logowania.
      1. Wyloguj się i zaloguj się z powrotem do urządzenia, aby zakończyć odzyskiwanie.

      W przypadku urządzeń z systemem Windows 10 zarejestrowanych w usłudze Azure AD należy wykonać następujące czynności:

      1. Przejdź do **pozycji Ustawienia** > **konta** > **dostęp do pracy lub szkoły**. 
      1. Wybierz konto i wybierz pozycję **Rozłącz**.
      1. Kliknij na "+ Connect" i zarejestruj urządzenie ponownie, przechodząc przez proces logowania.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Pyt.: Dlaczego w witrynie Azure portal są widoczne zduplikowane wpisy urządzeń?

**A:**

- W systemach Windows 10 i Windows Server 2016 powtarzające się próby odłączenia i ponownego dołączenia do tego samego urządzenia mogą spowodować zduplikowane wpisy. 
- Każdy użytkownik systemu Windows, który używa **konta Add Work lub School,** tworzy nowy rekord urządzenia o tej samej nazwie urządzenia.
- W przypadku wersji systemu operacyjnego Windows o poziomie w dół, które są przyłączone do lokalnej domeny usługi Azure Directory, automatyczna rejestracja tworzy nowy rekord urządzenia o tej samej nazwie urządzenia dla każdego użytkownika domeny, który loguje się do urządzenia. 
- Komputer przyłączony do usługi Azure AD, który jest czyścił, ponownie instalował i dołączał ponownie o tej samej nazwie, jest wyświetlany jako inny rekord o tej samej nazwie urządzenia.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Pyt.: Czy rejestracja urządzeń systemu Windows 10 w usłudze Azure AD obsługuje maszyny TPM w trybie FIPS?

**Odp.:** Rejestracja urządzeń z systemem Windows 10 jest obsługiwana tylko dla modułu TPM 2.0 zgodnego ze standardem FIPS i nie jest obsługiwana dla modułu TPM 1.2. Jeśli twoje urządzenia mają zgodny z FIPS moduł TPM 1.2, należy je wyłączyć przed przystąpieniem do usługi Azure AD lub hybrydowym sprzężeniem usługi Azure AD. Firma Microsoft nie udostępnia żadnych narzędzi do wyłączania trybu FIPS dla modułów TPM, ponieważ jest on zależny od producenta modułu TPM. Aby uzyskać pomoc techniczną, skontaktuj się ze sprzętowym oem. 

---

**Pyt.: Dlaczego użytkownik nadal dostęp do zasobów z urządzenia, które zostały wyłączone w witrynie Azure Portal?**

**Odp.:** Trwa do godziny odwołania mają być stosowane od momentu, gdy urządzenie usługi Azure AD jest oznaczony jako wyłączone.

>[!NOTE] 
>W przypadku zarejestrowanych urządzeń zaleca się wyczyszczenie urządzenia, aby upewnić się, że użytkownicy nie mogą uzyskać dostępu do zasobów. Aby uzyskać więcej informacji, zobacz [Co to jest rejestracja urządzenia?](/mem/intune/user-help/use-managed-devices-to-get-work-done). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>Pyt.: Dlaczego istnieją urządzenia oznaczone jako "Oczekujące" w kolumnie REGISTERED w witrynie Azure portal?

**A**: Oczekujące wskazuje, że urządzenie nie jest zarejestrowane. Ten stan wskazuje, że urządzenie zostało zsynchronizowane przy użyciu usługi Azure AD połączyć z lokalnej usługi AD i jest gotowy do rejestracji urządzenia. Te urządzenia mają typ join ustawiony na "Hybrid Azure AD joined". Dowiedz się więcej [o planowaniu implementacji hybrydowego dołączania do usługi Azure Active Directory.](hybrid-azuread-join-plan.md)

>[!NOTE]
>Urządzenie może również zmienić stan zarejestrowany na "Oczekujące"
>* Jeśli urządzenie zostanie najpierw usunięte z usługi Azure AD i ponownie zsynchronizowane z lokalnej usługi AD.
>* Jeśli urządzenie zostanie usunięte z zakresu synchronizacji w usłudze Azure AD Connect i dodane z powrotem.
>
>W obu przypadkach należy ponownie zarejestrować urządzenie ręcznie na każdym z tych urządzeń. Aby sprawdą, czy urządzenie było wcześniej zarejestrowane, można [rozwiązywać problemy z urządzeniami za pomocą polecenia dsregcmd](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Często zadawane pytania dotyczące dołączania do usługi Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Pyt.: Jak odłączyć urządzenie przyłączone do usługi Azure AD lokalnie na urządzeniu?

**Odp.:** W przypadku urządzeń przyłączonych do usługi Azure AD upewnij się, że masz konto administratora lokalnego w trybie offline lub utwórz je. Nie można zalogować się przy użyciu żadnych poświadczeń użytkownika usługi Azure AD. Następnie przejdź do **pozycji Ustawienia** > **konta** > **dostęp do pracy lub szkoły**. Wybierz swoje konto i wybierz pozycję **Rozłącz**. Postępuj zgodnie z instrukcjami i podasz poświadczenie administratora lokalnego po wyświetleniu monitu. Uruchom ponownie urządzenie, aby zakończyć proces odłączenia.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Pyt.: Czy moi użytkownicy mogą logować się do urządzeń przyłączonych do usługi Azure AD, które są usuwane lub wyłączone w usłudze Azure AD?

**Odpowiedź:** tak. System Windows ma buforowaną nazwę użytkownika i hasło, które umożliwia użytkownikom, którzy logowali się wcześniej, szybki dostęp do pulpitu, nawet bez łączności sieciowej. 

Gdy urządzenie zostanie usunięte lub wyłączone w usłudze Azure AD, nie jest znane urządzeniu z systemem Windows. Dlatego użytkownicy, którzy zalogowali się wcześniej, nadal uzyskują dostęp do pulpitu za pomocą pamięci podręcznej nazwy użytkownika i hasła. Ponieważ urządzenie jest usuwane lub wyłączone, użytkownicy nie mogą uzyskać dostępu do żadnych zasobów chronionych przez dostęp warunkowy oparty na urządzeniu. 

Użytkownicy, którzy wcześniej się nie logują, nie mogą uzyskać dostępu do urządzenia. Nie ma włączonej pamięci podręcznej nazwy użytkownika i hasła. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>P: Czy wyłączony lub usunięty użytkownik może zalogować się do urządzeń przyłączonych do usługi Azure AD

**Odp.:** Tak, ale tylko przez ograniczony czas. Gdy użytkownik zostanie usunięty lub wyłączony w usłudze Azure AD, nie jest od razu znany urządzeniu z systemem Windows. Dzięki czemu użytkownicy, którzy zalogowali się wcześniej, mogą uzyskać dostęp do pulpitu za pomocą pamięci podręcznej nazwy użytkownika i hasła. 

Zazwyczaj urządzenie jest świadomy stanu użytkownika w mniej niż cztery godziny. Następnie system Windows blokuje dostęp tych użytkowników do pulpitu. Ponieważ użytkownik jest usuwany lub wyłączony w usłudze Azure AD, wszystkie ich tokeny są odwoływane. Nie mają więc dostępu do żadnych zasobów. 

Usunięci lub wyłączeni użytkownicy, którzy wcześniej się nie logują, nie mogą uzyskać dostępu do urządzenia. Nie ma włączonej pamięci podręcznej nazwy użytkownika i hasła. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Pyt.: Dlaczego moi użytkownicy mają problemy na urządzeniach przyłączonych do usługi Azure AD po zmianie ich sieci UPN?

**Odp.:** Obecnie zmiany w sieci UPN nie są w pełni obsługiwane na urządzeniach przyłączonych do usługi Azure AD. Dlatego ich uwierzytelnianie za pomocą usługi Azure AD kończy się niepowodzeniem po ich zmianach w usłudze UPN. W rezultacie użytkownicy mają problemy z przysłowiem sytym i dostępem warunkowym na swoich urządzeniach. W tej chwili użytkownicy muszą zalogować się do systemu Windows za pomocą kafelka "Inny użytkownik" przy użyciu nowej nazwy UPN, aby rozwiązać ten problem. Obecnie pracujemy nad zajęciem się tym problemem. Jednak użytkownicy logujący się za pomocą funkcji Windows Hello dla firm nie napotykają tego problemu. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>P: Moi użytkownicy nie mogą przeszukiwać drukarek z urządzeń przyłączonych do usługi Azure AD. Jak włączyć drukowanie z tych urządzeń?

**Odp.:** Aby wdrożyć drukarki dla urządzeń przyłączonych do usługi Azure AD, zobacz [Wdrażanie usługi Windows Server Hybrid Cloud Print przy użyciu uwierzytelniania wstępnego](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Do wdrożenia wydruku w chmurze hybrydowej potrzebny jest lokalny system Windows Server. Obecnie usługa drukowania w chmurze nie jest dostępna. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Pyt.: Jak połączyć się ze zdalnym urządzeniem przyłączanym do usługi Azure AD?

**Odp.:** Zobacz [Łączenie ze zdalnym komputerem przyłączanym do usługi Azure Active Directory](/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>P: Dlaczego moi użytkownicy *widzą, że nie możesz się stąd dostać?*

**Odp.:** Czy skonfigurowano niektóre reguły dostępu warunkowego, aby wymagały określonego stanu urządzenia? Jeśli urządzenie nie spełnia kryteriów, użytkownicy są blokowani i widzą ten komunikat. Oceń reguły zasad dostępu warunkowego. Upewnij się, że urządzenie spełnia kryteria, aby uniknąć wiadomości.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Pyt.: Dlaczego niektórzy z moich użytkowników nie otrzymują monitów o uwierzytelnianie wieloskładnikowe platformy Azure na urządzeniach przyłączonych do usługi Azure AD?

**Odp.:** Użytkownik może dołączyć lub zarejestrować urządzenie za pomocą usługi Azure AD przy użyciu uwierzytelniania wieloskładnikowego. Następnie samo urządzenie staje się zaufanym drugim czynnikiem dla tego użytkownika. Za każdym razem, gdy ten sam użytkownik loguje się do urządzenia i uzyskuje dostęp do aplikacji, usługa Azure AD uznaje urządzenie za drugi czynnik. Umożliwia to użytkownikowi bezproblemowy dostęp do aplikacji bez dodatkowych monitów uwierzytelniania wieloskładnikowego. 

To zachowanie:

- Ma zastosowanie do urządzeń przyłączonych do usługi Azure AD i usługi Azure AD — ale nie dla urządzeń przyłączonych do usługi Azure AD.
- Nie ma zastosowania do żadnego innego użytkownika, który zaloguje się na tym urządzeniu. Dlatego wszyscy inni użytkownicy, którzy uzyskują dostęp do tego urządzenia, otrzymują wyzwanie uwierzytelniania wieloskładnikowego. Następnie mogą uzyskać dostęp do aplikacji, które wymagają uwierzytelniania wieloskładnikowego.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Pyt.: Dlaczego mogę uzyskać *nazwę użytkownika lub hasło jest niepoprawna* wiadomość dla urządzenia, które właśnie dołączył do usługi Azure AD?

**Odp.:** Typowe przyczyny tego scenariusza są następujące:

- Poświadczenia użytkownika nie są już prawidłowe.
- Komputer nie może komunikować się z usługą Azure Active Directory. Sprawdź, czy nie występują problemy z łącznością sieciową.
- Federacyjne logowania wymagają serwera federacyjnego do obsługi punktów końcowych WS-Trust, które są włączone i dostępne. 
- Włączono uwierzytelnianie przekazywane. Dlatego hasło tymczasowe musi zostać zmienione podczas logowania.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>P: Dlaczego widzę *Ups... wystąpił błąd!* podczas próby dołączenia do usługi Azure AD?

**Odp.:** Ten błąd występuje podczas konfigurowania rejestracji usługi Azure Active Directory w usłudze Intune. Upewnij się, że użytkownik, który próbuje sprzężenia usługi Azure AD ma przypisaną właściwą licencję usługi Intune. Aby uzyskać więcej informacji, zobacz [Konfigurowanie rejestracji dla urządzeń z systemem Windows](/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Pyt.: Dlaczego moja próba dołączenia do usługi Azure AD nie powiodła się, chociaż nie otrzymuję żadnych informacji o błędzie?

**Odp.:** Prawdopodobną przyczyną jest zalogowanie się do urządzenia przy użyciu lokalnego wbudowanego konta administratora. Utwórz inne konto lokalne przed użyciem sprzężenia usługi Azure Active Directory w celu zakończenia instalacji. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>P: Jakie certyfikaty MS-Organization-P2P-Access są obecne na naszych urządzeniach z systemem Windows 10?

**Odp.:** Certyfikaty MS-Organization-P2P-Access są wystawiane przez usługę Azure AD zarówno na urządzeniach przyłączonych do usługi Azure AD, jak i na hybrydowych urządzeniach przyłączonych do usługi Azure AD. Certyfikaty te są używane w celu umożliwienia zaufania między urządzeniami w tej samej dzierżawie dla scenariuszy pulpitu zdalnego. Jeden certyfikat jest wystawiany na urządzenie, a drugi jest wystawiany użytkownikowi. Certyfikat urządzenia jest `Local Computer\Personal\Certificates` obecny i jest ważny przez jeden dzień. Ten certyfikat jest odnawiany (przez wystawienie nowego certyfikatu), jeśli urządzenie jest nadal aktywne w usłudze Azure AD. Certyfikat użytkownika jest `Current User\Personal\Certificates` obecny i ten certyfikat jest również ważny przez jeden dzień, ale jest wystawiany na żądanie, gdy użytkownik próbuje sesji pulpitu zdalnego do innego urządzenia przyłączone do usługi Azure AD. Nie jest odnawiany po wygaśnięciu. Oba te certyfikaty są wystawiane przy użyciu certyfikatu MS-Organization-P2P-Access obecnego w pliku `Local Computer\AAD Token Issuer\Certificates`. Ten certyfikat jest wystawiany przez usługę Azure AD podczas rejestracji urządzenia. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>P: Dlaczego na urządzeniach z systemem Windows 10 jest widoczna wiele wygasłych certyfikatów wystawionych przez ms-organization-P2P-Access? Jak je usunąć?

**Odp.:** Wystąpił problem zidentyfikowany w systemie Windows 10 w wersji 1709 i niższym, w którym wygasłe certyfikaty MS-Organization-P2P-Access nadal występowały w magazynie komputerów z powodu problemów kryptograficznych. Użytkownicy mogą napotkać problemy z łącznością sieciową, jeśli używasz klientów sieci VPN (na przykład Cisco AnyConnect), którzy nie mogą obsłużyć dużej liczby wygasłych certyfikatów. Ten problem został rozwiązany w wersji systemu Windows 10 1803, aby automatycznie usunąć wszystkie takie wygasłe certyfikaty MS-Organization-P2P-Access. Ten problem można rozwiązać, aktualizując urządzenia do systemu Windows 10 1803. Jeśli nie można zaktualizować, można usunąć te certyfikaty bez żadnego negatywnego wpływu.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Hybrid Azure AD join – często zadawane pytania

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>Pyt.: Jak odłączyć hybrydowe urządzenie przyłączone do usługi Azure AD lokalnie na urządzeniu?

**Odp.:** W przypadku hybrydowych urządzeń przyłączonych do usługi Azure AD należy wyłączyć automatyczną rejestrację. Następnie zaplanowane zadanie nie rejestruje urządzenia ponownie. Następnie otwórz wiersz polecenia jako administrator `dsregcmd.exe /debug /leave`i wprowadź . Lub uruchom to polecenie jako skrypt na kilku urządzeniach, aby odłączyć zbiorczo.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Pyt.: Gdzie można znaleźć informacje dotyczące rozwiązywania problemów w celu zdiagnozowania błędów sprzężenia hybrydowego usługi Azure AD?

**Odp.:** Aby uzyskać informacje dotyczące rozwiązywania problemów, zobacz następujące artykuły:

- [Rozwiązywanie problemów z hybrydowymi urządzeniami usługi Azure Active Directory przyłączonych do urządzeń z systemem Windows 10 i Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z hybrydowymi urządzeniami usługi Azure Active Directory przyłączanych do urządzeń poziomu sieciowego](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Pyt.: Dlaczego na liście urządzeń usługi Azure AD jest widoczny zduplikowany rekord zarejestrowany usługi Azure AD dla mojego hybrydowego urządzenia usługi Azure AD systemu Windows 10?

**Odp.:** Gdy użytkownicy dodają swoje konta do aplikacji na urządzeniu przyłączanym do domeny, mogą zostać monitowani o **dodanie konta do systemu Windows?** Jeśli w wierszu polecenia wejdą **tak,** urządzenie zarejestruje się w usłudze Azure AD. Typ zaufania jest oznaczony jako zarejestrowany w usłudze Azure AD. Po włączeniu hybrydowego sprzężenia usługi Azure AD w organizacji urządzenie zostanie również sprzężene z hybrydową usługą Azure AD. Następnie dwa stany urządzenia są wyświetlane dla tego samego urządzenia. 

Hybrydowe sprzężenie usługi Azure AD ma pierwszeństwo przed stanem zarejestrowanym usługi Azure AD. Dzięki temu urządzenie jest uważane za hybrydowe usługi Azure AD przyłączone do dowolnego uwierzytelniania i oceny dostępu warunkowego. Można bezpiecznie usunąć rekord urządzenia zarejestrowanego usługi Azure AD z portalu usługi Azure AD. Dowiedz [się, aby uniknąć lub oczyścić ten podwójny stan na komputerze z systemem Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Pyt.: Dlaczego moi użytkownicy mają problemy z systemem Windows 10 hybrydowych urządzeń platformy Azure AD dołączył po zmianie ich upn?

**Odp.:** Obecnie zmiany w upn nie są w pełni obsługiwane z hybrydowych urządzeń platformy Azure AD przyłączone. Podczas gdy użytkownicy mogą logować się do urządzenia i uzyskiwać dostęp do swoich aplikacji lokalnych, uwierzytelnianie za pomocą usługi Azure AD kończy się niepowodzeniem po zmianie sieci UPN. W rezultacie użytkownicy mają problemy z przysłowiem sytym i dostępem warunkowym na swoich urządzeniach. W tej chwili należy odłączyć urządzenie z usługi Azure AD (uruchom "dsregcmd /leave" z podwyższonymi uprawnieniami) i ponownie dołączyć (dzieje się automatycznie), aby rozwiązać problem. Obecnie pracujemy nad zajęciem się tym problemem. Jednak użytkownicy logujący się za pomocą funkcji Windows Hello dla firm nie napotykają tego problemu. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Pyt.: Czy hybrydowe urządzenia platformy Azure AD systemu Windows 10 wymagają linii wzroku do kontrolera domeny, aby uzyskać dostęp do zasobów w chmurze?

**Odp.:** Nie, z wyjątkiem sytuacji, gdy hasło użytkownika zostanie zmienione. Po zakończeniu hybrydowego sprzężenia usługi Azure AD systemu Windows 10 i zalogowaniu się użytkownika co najmniej raz urządzenie nie wymaga linii wzroku do kontrolera domeny, aby uzyskać dostęp do zasobów w chmurze. System Windows 10 może uzyskać logowanie jednokrotne do aplikacji usługi Azure AD z dowolnego miejsca z połączeniem internetowym, z wyjątkiem sytuacji zmiany hasła. Użytkownicy, którzy logują się za pomocą funkcji Windows Hello dla firm, nadal otrzymują logowanie jednokrotne do aplikacji usługi Azure AD nawet po zmianie hasła, nawet jeśli nie mają linii wzroku do kontrolera domeny. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Pyt.: Co się stanie, jeśli użytkownik zmieni swoje hasło i spróbuje zalogować się do hybrydowego urządzenia usługi Azure AD systemu Windows 10 poza siecią firmową?

**Odp.:** Jeśli hasło zostanie zmienione poza siecią firmową (na przykład przy użyciu umowy SSPR usługi Azure AD), użytkownik zaloguje się przy użyciu nowego hasła zakończy się niepowodzeniem. W przypadku hybrydowych urządzeń przyłączonych do usługi Azure AD lokalna usługa Active Directory jest podstawowym urzędem. Jeśli urządzenie nie ma linii wzroku do kontrolera domeny, nie może zweryfikować nowego hasła. Tak więc użytkownik musi nawiązać połączenie z kontrolerem domeny (za pośrednictwem sieci VPN lub będąc w sieci firmowej), zanim będzie mógł zalogować się do urządzenia za pomocą nowego hasła. W przeciwnym razie mogą zalogować się tylko przy za pomocą starego hasła z powodu możliwości logowania w pamięci podręcznej w systemie Windows. Jednak stare hasło jest unieważnione przez usługę Azure AD podczas żądań tokenu i w związku z tym zapobiega logowaniu jednokrotnemu i nie spełnia żadnych zasad dostępu warunkowego opartego na urządzeniu. Ten problem nie występuje, jeśli używasz Windows Hello dla firm. 

---

## <a name="azure-ad-register-faq"></a>Często zadawane pytania dotyczące rejestru usługi Azure AD

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>Pyt.: Jak usunąć stan zarejestrowany usługi Azure AD dla urządzenia lokalnie?

**A:** 
- W przypadku urządzeń zarejestrowanych w usłudze Azure AD systemu Windows 10 przejdź do**sekcji Konta** >  **ustawień** > **Dostęp do pracy lub szkoły**. Wybierz swoje konto i wybierz pozycję **Rozłącz**. Rejestracja urządzenia jest na profil użytkownika w systemie Windows 10.
- W systemach iOS i Android można użyć aplikacji Microsoft Authenticator **Ustawienia** > **rejestracji urządzeń** i wybrać pozycję **Wyrejestruj urządzenie**.
- W systemie macOS można użyć aplikacji Microsoft Intune Company Portal, aby wyrejestrować urządzenie z zarządzania i usunąć dowolną rejestrację. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>Pyt.: Jak zablokować użytkownikom dodawanie dodatkowych kont służbowych (zarejestrowana w usłudze Azure AD) na moich firmowych urządzeniach z systemem Windows 10?

**Odp.:** Włącz następujący rejestr, aby uniemożliwić użytkownikom dodawanie dodatkowych kont służbowych do domeny firmowej przyłączone, usługi Azure AD przyłączone lub hybrydowe urządzenia z systemem Windows 10 przyłączone do usługi Azure AD. Ta zasada może również służyć do blokowania maszyn przyłączonych do domeny od przypadkowego uzyskania usługi Azure AD zarejestrowane przy użyciu tego samego konta użytkownika. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>P: Czy mogę zarejestrować urządzenia Z systemem Android lub iOS BYOD?

**Odp.:** Tak, ale tylko z usługą rejestracji urządzeń platformy Azure i dla klientów hybrydowych. Usługa rejestracji urządzeń lokalnych w usługach federacyjnych Active Directory (AD FS) nie jest obsługiwana.

---
### <a name="q-how-can-i-register-a-macos-device"></a>P: Jak mogę zarejestrować urządzenie z systemem macOS?

**Odp.:** Wykonać następujące kroki:

1.    [Tworzenie zasad zgodności](/intune/compliance-policy-create-mac-os)
1.    [Definiowanie zasad dostępu warunkowego dla urządzeń z systemem macOS](../active-directory-conditional-access-azure-portal.md) 

**Uwagi:**

- Użytkownicy uwzględnieni w zasadach dostępu warunkowego potrzebują [obsługiwanej wersji pakietu Office dla systemu macOS,](../conditional-access/concept-conditional-access-conditions.md) aby uzyskać dostęp do zasobów. 
- Podczas pierwszej próby dostępu użytkownicy są monitowani o zarejestrowanie urządzenia przy użyciu portalu firmy.

---
## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [urządzeniach zarejestrowanych w usłudze Azure AD](concept-azure-ad-register.md)
- Dowiedz się więcej o [urządzeniach przyłączonych do usługi Azure AD](concept-azure-ad-join.md)
- Dowiedz się więcej o [hybrydowych urządzeniach przyłączonych do usługi Azure AD](concept-azure-ad-join-hybrid.md)
