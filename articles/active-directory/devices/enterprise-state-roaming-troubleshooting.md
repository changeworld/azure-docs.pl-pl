---
title: Rozwiązywanie problemów z roamingiem w stanie przedsiębiorstwa w usłudze Azure Active Directory
description: Zawiera odpowiedzi na niektóre pytania administratorów IT dotyczące ustawień i synchronizacji danych aplikacji.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae8ce24aeb665a7f99326e83dbe18d020e1b6196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672348"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Rozwiązywanie problemów z ustawieniami roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory

Ten temat zawiera informacje dotyczące rozwiązywania i diagnozowania problemów z roamingiem w stanie przedsiębiorstwa oraz zawiera listę znanych problemów.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Ten artykuł dotyczy przeglądarki microsoft edge legacy w formacie HTML uruchomionej w systemie Windows 10 w lipcu 2015 r. Artykuł nie ma zastosowania do nowej przeglądarki opartej na chromie Microsoft Edge, wydanej 15 stycznia 2020 roku. Aby uzyskać więcej informacji na temat zachowania synchronizacji dla nowej przeglądarki Microsoft Edge, zobacz artykuł [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="preliminary-steps-for-troubleshooting"></a>Wstępne kroki rozwiązywania problemów 

Przed rozpoczęciem rozwiązywania problemów sprawdź, czy użytkownik i urządzenie zostały poprawnie skonfigurowane i czy wszystkie wymagania roamingu stanu przedsiębiorstwa są spełnione przez urządzenie i użytkownika. 

1. System Windows 10 z najnowszymi aktualizacjami i minimalną wersją 1511 (kompilacja systemu operacyjnego 10586 lub nowsza) jest zainstalowany na urządzeniu. 
1. Urządzenie jest przyłączone do usługi Azure AD lub przyłączone do hybrydowej usługi Azure AD. Aby uzyskać więcej informacji, zobacz [jak uzyskać urządzenie pod kontrolą usługi Azure AD](overview.md).
1. Upewnij się, że **roaming stanu przedsiębiorstwa** jest włączony dla dzierżawy w usłudze Azure AD, zgodnie z opisem w [celu włączenia roamingu w stanie przedsiębiorstwa.](enterprise-state-roaming-enable.md) Roaming można włączyć dla wszystkich użytkowników lub tylko dla wybranej grupy użytkowników.
1. Użytkownikowi jest przypisywana licencja Usługi Azure Active Directory Premium.  
1. Urządzenie musi zostać ponownie uruchomione, a użytkownik musi zalogować się ponownie, aby uzyskać dostęp do funkcji roamingu w stanie przedsiębiorstwa.

## <a name="information-to-include-when-you-need-help"></a>Informacje, które należy podać, gdy będziesz potrzebować pomocy
Jeśli nie możesz rozwiązać problemu za pomocą poniższych wskazówek, możesz skontaktować się z naszymi inżynierami pomocy technicznej. Podczas kontaktów z nimi należy podać następujące informacje:

* **Ogólny opis błędu**: Czy użytkownik widzi komunikaty o błędach? Jeśli nie było żadnego komunikatu o błędzie, opisz nieoczekiwane zachowanie, które zauważyłeś, szczegółowo. Jakie funkcje są włączone do synchronizacji i co użytkownik oczekuje synchronizacji? Czy wiele funkcji nie jest synchronizowanych lub jest odizolowanych od jednej?
* **Użytkownicy, których dotyczy problem** — czy synchronizacja działa/nie działa dla jednego użytkownika lub wielu użytkowników? Ile urządzeń jest zaangażowanych na użytkownika? Czy wszystkie z nich nie są synchronizowane lub niektóre z nich synchronizują się, a niektóre nie są synchronizowane?
* **Informacje o użytkowniku** — jaką tożsamość używa użytkownik do logowania się do urządzenia? W jaki sposób użytkownik loguje się do urządzenia? Czy są one częścią wybranej grupy zabezpieczeń do synchronizacji? 
* **Informacje o urządzeniu** — czy to urządzenie przyłączone do usługi Azure AD lub przyłączone do domeny? Na jakiej kompilacji znajduje się urządzenie? Jakie są najnowsze aktualizacje?
* **Data / godzina / strefa czasowa** - Jaka była dokładna data i godzina, w której wystąpił błąd (w tym strefa czasowa)?

Podanie tych informacji pomaga nam rozwiązać problem tak szybko, jak to możliwe.

## <a name="troubleshooting-and-diagnosing-issues"></a>Rozwiązywanie i diagnozowanie problemów

W tej sekcji przedstawiono sugestie dotyczące rozwiązywania i diagnozowania problemów związanych z roamingiem w stanie przedsiębiorstwa.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Zweryfikuj synchronizację i stronę ustawień "Synchronizuj ustawienia" 

1. Po dołączeniu komputera z systemem Windows 10 do domeny skonfigurowaną tak, aby zezwalała na roaming w stanie przedsiębiorstwa, zaloguj się za pomocą konta służbowego. Przejdź do**pozycji Konta** >  **ustawień** > **synchronizuj ustawienia** i upewnij się, że synchronizacja i poszczególne ustawienia są włączone, a górna część strony ustawień wskazuje, że synchronizujesz się z kontem służbowym. Potwierdź, że to samo konto jest również używane jako konto logowania w **Ustawieniach** > **Kont** > **Twoje informacje**. 
1. Sprawdź, czy synchronizacja działa na wielu komputerach, wprowadzając pewne zmiany na oryginalnym komputerze, takie jak przeniesienie paska zadań w prawo lub w górnej części ekranu. Obserwuj, jak zmiana rozprzestrzenia się na drugą maszynę w ciągu pięciu minut. 

   * Blokowanie i odblokowywanie ekranu (Win + L) może pomóc w wyzwoleniu synchronizacji.
   * Musisz zalogować się przy tym samym koncie na obu komputerach, aby synchronizacja działała — ponieważ roaming w stanie przedsiębiorstwa jest powiązany z kontem użytkownika, a nie z kontem komputera.

**Potencjalny problem:** Jeśli formanty na stronie **Ustawienia** nie są dostępne, a zostanie wyświetlony komunikat "Niektóre funkcje systemu Windows są dostępne tylko w przypadku korzystania z konta Microsoft lub konta służbowego". Ten problem może wystąpić dla urządzeń, które są skonfigurowane do przyłączonego do domeny i zarejestrowane w usłudze Azure AD, ale urządzenie nie zostało jeszcze pomyślnie uwierzytelnione w usłudze Azure AD. Możliwą przyczyną jest to, że zasady urządzenia muszą być stosowane, ale ta aplikacja dzieje się asynchronicznie i może być opóźnione o kilka godzin. 

### <a name="verify-the-device-registration-status"></a>Sprawdzanie stanu rejestracji urządzenia

Roaming stanu przedsiębiorstwa wymaga, aby urządzenie było zarejestrowane w usłudze Azure AD. Chociaż nie jest to specyficzne dla roamingu w stanie przedsiębiorstwa, postępowanie zgodnie z poniższymi instrukcjami może pomóc potwierdzić, że klient systemu Windows 10 jest zarejestrowany i potwierdzić odcisk palca, adres URL ustawień usługi Azure AD, stan NGC i inne informacje.

1. Otwórz wiersz polecenia bez polecenia. Aby to zrobić w systemie Windows, otwórz uruchom launcher (Win + R) i wpisz "cmd", aby otworzyć.
1. Po otwarciu wiersza polecenia wpisz "*dsregcmd.exe /status*".
1. W przypadku oczekiwanych danych wyjściowych wartość pola **AzureAdJoined** powinna wynosić "TAK", wartość pola **WamDefaultSet** powinna być "TAK", a wartość pola **WamDefaultGUID** powinna być identyfikatorem GUID z "(AzureAd)" na końcu.

**Potencjalny problem:** **WamDefaultSet** i **AzureAdJołączone** mają "NIE" w wartości pola, urządzenie zostało przyłączone do domeny i zarejestrowane w usłudze Azure AD, a urządzenie nie jest synchronizowane. Jeśli jest to pokazano, urządzenie może być konieczne czekać na zasady mają być stosowane lub uwierzytelniania dla urządzenia nie powiodło się podczas łączenia się z usługą Azure AD. Użytkownik może być musiał poczekać kilka godzin na zastosowanie zasad. Inne kroki rozwiązywania problemów mogą obejmować ponawianie próby autoregistration przez wylogowanie się i ponowne wprowadzenie lub uruchomienie zadania w Harmonogramie zadań. W niektórych przypadkach uruchomienie "*dsregcmd.exe /leave*" w oknie wiersza polecenia z podwyższonym poziomem uprawnień, ponowne uruchomienie i ponowna próba rejestracji może pomóc w tym problemie.

**Potencjalny problem:** Pole **settingsUrl** jest puste, a urządzenie nie jest synchronizowane. Użytkownik mógł ostatnio zalogować się do urządzenia, zanim roaming w stanie przedsiębiorstwa został włączony w portalu usługi Azure Active Directory. Uruchom ponownie urządzenie i zaloguj się użytkownikiem. Opcjonalnie w portalu spróbuj wyłączyć roaming w**usłudze** **Azure Active Directory,** > aby administrator IT przechodził do usługi Azure Active Directory**Devices** > Enterprise State Roaming i ponownie włączał **użytkownicy mogą synchronizować ustawienia i dane aplikacji na różnych urządzeniach.** Po ponownym włączeniu uruchom ponownie urządzenie i zaloguj się użytkownika. Jeśli to nie rozwiąże problemu, **SettingsUrl** może być pusty, jeśli istnieje zły certyfikat urządzenia. W takim przypadku uruchomienie "*dsregcmd.exe /leave*" w oknie wiersza polecenia z podwyższonym poziomem uprawnień, ponowne uruchomienie i ponowna próba rejestracji może pomóc w tym problemie.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Roaming w stanie przedsiębiorstwa i uwierzytelnianie wieloskładnikowe 

Pod pewnymi warunkami roaming w stanie przedsiębiorstwa może nie synchronizować danych, jeśli skonfigurowano uwierzytelnianie wieloskładnikowe platformy Azure. Aby uzyskać więcej informacji na temat tych objawów, zobacz dokument pomocy technicznej [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potencjalny problem:** Jeśli urządzenie jest skonfigurowane tak, aby wymagało uwierzytelniania wieloskładnikowego w portalu usługi Azure Active Directory, synchronizowanie ustawień podczas logowania się do urządzenia z systemem Windows 10 przy użyciu hasła może nie zostać zsynchronizowany. Ten typ konfiguracji uwierzytelniania wieloskładnikowego ma na celu ochronę konta administratora platformy Azure. Użytkownicy administracyjni mogą nadal być w stanie synchronizować się, logując się na swoich urządzeniach z systemem Windows 10 za pomocą numeru PIN usługi Microsoft Passport for Work lub wykonując uwierzytelnianie wieloskładnikowe podczas uzyskiwania dostępu do innych usług platformy Azure, takich jak Office 365.

**Potencjalny problem:** Synchronizacja może zakończyć się niepowodzeniem, jeśli administrator skonfiguruje zasady wieloskładnikowego dostępu warunkowego uwierzytelniania wieloskładnikowego usług federacyjnych Active Directory i token dostępu na urządzeniu. Upewnij się, że logujesz się i wylogujesz przy użyciu numeru PIN usługi Microsoft Passport for Work lub ukończysz uwierzytelnianie wieloskładnikowe podczas uzyskiwania dostępu do innych usług platformy Azure, takich jak Office 365.

### <a name="event-viewer"></a>Podgląd zdarzeń

W przypadku zaawansowanego rozwiązywania problemów podgląd zdarzeń może służyć do znajdowania określonych błędów. Są one udokumentowane w poniższej tabeli. Zdarzenia można znaleźć w obszarze Podgląd zdarzeń > dzienniki aplikacji i usług > **microsoft** > **Windows** > **SettingSync-Azure** oraz problemy związane z tożsamością z synchronizacją**AAD**systemu **Microsoft** > **Windows** > .

## <a name="known-issues"></a>Znane problemy

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synchronizacja nie działa na urządzeniach, na których aplikacje są ładowane po stronie przy użyciu oprogramowania MDM

Wpływa na urządzenia z rocznicową aktualizacją systemu Windows 10 (wersja 1607). W Podglądzie zdarzeń w obszarze Dzienniki SettingSync-Azure często jest widoczny identyfikator zdarzenia 6013 z błędem 80070259.

**Zalecana akcja**  
Upewnij się, że klient systemu Windows 10 v1607 ma aktualizację zbiorczą z 23 sierpnia 2016 r.[(KB3176934](https://support.microsoft.com/kb/3176934) Kompilacja systemu operacyjnego 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Ulubione programu Internet Explorer nie są synchronizowane

Wpływa na urządzenia z systemem Windows 10 November Update (wersja 1511).

**Zalecana akcja**  
Upewnij się, że klient systemu Windows 10 v1511 ma aktualizację zbiorczą z lipca 2016 r. (kompilacja systemu operacyjnego[KB3172985](https://support.microsoft.com/kb/3172985) 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Motyw nie jest synchronizowany, a także dane chronione za pomocą ochrony informacji systemu Windows 

Aby zapobiec wyciekowi danych, dane chronione za pomocą [ochrony informacji systemu Windows](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) nie będą synchronizowane za pośrednictwem roamingu w stanie przedsiębiorstwa dla urządzeń korzystających z rocznicowej aktualizacji systemu Windows 10.

**Zalecana akcja**  
Brak. Przyszłe aktualizacje systemu Windows mogą rozwiązać ten problem.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Ustawienia daty, godziny i regionu nie są synchronizowane na urządzeniu przyłączanym do domeny 
  
Urządzenia, które są przyłączone do domeny, nie będą synchronizowane dla ustawienia Data, Godzina i Region: czas automatyczny. Użycie czasu automatycznego może zastąpić inne ustawienia Daty, Godziny i Regionu i spowodować, że te ustawienia nie zostaną zsynchronizowane. 

**Zalecana akcja**  
Brak. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>Monity użytkownika podczas synchronizowania haseł

Wpływa na urządzenia z systemem Windows 10 November Update (wersja 1511) z bezprzewodową kartą sieciową skonfigurowaną do synchronizowania haseł.

**Zalecana akcja**  
Upewnij się, że klient systemu Windows 10 w wersji 1511 ma aktualizację zbiorczą (KOMPILACJA SYSTEMU OPERACYJNEGO[KB3140743](https://support.microsoft.com/kb/3140743) 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synchronizacja nie działa na urządzeniach korzystających z karty inteligentnej do logowania

Jeśli spróbujesz zalogować się do urządzenia z systemem Windows przy użyciu karty inteligentnej lub wirtualnej karty inteligentnej, synchronizacja ustawień przestanie działać.     

**Zalecana akcja**  
Brak. Przyszłe aktualizacje systemu Windows mogą rozwiązać ten problem.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Urządzenie przyłączone do domeny nie synchronizuje się po opuszczeniu sieci firmowej     

Urządzenia przyłączone do domeny zarejestrowane w usłudze Azure AD mogą wystąpić niepowodzenie synchronizacji, jeśli urządzenie jest poza siedzibą firmy przez dłuższy czas, a uwierzytelnianie domeny nie może zostać ukończone.

**Zalecana akcja**  
Podłącz urządzenie do sieci firmowej, aby można było wznowić synchronizację.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Urządzenie przyłączone do usługi Azure AD nie jest synchronizowane, a użytkownik ma nazwę główną użytkownika w przypadku mieszanym.

Jeśli użytkownik ma mieszane przypadku UPN (na przykład UserName zamiast nazwy użytkownika) i użytkownik znajduje się na urządzeniu przyłączonego do usługi Azure AD, który uaktualniony z systemu Windows 10 Build 10586 do 14393, urządzenie użytkownika może nie zostać zsynchronizowany. 

**Zalecana akcja**  
Użytkownik będzie musiał odłączyć i ponownie dołączyć urządzenie do chmury. Aby to zrobić, zaloguj się jako użytkownik administratora lokalnego i odłącz urządzenie, przechodząc do **pozycji Ustawienia** > **informacje o systemie** > **About** i wybierz opcję "Zarządzaj lub odłącz się od pracy lub szkoły". Wyczyść pliki poniżej, a następnie usługa Azure AD Dołącz do urządzenia ponownie w **ustawieniach** > **systemowych** > **informacje** i wybierając opcję "Połącz z pracą lub szkołą". Kontynuuj dołączanie urządzenia do usługi Azure Active Directory i ukończenie przepływu.

W kroku oczyszczania wyczyść następujące pliki:
- Ustawienia.dat w`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Wszystkie pliki w folderze`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Identyfikator zdarzenia 6065: 80070533 Ten użytkownik nie może się zalogować, ponieważ to konto jest obecnie wyłączone  

W Podglądzie zdarzeń w obszarze Dzienniki SettingSync/Debug ten błąd jest widoczny po wygaśnięciu poświadczeń użytkownika. Ponadto może wystąpić, gdy dzierżawa nie automatycznie azurerms aprowizowana. 

**Zalecana akcja**  
W pierwszym przypadku użytkownik zaktualizował swoje poświadczenia i zalogował się do urządzenia przy użyciu nowych poświadczeń. Aby rozwiązać problem usługi AzureRMS, należy wykonać kroki wymienione w aktualizacji [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Identyfikator zdarzenia 1098: Błąd: operacja brokera tokenów 0xCAA5001C nie powiodła się  

W Podglądzie zdarzeń w obszarze dzienników AAD/Operacyjnych ten błąd może być widoczny w przypadku wywołania wtyczki Event 1104: AAD Cloud AP Get token returned error: 0xC000005F. Ten problem występuje, jeśli brakuje uprawnień lub atrybutów własności.  

**Zalecana akcja**  
Przejdź do czynności wymienionych w aktualizacji [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie, zobacz [omówienie roamingu stanu przedsiębiorstwa](enterprise-state-roaming-overview.md).
