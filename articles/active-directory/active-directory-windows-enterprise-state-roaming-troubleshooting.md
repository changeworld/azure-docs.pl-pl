---
title: Rozwiązywanie problemów z ustawieniami roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zawiera odpowiedzi na pytania Administratorzy IT mogą mieć o ustawieniach i synchronizacji danych aplikacji.
services: active-directory
keywords: Enterprise mobilnego ustawienia stanu, chmury systemu windows, często zadawane pytania na roamingu stanu przedsiębiorstwa
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: tanning
ms.custom: it-pro
ms.openlocfilehash: b5ade0f3a6590832b6717bbb5f91a4d62cdc9e0c
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309047"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Rozwiązywanie problemów z ustawieniami roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory

Ten temat zawiera informacje na temat Rozwiązywanie problemów i diagnozowanie problemów z roamingu stanu przedsiębiorstwa i udostępnia listę znanych problemów.

## <a name="preliminary-steps-for-troubleshooting"></a>Wstępne kroki rozwiązywania problemów 
Przed rozpoczęciem rozwiązywania problemów, sprawdź, czy użytkownika i urządzenia zostały skonfigurowane prawidłowo i czy wszystkie wymagania roamingu stanu przedsiębiorstwa są spełnione przez urządzenia i użytkownika. 

1. Windows 10 z najnowsze aktualizacje i minimalnej wersji 1511 (kompilacja systemu operacyjnego 10586 lub nowszym) jest zainstalowany na urządzeniu. 
2. Urządzenie jest usługi Azure AD dołączona lub przyłączony do hybrydowej usługi Azure AD. Aby uzyskać więcej informacji, zobacz [jak urządzenie pod kontrolą programu Azure AD](device-management-introduction.md).
3. Upewnij się, że **roamingu stanu przedsiębiorstwa** jest włączone dla dzierżawy w usłudze Azure AD, zgodnie z opisem w [umożliwiające roamingu stanu przedsiębiorstwa](active-directory-windows-enterprise-state-roaming-enable.md). Można włączyć mobilnego dla wszystkich użytkowników lub dla wybranej grupy użytkowników.
4. Użytkownik musi już przypisany licencji usługi Azure Active Directory Premium.  
25. Należy ponownie uruchomić urządzenie, a użytkownik musi zarejestrować ponownie dostęp do funkcji roamingu stanu przedsiębiorstwa.

## <a name="information-to-include-when-you-need-help"></a>Informacje do dołączenia, gdy potrzebujesz pomocy
Jeśli nie możesz rozwiązać problemu z poniższych wskazówek, można skontaktuj się z naszym pracowników działu pomocy technicznej. Gdy musisz skontaktować się z nimi zawierają następujące informacje:

* **Ogólny opis błędu**: istnieją komunikaty o błędach, które zostały odebrane przez użytkownika? Jeśli nie istnieje żaden komunikat o błędzie, opisano nieoczekiwanego zachowania, które można zauważyć szczegółowo. Jakie funkcje są włączone do celów synchronizacji, a co jest użytkownika oczekiwano synchronizacji? Wiele funkcji nie synchronizacji lub jest on izolowane, ograniczone do jednego?
* **Użytkownicy, których to dotyczy** — jest synchronizacja pracy/niepowodzeniem dla jednego użytkownika lub wielu użytkowników? Ile urządzeń są zaangażowane na użytkownika? Są je nie synchronizuje wszystkie lub niektóre z nich synchronizowania i niektóre nie synchronizuje?
* **Informacje o użytkowniku** — jakie tożsamość jest użytkownika za pomocą do logowania się na urządzeniu? Jak użytkownik loguje się do urządzenia? Są one częścią wybranej grupy zabezpieczeń wykonywać synchronizację? 
* **Informacje o urządzeniu** — jest to urządzenie Azure przyłączonych do usługi AD lub przyłączonym do domeny? Jakie kompilacji znajduje się na urządzeniu? Jakie są najnowsze aktualizacje?
- **Daty i godziny / strefy czasowej** — jaka była dokładnej daty i czasu był wyświetlany błąd (m.in. strefa czasowa)?

Te informacje w tym pomoże nam w możliwie najkrótszym czasie rozwiązać tego problemu.

## <a name="troubleshooting-and-diagnosing-issues"></a>Rozwiązywanie i diagnozowanie problemów
W tej sekcji przedstawiono sugestie dotyczące Rozwiązywanie problemów i diagnozowanie problemów związanych z roamingu stanu przedsiębiorstwa.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Sprawdź, synchronizacji i na stronie ustawień "Synchronizuj swoje ustawienia" 

