---
title: Zarządzanie urządzeniami w usłudze Azure Active Directory — często zadawane pytania | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory Zarządzanie urządzeniami — często zadawane pytania.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: cc0521f1f27ddfc1fc44b9f24212393d11177d70
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660973"
---
# <a name="azure-active-directory-device-management-faq"></a>Zarządzanie urządzeniami w usłudze Azure Active Directory — często zadawane pytania

**Pyt.: Czy ostatnio zarejestrowane urządzenia. Dlaczego nie widzę urządzenia w obszarze Moje informacje o użytkowniku w witrynie Azure portal? Lub dlaczego jest właściciel urządzenia oznaczony jako urządzenia przyłączone do n/d dla hybrydowych usługi Azure Active Directory (Azure AD)?**

**ODP.:** Urządzenia z systemem Windows 10, które są hybrydowe przyłączone do usługi Azure AD nie pojawiają się w **urządzenia użytkowników**.
Użyj **urządzeniom** widok w witrynie Azure portal. Możesz również użyć programu PowerShell [Get MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) polecenia cmdlet.

Następujące urządzenia są wyświetlane w obszarze **urządzenia użytkowników**:

- Przyłączone do wszystkich urządzeń osobistych, które nie są hybrydowej usługi Azure AD. 
- Wszystkie inne niż Windows 10 lub urządzeń z systemem Windows Server 2016.
- Wszystkie urządzenia inne niż Windows. 

--- 

**Pyt.: Jak sprawdzić stanu rejestracji urządzenia klienta jest?**

**ODP.:** W witrynie Azure portal przejdź do **urządzeniom**. Wyszukaj urządzenia za pomocą identyfikatora urządzenia. Sprawdź wartości w kolumnie Typ sprzężenia. Czasami urządzenie może zresetować lub odtworzony z obrazu. Dlatego istotne jest również sprawdzić stanu rejestracji urządzenia na urządzeniu:

- W przypadku systemu Windows 10 i Windows Server 2016 lub nowszym, systemem `dsregcmd.exe /status`.
- W przypadku wcześniejszych wersji systemu operacyjnego, systemem `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

**Pyt.: Czy mogę zobaczyć rekordem urządzenia w obszarze informacje o użytkowniku w witrynie Azure portal. I wyświetlić stan, ponieważ zarejestrowany na urządzeniu. Jestem I prawidłowo skonfigurowany do korzystania z dostępu warunkowego?**

**ODP.:** Stan dołączania urządzenia, wyświetlane według **deviceID**musi odpowiadać stan w usłudze Azure AD i spełniać wszystkie kryteria oceny dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [wymagają zarządzanych urządzeń, aby uzyskać dostęp do aplikacji w chmurze przy użyciu dostępu warunkowego](../conditional-access/require-managed-devices.md).

---

**Pyt.: Po usunięciu urządzenia w witrynie Azure portal lub za pomocą programu Windows PowerShell. Ale stan lokalnego na urządzeniu jest wyświetlany komunikat, że pozostaje on zarejestrowany.**

**ODP.:** Ta operacja jest celowe. Urządzenie nie ma dostępu do zasobów w chmurze. 

Jeśli chcesz ponownie zarejestrować, należy wykonać akcję ręczną na urządzeniu. 

Aby wyczyścić stanu dołączania do, z systemem Windows 10 i Windows Server 2016, które są przyłączone do domeny usługi Active Directory w środowisku lokalnym, wykonaj następujące czynności:

1.  Otwórz wiersz polecenia jako administrator.

2.  Wprowadź polecenie `dsregcmd.exe /debug /leave`.

3.  Wyloguj się i zaloguj się w celu wyzwolenia zaplanowane zadanie, które rejestruje urządzenie ponownie przy użyciu usługi Azure AD. 

W wersjach systemu operacyjnego Windows niższego poziomu, które są przyłączone do domeny usługi Active Directory w środowisku lokalnym wykonaj następujące czynności:

1.  Otwórz wiersz polecenia jako administrator.
2.  Wprowadź polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Wprowadź polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**Pyt.: Dlaczego są wyświetlane urządzenia zduplikowanych wpisów w witrynie Azure portal?**

**ODP.:**

-   Dla systemu Windows 10 i Windows Server 2016 powtarzanych próbuje Odłącz i ponownie dołączyć w tym samym urządzeniu może spowodować zduplikowane wpisy. 

-   Każdy użytkownik Windows **dodać pracy konta firmowego lub szkolnego** tworzy nowy rekord urządzenia o takiej samej nazwie urządzenia.

-   Dla wersji systemu operacyjnego Windows niższego poziomu, które są przyłączone do domeny usługi Azure Directory w środowisku lokalnym automatycznej rejestracji tworzy nowy rekord urządzenia z taką samą nazwę urządzenia dla każdego użytkownika domeny, który loguje się do urządzenia. 

-   Komputerze dołączonym do usługi Azure AD, który wyczyszczone, ponowna instalacja i ponowne dołączenie o takiej samej nazwie jest wyświetlany jako innego rekordu o takiej samej nazwie urządzenia.

---

**Pyt.: Dlaczego może użytkownik nadal dostęp do zasobów z urządzenia, wyłączenia w witrynie Azure portal?**

**ODP.:** Zajmuje się do godziny odwołania mają być stosowane.

>[!NOTE] 
>Dla zarejestrowanych urządzeń firma Microsoft zaleca wyczyszczeniu urządzenia, aby upewnić się, że użytkownicy nie mogą uzyskiwać dostęp do zasobów. Aby uzyskać więcej informacji, zobacz [co to jest rejestrowanie urządzenia?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Funkcja Azure AD join — często zadawane pytania

**Pyt.: Jak I odłączenia urządzenia przyłączone do usługi Azure AD lokalnie na urządzeniu?**

**ODP.:** 
- Do hybrydowej usługi Azure AD dołączonym do urządzenia, upewnij się wyłączyć funkcję automatycznej rejestracji. Następnie zaplanowane zadanie nie ponownie zarejestrować urządzenie. Następnie otwórz wiersz polecenia jako administrator i wprowadź `dsregcmd.exe /debug /leave`. Lub uruchom to polecenie jako skrypt przez kilka urządzeń odłączyć zbiorczo.

- Dla czystych usługi Azure AD dołączonym do urządzenia, upewnij się, masz konto administratora lokalnego w trybie offline lub utworzyć. Nie możesz zalogować się przy użyciu dowolnej poświadczeń użytkownika usługi Azure AD. Następnie przejdź do **ustawienia** > **kont** > **dostęp do zasobów służbowych**. Wybierz swoje konto i wybierz **rozłączenia**. Postępuj zgodnie z monitami i podaj poświadczenia administratora lokalnego po wyświetleniu monitu. Ponowne uruchomienie urządzenia, aby zakończyć proces odłączania.

---

**Pyt.: Moi użytkownicy zalogować się do urządzeń przyłączonych do usługi Azure AD, które usunięte lub wyłączone w usłudze Azure AD?**

**ODP.:** Tak. Windows ma pamięci podręcznej nazwy użytkownika i hasło możliwości, który umożliwia użytkownikom, którzy wcześniej zalogowano się do pulpitu szybko nawet bez połączenia z siecią. 

Gdy urządzenie jest usunięte lub wyłączone w usłudze Azure AD, nie jest znany dla urządzeń Windows. Dlatego użytkownicy, którzy zalogowano się wcześniej jest nadal dostęp do pulpitu z pamięci podręcznej nazwy użytkownika i hasła. Ale jak urządzenie ma usunięte lub wyłączone, użytkownicy nie mogą korzystać wszystkie zasoby chronione przez dostęp warunkowy oparty na urządzeniu. 

Użytkownicy, którzy nie został wcześniej Zaloguj się w nie może uzyskać dostęp do urządzenia. Istnieje nie pamięci podręcznej nazwy użytkownika i hasło, które włączono dla nich. 

---

**Pyt.: Można wyłączonych lub usuniętych użytkowników logowania się na urządzeniach przyłączonych do usługi Azure AD**

**ODP.:** Tak, ale tylko przez ograniczony czas. Gdy użytkownik jest usunięte lub wyłączone w usłudze Azure AD, nie od razu wiadomo na urządzeniu Windows. Dlatego użytkowników, którzy wcześniej podpisany w można uzyskać dostęp do pulpitu z pamięci podręcznej nazwy użytkownika i hasła. 

Zazwyczaj urządzenia jest świadome danych stanu użytkownika w mniej niż cztery godziny. Następnie Windows blokuje dostęp do tych użytkowników, na pulpicie. Użytkownik jest usunięte lub wyłączone w usłudze Azure AD, wszystkie tokeny są odwoływane. Nie można ich więc dostęp do wszystkich zasobów. 

Usunięte lub wyłączone użytkowników, którzy nie został wcześniej Zaloguj się w nie może uzyskać dostęp do urządzenia. Istnieje nie pamięci podręcznej nazwy użytkownika i hasło, które włączono dla nich. 

---

**Pyt.: Dlaczego moja użytkownicy mają problemy na urządzeniach przyłączonych do usługi Azure AD po zmianie ich nazwy UPN?**

**ODP.:** Zmiany nazwy UPN nie są obecnie w pełni obsługiwane na urządzeniach przyłączonych do usługi Azure AD. Dlatego ich uwierzytelniania przy użyciu usługi Azure AD nie powiedzie się po zmianie ich nazwy UPN. W rezultacie użytkownicy mają logowania jednokrotnego i dostępu warunkowego problemy na swoich urządzeniach. W tej chwili użytkownicy muszą logować się do Windows za pośrednictwem kafelka "Innego użytkownika", przy użyciu nowych nazw UPN, aby rozwiązać ten problem. Obecnie pracujemy nad tego problemu. Jednak użytkownikom logowanie się przy użyciu Windows Hello dla firm nie stoją w obliczu ten problem. 

---

**Pyt.: Moje użytkownicy nie może wyszukać drukarki z urządzeniami dołączonymi do usługi Azure AD. Jak włączyć drukowanie z tych urządzeń?**

**ODP.:** Aby wdrożyć drukarki dla usługi Azure AD urządzenia przyłączone do, zobacz [wdrażania systemu Windows dla serwera hybrydowego Cloud Print przy użyciu wstępnego uwierzytelniania](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Należy na lokalnym serwerze systemu Windows do wdrożenia drukowania chmury hybrydowej. Obecnie usługa oparta na chmurze usługi drukowania nie jest dostępna. 

---

**Pyt.: Jak połączyć do zdalnej usługi Azure AD dołączonym do urządzenia?**

**ODP.:** Zobacz [nawiązywanie połączenia z Komputerem zdalnym przyłączonych do usługi Azure Active Directory](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

**Pyt.: Dlaczego Moi użytkownicy widzą *nie możesz tam przejść stąd*?**

**ODP.:** Czy skonfigurować niektóre zasady dostępu warunkowego będą musieli stan określonego urządzenia? Jeśli urządzenie nie spełnia kryteria, użytkownikom zostanie zablokowany, a zobaczą ten komunikat. Oceń zasady dostępu warunkowego. Upewnij się, że urządzenie spełnia kryteria, aby uniknąć wiadomości.

---

**Pyt.: Dlaczego nie niektóre z moich użytkowników uzyskasz monitów usługi Azure Multi-Factor Authentication na urządzeniach przyłączonych do usługi Azure AD?**

**ODP.:** Użytkownik może dołączyć lub zarejestrować urządzenia z usługą Azure AD przy użyciu usługi Multi-Factor Authentication. Samo urządzenie staje się drugi składnik zaufany dla tego użytkownika. Zawsze, gdy ten sam użytkownik loguje się do urządzenia i uzyskuje dostęp do aplikacji, usługi Azure AD uwzględnia urządzenia jako drugiego składnika. Umożliwia użytkownikowi uzyskiwanie dostępu do aplikacji bez dodatkowych monitów uwierzytelniania Multi-Factor Authentication. 

To zachowanie nie ma zastosowania do żadnego innego użytkownika, który loguje się do tego urządzenia. Dlatego wszystkich innych użytkowników, którzy uzyskują dostęp tego urządzenia Uzyskaj wezwanie do uwierzytelnienia Multi-Factor Authentication. Następnie one dostęp do aplikacji, które wymagają uwierzytelniania wieloskładnikowego.

---

**Pyt.: Dlaczego warto uzyskać *nazwy użytkownika lub hasło jest niepoprawne* komunikatu dla urządzenia, czy mogę po prostu przyłączone do usługi Azure AD?**

**ODP.:** W tym scenariuszu typowe przyczyny są następujące:

- Poświadczenia użytkownika nie są już prawidłowe.

- Komputer nie może komunikować się z usługą Azure Active Directory. Sprawdź, czy wszystkie problemy z połączeniem sieciowym.

- Logowania federacyjnego wymagają serwerze federacyjnym w celu obsługi punktów końcowych usługi WS-Trust, które są włączone i dostępne. 

- Włączono uwierzytelnianie przekazywane. Dlatego tymczasowe hasło musi zostać zmienione podczas logowania.

---

**Pyt.: Dlaczego widzę *Niestety... Wystąpił błąd!* okna dialogowego, gdy próbuję z usługą Azure AD join moim komputerze?**

**ODP.:** Ten błąd występuje podczas konfigurowania usługi Azure Active Directory rejestracji w usłudze Intune. Upewnij się, że użytkownik, który próbuje dołączania do usługi Azure AD ma przypisaną odpowiednią licencję usługi Intune. Aby uzyskać więcej informacji, zobacz [konfigurowania rejestracji dla urządzeń Windows](https://docs.microsoft.com/intune/windows-enroll).  

---

**Pyt.: Dlaczego próba usługi Azure AD join niepowodzeń komputera, mimo że nie mogę uzyskać informacje o błędzie?**

**ODP.:** Prawdopodobną przyczyną jest to, że użytkownik zalogowany do urządzenia przy użyciu konta wbudowanego konta administratora lokalnego. Utwórz inne konto lokalne, zanim użyjesz usługi Azure Active Directory join na zakończenie instalacji. 

---

**Pytanie: co to są certyfikaty P2P dostęp, MS organizacji w-znajduje się na naszych urządzeń z systemem Windows 10?**

**ODP.:** Certyfikaty P2P dostęp, MS organizacji w-są wydawane przez usługę Azure AD do obu przyłączonych do usługi Azure AD i urządzeń przyłączonych do hybrydowej usługi Azure AD. Te certyfikaty są używane, aby ustanowić zaufanie między urządzeniami w ramach tej samej dzierżawy dla scenariuszy usług pulpitu zdalnego. Jeden certyfikat jest wystawiony dla urządzenia, a inny wystawionego dla użytkownika. Certyfikat urządzenie znajduje się w `Local Computer\Personal\Certificates` i jest ważny przez jeden dzień. Ten certyfikat zostanie odnowiony (przez wystawienie nowego certyfikatu) Jeśli urządzenie jest nadal aktywne w usłudze Azure AD. Certyfikat użytkownika znajduje się w `Current User\Personal\Certificates` ten certyfikat również jest ważny przez jeden dzień, ale jest wystawiony na żądanie, gdy użytkownik próbuje sesji usług pulpitu zdalnego na innym urządzeniu dołączonym do usługi Azure AD. Nie zostanie odnowiony po upływie. Oba te certyfikaty są wystawiane przy użyciu certyfikatu P2P dostęp, MS organizacji w-obecne w `Local Computer\AAD Token Issuer\Certificates`. Certyfikat został wystawiony przez usługę Azure AD podczas rejestracji urządzenia. 

---

**Q:Why wielu wygasłe certyfikaty wydane przez MS-organizacji-P2P-dostęp na urządzeniach z naszego systemu Windows 10 są widoczne? Jak można je usunąć?**

**ODP.:** Wystąpił problem podczas identyfikowane w systemie Windows 10 w wersji 1709 i niższe, gdzie wygasłe certyfikaty MS-organizacji-P2P-dostęp, nadal istnieją w magazynie komputera z powodu problemów kryptograficznych. Użytkownikom można stoją w obliczu problemów z łącznością sieciową, jeśli używasz dowolnego klientów sieci VPN (np. Cisco AnyConnect), które nie może obsługiwać dużą liczbę wygasłe certyfikaty. Ten problem został rozwiązany w wersji 10 1803 systemu Windows, aby automatycznie usuwać takie wygasłe certyfikaty MS-organizacji-P2P — dostęp. Aby rozwiązać ten problem, należy zaktualizować urządzenia do systemu Windows 10 w wersji 1803. Jeśli nie można zaktualizować, możesz usunąć te certyfikaty, bez żadnych negatywnych skutków.  

---


## <a name="hybrid-azure-ad-join-faq"></a>Przyłączanie do hybrydowej usługi Azure AD — często zadawane pytania

**Pyt.: Gdzie mogę znaleźć, rozwiązywanie problemów z informacje do diagnozowania niepowodzeń przyłączanie do hybrydowej usługi Azure AD?**

**ODP.:** Aby uzyskać informacje dotyczące rozwiązywania problemów, zobacz następujące artykuły:

- [Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory urządzenia z systemem Windows 10 i Windows Server 2016 przyłączone do](troubleshoot-hybrid-join-windows-current.md)

- [Rozwiązywanie problemów z hybrydowej usługi Azure Active Directory urządzenia niskiego poziomu przyłączone do](troubleshoot-hybrid-join-windows-legacy.md)
 
**Pyt.: Dlaczego są wyświetlane zduplikowane usługi Azure AD zarejestrowanej rekord dla mojego systemu Windows 10 hybrydowej usługi Azure AD urządzenia połączonego z listy urządzeń usługi Azure AD?**

**ODP.:** Gdy użytkownicy dodać swoje konta do aplikacji na urządzeniu przyłączonym do domeny, ich może zostać wyświetlony monit o **dodać konto do Windows?** Jeśli użytkownik podał **tak** w wierszu polecenia, rejestruje urządzenie, za pomocą usługi Azure AD. Typ zaufania jest oznaczona jako zarejestrowana z usługi Azure AD. Po włączeniu dołączenie do hybrydowej usługi Azure AD w organizacji, urządzenie pobiera również hybrydowe przyłączone do usługi Azure AD. Następnie dwa stany urządzeń widoczna dla tego samego urządzenia. 

Dołączenie do hybrydowej usługi Azure AD mają pierwszeństwo przed stan usługi Azure AD zarejestrowany. Dlatego Twoje urządzenie jest uznawane za hybrydowe do uwierzytelniania i oceny dostępu warunkowego usługi Azure AD. Możesz bezpiecznie usunąć rekord urządzenia zarejestrowane z usługi Azure AD z portalu usługi Azure AD. Dowiedz się, jak [unikać lub wyczyścić ten stan podwójną na komputerze z systemem Windows 10](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 


---

**Pyt.: Dlaczego Moi użytkownicy mają problemy z urządzeniach z systemem Windows 10 hybrydowego przyłączony Azure AD po zmianie ich nazwy UPN?**

**ODP.:** Zmiany nazwy UPN nie są obecnie w pełni obsługiwane z urządzeniami dołączonymi do usługi Azure AD hybrydowych. Gdy użytkownicy mogą zalogować się do urządzenia i uzyskiwać dostęp do swoich aplikacji w środowisku lokalnym, uwierzytelniania za pomocą usługi Azure AD nie powiedzie się po zmianie nazwy UPN. W rezultacie użytkownicy mają logowania jednokrotnego i dostępu warunkowego problemy na swoich urządzeniach. W tej chwili należy odłączyć urządzenie z usługi Azure AD (Uruchom "dsregcmd /leave" z podniesionymi uprawnieniami) i ponownie Dołącz (wykonywane automatycznie) Aby rozwiązać ten problem. Obecnie pracujemy nad tego problemu. Jednak użytkownikom logowanie się przy użyciu Windows Hello dla firm nie stoją w obliczu ten problem. 

---


## <a name="azure-ad-register-faq"></a>Usługa Azure AD rejestru — często zadawane pytania

**Pyt.: Czy mogę zarejestrować urządzenia BYOD systemu Android lub iOS**

**ODP.:** Tak, ale tylko przy użyciu usługi rejestracji urządzeń na platformie Azure oraz dla klientów hybrydowych. Nie jest obsługiwana przy użyciu usługi rejestracji urządzeń lokalnych w Active Directory Federation Services (AD FS).

**Pyt.: Jak mogę zarejestrować urządzenia z systemem macOS**

**ODP.:** Wykonaj następujące czynności:

1.  [Tworzenie zasad zgodności](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definiowanie zasad dostępu warunkowego dla urządzeń z systemem macOS](../active-directory-conditional-access-azure-portal.md) 

**Uwagi:**

- Użytkownicy, zawarte w zasadach dostępu warunkowego muszą [obsługiwana wersja pakietu Office dla systemu macOS](../conditional-access/technical-reference.md#client-apps-condition) uzyskują dostęp do zasobów. 

- Podczas pierwszej próby dostępu do usługi Użytkownicy są monitowani o zarejestrowanie urządzenia przy użyciu portalu firmy.