1. Po dołączeniu komputera z systemem Windows 10 do domeny, która jest skonfigurowana do zezwalania roamingu stanu przedsiębiorstwa, należy zalogować się z kontem służbowym. Przejdź do **ustawienia** > **kont** > **ustawień synchronizacji** i upewnij się, że synchronizacja i poszczególnych ustawień znajdują się na oraz że na początku Strona Ustawienia wskazuje, że trwa synchronizacja z kontem służbowym. Upewnij się, to samo konto jest również używane jako konto logowania w **ustawienia** > **kont** > **Twoje informacje**. 
2. Sprawdź, czy synchronizacja działa na wielu komputerach, wprowadzając niektóre zmiany na maszynie oryginalnej, takie jak przeniesienie na pasku zadań do prawej lub górnej krawędzi ekranu. Obejrzyj zmiany są propagowane do drugiej maszyny w ciągu pięciu minut. 
  * Blokowanie i odblokowywanie, ekranu (Windows + L) może ułatwić wyzwalanie synchronizacji.
  * Możesz musi można zalogować się tego samego konta na obu komputerach synchronizacji do pracy — zgodnie z roamingu stanu przedsiębiorstwa jest powiązany z konta użytkownika, a nie konta komputera.

**Potencjalny problem**: Jeśli formantów w **ustawienia** strony nie są dostępne i zostanie wyświetlony komunikat "niektóre funkcje systemu Windows są dostępne tylko, jeśli używasz konta Microsoft lub konta służbowego." Ten problem może wystąpić dla urządzeń, które są ustawione do być przyłączone do domeny i zarejestrowanych w usłudze Azure AD, ale urządzenie nie został jeszcze pomyślnie uwierzytelniony do usługi Azure AD. Możliwą przyczyną jest zasady urządzeń należy zastosować, że ta aplikacja asynchroniczne i może zostać opóźnione przez kilka godzin. 

### <a name="verify-the-device-registration-status"></a>Sprawdź stan rejestracji urządzenia
Roamingu stanu przedsiębiorstwa wymaga urządzenia, które mają być zarejestrowane w usłudze Azure AD. Chociaż nie dotyczą roamingu stanu przedsiębiorstwa, zgodnie z instrukcjami poniżej mogą pomóc potwierdzić, że klient systemu Windows 10 jest zarejestrowany i potwierdzić odcisku palca, adres URL ustawienia usługi Azure AD, stan NGC oraz inne informacje.

1.  Otwórz wiersz polecenia wyrównano. Aby to zrobić w systemie Windows, otwórz uruchamiania wykonywania (Win + R), a następnie wpisz "cmd" Aby otworzyć.
2.  Po otwarciu w wierszu polecenia wpisz "*/status dsregcmd.exe*".
3.  Dla oczekiwane dane wyjściowe **AzureAdJoined** wartość pola powinna być "Tak", **WamDefaultSet** wartość pola powinna być "Tak" i **WamDefaultGUID** wartości pola powinny być identyfikatorem GUID z "(AzureAd)" na końcu.

**Potencjalny problem**: **WamDefaultSet** i **AzureAdJoined** zarówno mieć "Nie" w wartości pola, urządzenie zostało przyłączone do domeny i zarejestrowane w usłudze Azure AD i synchronizacji urządzenia. Jeśli jest on wyświetlany, urządzenie może być konieczne poczekanie, aż zasady do zastosowania lub uwierzytelniania urządzenia nie powiodła się podczas nawiązywania połączenia z usługą Azure AD. Użytkownik może być konieczne Poczekaj kilka godzin, zasad, które mają być stosowane. Z procedurą rozwiązywania problemów mogą obejmować ponawianie próby rejestracji automatycznej przy wylogowaniu się i z powrotem w lub uruchamianie zadań w harmonogramie zadań. W niektórych przypadkach uruchomiona "*dsregcmd.exe /leave*" w oknie wiersza polecenia z podwyższonym poziomem uprawnień, ponowny rozruch i podjęcie ponownej próby rejestracji mogą pomoc dotyczącą tego problemu.


**Potencjalny problem**: pole **AzureAdSettingsUrl** jest pusta i nie synchronizować urządzenia. Użytkownik może ostatnio zalogowali się do urządzenia przed włączeniem roamingu stanu przedsiębiorstwa w portalu Azure Active Directory. Uruchom ponownie urządzenie i mieć dane logowania użytkownika. Opcjonalnie w portalu, spróbuj o administratora IT, wyłącz i ponownie włączyć użytkownicy mogą synchronizacji ustawień i danych aplikacji przedsiębiorstwa. Po ponownym włączeniu ponownie uruchomić urządzenie i mieć dane logowania użytkownika. Jeśli to nie rozwiąże problemu, **AzureAdSettingsUrl** może być pusty w przypadku certyfikatów zły urządzenia. W takim przypadku uruchomiona "*dsregcmd.exe /leave*" w oknie wiersza polecenia z podwyższonym poziomem uprawnień, ponowny rozruch i podjęcie ponownej próby rejestracji mogą pomoc dotyczącą tego problemu.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Roaming stanu przedsiębiorstwa i uwierzytelniania wieloskładnikowego 
W niektórych warunkach roamingu stanu przedsiębiorstwa może zakończyć się niepowodzeniem na synchronizowanie danych, jeśli skonfigurowano uwierzytelnianie wieloskładnikowe Azure. Aby uzyskać dodatkowe informacje na następujące symptomy, zobacz dokument pomocy technicznej [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potencjalny problem**: Jeśli urządzenie jest skonfigurowany do żądania uwierzytelniania wieloskładnikowego w portalu usługi Azure Active Directory, może być niemożliwe Synchronizuj ustawienia podczas logowania się na urządzenia z systemem Windows 10 przy użyciu hasła. Ten typ konfiguracji usługi Multi-Factor Authentication ma chronić konto administratora platformy Azure. Użytkownicy Administratorzy nadal można zsynchronizować logując się na urządzeniach z systemem Windows 10 z ich Microsoft Passport dla pracy numeru PIN lub wykonując uwierzytelnianie wieloskładnikowe podczas uzyskiwania dostępu do innych usług Azure, takich jak usługi Office 365.

**Potencjalny problem**: synchronizacja może zakończyć się niepowodzeniem, jeśli administrator konfiguruje zasady dostępu warunkowego uwierzytelniania wieloskładnikowego usług federacyjnych Active Directory i wygaśnięcia tokenu dostępu na urządzeniu. Upewnij się zalogujesz, wyloguj się przy użyciu Microsoft Passport dla numeru PIN pracy lub wykonać uwierzytelnianie wieloskładnikowe podczas uzyskiwania dostępu do innych usług Azure, takich jak usługi Office 365.

### <a name="event-viewer"></a>Podgląd zdarzeń
Do zaawansowanego rozwiązywania problemów można znaleźć określone błędy Podgląd zdarzeń. Te są opisane w poniższej tabeli. Zdarzenia można znaleźć w Podglądzie zdarzeń > Dzienniki aplikacji i usług > **Microsoft** > **Windows** > **SettingSync** i problemów z synchronizacją tożsamości **Microsoft** > **Windows** > **AAD**.


## <a name="known-issues"></a>Znane problemy

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synchronizacja nie działa na urządzeniach, które znajdują się aplikacje ładowana za pomocą oprogramowania MDM

Wpływa na urządzeniach z systemem Windows 10 Anniversary Update (w wersji 1607). W Podglądzie zdarzeń w obszarze dzienniki SettingSync Azure 6013 identyfikator zdarzenia z powodu błędu 80070259 często jest widoczny.

**Zalecana akcja**  
Upewnij się, że klient systemu Windows 10 v1607 ma 23 sierpnia 2016 aktualizacji zbiorczej ([KB3176934](https://support.microsoft.com/kb/3176934) 14393.82 kompilacji systemu operacyjnego). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Ulubione programu Internet Explorer synchronizacji.

Wpływa na urządzeniach z systemem Windows 10 listopada aktualizację (wersja 1511).

**Zalecana akcja**  
Upewnij się, że klient systemu Windows 10 v1511 ma lipca 2016 r aktualizacji zbiorczej ([KB3172985](https://support.microsoft.com/kb/3172985) 10586.494 kompilacji systemu operacyjnego).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Nie jest synchronizowany motywu, a także dane chronione przy użyciu systemu Windows Information Protection 

Aby uniknąć wycieków danych, danych, który jest chroniony za pomocą [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) zostanie zsynchronizowany przy użyciu roamingu stanu przedsiębiorstwa dla urządzeń w usłudze Windows Update rocznicy 10.



**Zalecana akcja**  
Brak. Przyszłe aktualizacje systemu Windows może rozwiązać ten problem.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Ustawienia daty, godziny i Region nie synchronizowane na urządzeniu przyłączonym do domeny 
  
Urządzenia, które są przyłączone do domeny nie będzie działać z synchronizacji ustawienie daty, godziny i regionu: automatyczne czasu. Przy użyciu automatycznego czasu może zastąpić inne ustawienia daty, godziny i regionu i spowodować tych ustawień nie można zsynchronizować. 

**Zalecana akcja**  
Brak. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>Podczas synchronizacji haseł monitów kontroli konta użytkownika

Wpływa na urządzeniach z systemem Windows 10 listopada aktualizację (wersja 1511) z sieci bezprzewodowej kart interfejsu Sieciowego, który jest skonfigurowany do synchronizacji haseł.

**Zalecana akcja**  
Upewnij się, że klient systemu Windows 10 v1511 ma aktualizacji zbiorczej ([KB3140743](https://support.microsoft.com/kb/3140743) 10586.494 kompilacji systemu operacyjnego).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synchronizacja nie działa na urządzeniach, które użycie karty inteligentnej do logowania
Jeśli do logowania się na urządzeniu z systemem Windows przy użyciu karty inteligentnej lub wirtualnej karty inteligentnej, ustawienia synchronizacji przestaną działać.     

**Zalecana akcja**  
Brak. Przyszłe aktualizacje systemu Windows może rozwiązać ten problem.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Urządzenia przyłączone do domeny nie jest synchronizowany po opuszczeniu sieci firmowej     
Przyłączonych do domeny urządzenia zarejestrowane w usłudze Azure AD mogą wystąpić błąd synchronizacji, jeśli urządzenie znajduje się poza nim przez dłuższy czas i nie można ukończyć uwierzytelniania domeny.

**Zalecana akcja**  
Podłącz urządzenie do sieci firmowej, dzięki którym możliwe wznowienie synchronizacji.

---

 ### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Azure AD Joined urządzenie nie jest synchronizowany, a użytkownik ma mieszane przypadku nazwa główna użytkownika.
 Jeśli użytkownik ma mieszane przypadku głównej nazwy użytkownika (np. nazwę użytkownika zamiast nazwy użytkownika), a użytkownik na urządzeniu Joined usługi Azure AD, który został uaktualniony z systemu Windows 10 kompilacji 10586 do 14393, urządzenie użytkownika może zakończyć się niepowodzeniem do synchronizacji. 

**Zalecana akcja**  
Użytkownik będzie musiał Odłączanie i ponowne przyłączenie urządzenia do chmury. W tym Zaloguj się jako użytkownik administratora lokalnego i odłączyć urządzenie, przechodząc do **ustawienia** > **systemu** > **o** i wybierz pozycję "Zarządzaj lub Odłącz od pracy lub nauki". Czyszczenie plików poniżej i Azure AD Join urządzenia ponownie w **ustawienia** > **systemu** > **o** i wybierając pozycję "Połącz z pracy lub Służbowe". Nadal dołączenie urządzenia do usługi Azure Active Directory i zakończyć przepływ.

W kroku oczyszczania oczyszczania następujące pliki:
- Settings.dat w `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Wszystkie pliki w folderze `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Identyfikator zdarzenia 6065:80070533 przez tego użytkownika nie może zalogować, ponieważ to konto jest obecnie wyłączona.  
W Podglądzie zdarzeń w obszarze dzienniki SettingSync/Debug ten błąd może być widoczny podczas wygasły poświadczenia użytkownika. Ponadto może wystąpić, gdy dzierżawcy nie miał automatycznie AzureRMS udostępnione. 

**Zalecana akcja**  
W pierwszym przypadku mają Zaktualizuj swoje poświadczenia i zaloguj do urządzenia z nowymi poświadczeniami użytkownika. Aby rozwiązać ten problem AzureRMS, przejść do czynności opisane w [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Identyfikator zdarzenia 1098: Błąd: 0xCAA5001C Token brokera operacja nie powiodła się  
W Podglądzie zdarzeń w obszarze dzienniki AAD/Operational tego błędu mogą być widoczne z 1104 zdarzeń: wywołanie wtyczki region chmury AAD token operacji Get zwróciło błąd: 0xC000005F. Ten problem występuje, gdy brakuje uprawnienia lub prawa własności atrybutów.  

**Zalecana akcja**  
Kontynuuj kroki [KB3196528](https://support.microsoft.com/kb/3196528).  



## <a name="next-steps"></a>Kolejne kroki

- Użyj [forum User Voice](https://social.technet.microsoft.com/Forums/windows/en-US/f51c856c-db92-4cf7-a497-720da21d7d31/enterprise-state-roaming) Aby przekazać opinie i sugestie dotyczące ulepszenia roamingu stanu przedsiębiorstwa.

- Aby uzyskać więcej informacji, zobacz [opis roamingu stanu przedsiębiorstwa](active-directory-windows-enterprise-state-roaming-overview.md). 

## <a name="related-topics"></a>Powiązane tematy
* [Przegląd roamingu stanu przedsiębiorstwa](active-directory-windows-enterprise-state-roaming-overview.md)
* [Włącz roaming stanu przedsiębiorstwa w usłudze Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Ustawienia i dane mobilne — często zadawane pytania](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Zasady grupy i ustawienia zarządzania urządzeniami Przenośnymi dla ustawień synchronizacji](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Informacje dotyczące ustawień roamingu systemu Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
