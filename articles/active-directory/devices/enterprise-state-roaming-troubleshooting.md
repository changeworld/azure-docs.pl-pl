---
title: Rozwiązywanie problemów z ustawieniami Roaming stanu dla przedsiębiorstw w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zawiera odpowiedzi na pytania administratorów IT może być o ustawieniach i synchronizowanie danych aplikacji.
services: active-directory
keywords: Enterprise stan ustawień mobilnego dostępu systemu windows w chmurze, często zadawane pytania dotyczące roaming stanu dla przedsiębiorstw
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.subservice: devices
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: joflore
ms.reviewer: tanning
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cce60769ffae399062a50e1b2c28c1cd6e49a47e
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904257"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Rozwiązywanie problemów z ustawieniami Roaming stanu dla przedsiębiorstw w usłudze Azure Active Directory

Ten temat zawiera informacje na temat rozwiązywania problemów i diagnozowania problemów z Roaming stanu dla przedsiębiorstw i zawiera listę znanych problemów.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="preliminary-steps-for-troubleshooting"></a>Wstępne kroki rozwiązywania problemów z 

Przed rozpoczęciem rozwiązywania problemów, sprawdź, czy użytkowników i urządzeń zostały skonfigurowane prawidłowo, i że spełniono wszystkie wymagania Roaming stanu dla przedsiębiorstw, przez urządzenia i użytkownika. 

1. Windows 10 z najnowszymi aktualizacjami i minimalnej wersji 1511 (kompilacja systemu operacyjnego 10586 lub nowszej) jest zainstalowana na urządzeniu. 
1. Urządzenie jest w usłudze Azure AD przyłączony lub przyłączone do hybrydowej usługi Azure AD. Aby uzyskać więcej informacji, zobacz [jak urządzenie pod kontrolą programu Azure AD](overview.md).
1. Upewnij się, że **Roaming stanu dla przedsiębiorstw** jest włączona dla dzierżawy w usłudze Azure AD, zgodnie z opisem w [włączyć Roaming stanu dla przedsiębiorstw](enterprise-state-roaming-enable.md). Aby umożliwić mobilnego dla wszystkich użytkowników, czy dla wybranej grupy użytkowników.
1. Użytkownik już musi mieć przypisaną licencję usługi Azure Active Directory Premium systemu Azure.  
1. Należy ponownie uruchomić urządzenie, a użytkownik musi zalogować się ponownie do uzyskania dostępu do funkcji Roaming stanu dla przedsiębiorstw.

## <a name="information-to-include-when-you-need-help"></a>Informacje do uwzględnienia, gdy będziesz potrzebować pomocy
Nie można rozwiązać problem z poniższych wskazówek, można skontaktować się z inżynierami pomocy technicznej. Gdy możesz skontaktować się z nimi, należy podać następujące informacje:

* **Ogólny opis błędu**: Czy są widoczne dla użytkownika komunikaty o błędach? Jeśli nie wystąpił żaden komunikat o błędzie, opisz nieoczekiwanego zachowania, które możesz zauważyć, że szczegółowo. Jakie funkcje są włączone do celów synchronizacji, a co to jest użytkownika oczekiwano synchronizacji? Jest wiele funkcji nie synchronizuje, czy jest izolowane do jednego?
* **Użytkownicy, których to dotyczy** — są zsynchronizowane roboczego/niepowodzenia dla jednego użytkownika lub wielu użytkowników? Ile urządzeń biorących udział na użytkownika? Są je nie synchronizuje wszystkie lub niektóre z nich synchronizowania, a niektóre nie synchronizuje?
* **Informacje o użytkowniku** — jakie tożsamość jest użytkownika za pomocą do logowania się na urządzeniu? Jak użytkownik loguje się do urządzenia? Są one częścią wybranej grupy zabezpieczeń mogą przeprowadzać synchronizacje? 
* **Informacje o urządzeniu** — czy to urządzenie Azure przyłączone do usługi AD lub przyłączone do domeny? Jakie kompilacji włączeniu urządzenia? Jakie są najnowsze aktualizacje?
* **Data / Godzina / strefa czasowa** — co było dokładne daty i godziny, o których wystąpił błąd (w tym strefa czasowa)?

W tym te informacje pomagają nam możliwie jak najszybciej rozwiązać tego problemu.

## <a name="troubleshooting-and-diagnosing-issues"></a>Rozwiązywanie i diagnozowanie problemów
W tej sekcji przedstawiono sugestie dotyczące rozwiązywania problemów i diagnozowania problemów związanych z Roaming stanu dla przedsiębiorstw.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Sprawdź, synchronizacji i na stronie ustawień "Synchronizować ustawienia" 

1. Po dołączeniu komputerze z systemem Windows 10 do domeny, która jest skonfigurowana do Zezwalaj na Roaming stanu dla przedsiębiorstw, zaloguj się przy użyciu swojego konta służbowego. Przejdź do **ustawienia** > **kont** > **ustawień synchronizacji** i upewnij się, że synchronizacji i poszczególne ustawienia znajdują się na i że górnej części Strona Ustawienia wskazuje, że są synchronizowane, przy użyciu swojego konta służbowego. Upewnij się, tego samego konta jest również używane jako konto logowania w **ustawienia** > **kont** > **Your informacje**. 
1. Sprawdź, czy synchronizacja, działa na wielu komputerach, dokonując pewnych zmian w oryginalnej maszyny, takie jak przeniesienie paska zadań do prawej lub górnej krawędzi ekranu. Obejrzyj zmiany są propagowane do drugiej maszyny w ciągu pięciu minut. 

   * Blokowanie i odblokowywanie ekranu (Win + L) może pomóc włączania synchronizacji.
   * Możesz musi można zalogować się tego samego konta na obu komputerach do celów synchronizacji do pracy — jak Roaming stanu dla przedsiębiorstw jest powiązany z konta użytkownika, a nie na koncie komputera.

**Potencjalny problem**: Jeśli kontrolek w **ustawienia** strony nie są dostępne, i zostanie wyświetlony komunikat "niektóre funkcje Windows są dostępne tylko jeśli używasz konta Microsoft lub konta służbowego." Ten problem, mogą wystąpić w przypadku urządzeń, które są skonfigurowane do przyłączonych do domeny i zarejestrowana na potrzeby usługi Azure AD, ale urządzenia nie został jeszcze pomyślnie uwierzytelniony z usługą Azure AD. Możliwą przyczyną jest muszą być stosowane zasady urządzeń, że ta aplikacja się asynchronicznie stanie i może być opóźniony przez kilka godzin. 

### <a name="verify-the-device-registration-status"></a>Sprawdzanie stanu rejestracji urządzenia

Roaming stanu dla przedsiębiorstw wymaga urządzenia do zarejestrowania w usłudze Azure AD. Chociaż nie odnoszą się do Roaming stanu dla przedsiębiorstw, postępując zgodnie z instrukcjami poniżej mogą pomóc potwierdzić, że klient dla systemu Windows 10 jest zarejestrowany i upewnij się, stan NGC odcisku palca, adres URL ustawień usługi Azure AD oraz inne informacje.

1.  Otwórz wiersz polecenia podnoszenia. Aby to zrobić w Windows, Otwórz okno dialogowe Uruchamianie Uruchom (Win + R), a następnie wpisz "cmd" Aby otworzyć.
2.  Po otwarciu w wierszu polecenia wpisz "*status dsregcmd.exe*".
3.  Oczekiwane dane wyjściowe, aby uzyskać **AzureAdJoined** pola. wartość powinna być "YES" **WamDefaultSet** pola. wartość powinna być "YES" i **WamDefaultGUID** wartości pola powinny być identyfikatorem GUID za pomocą "(AzureAd)" na końcu.

**Potencjalny problem**: **WamDefaultSet** i **AzureAdJoined** zarówno "NO" w wartości pola, urządzenie zostało zarejestrowane w usłudze Azure AD i przyłączone do domeny i nie synchronizuje urządzenia. Jeśli jest on wyświetlany, urządzenie może być konieczne poczekanie, aż zasady, które mają być stosowane lub uwierzytelniania dla tego urządzenia nie powiodła się podczas nawiązywania połączenia z usługą Azure AD. Użytkownik może być konieczne Poczekaj kilka godzin, zasad, które mają być stosowane. Pozostałe kroki rozwiązywania problemów może obejmować ponawianie próby rejestracji automatycznej przy wylogowaniu i z powrotem w lub uruchamianie zadań w harmonogramie zadań. W niektórych przypadkach uruchamianie "*dsregcmd.exe /leave*" w okno Wiersz polecenia z podwyższonym poziomem uprawnień, ponowne uruchamianie i podjęcie ponownej próby rejestracji może pomocy dotyczącej tego problemu.


**Potencjalny problem**: Pole **SettingsUrl** jest pusta i nie synchronizuje urządzenia. Użytkownik może ostatnio zalogowali się do urządzenia przed Roaming stanu dla przedsiębiorstw została włączona w portalu Azure Active Directory. Uruchom ponownie urządzenie i mieć dane logowania użytkownika. Opcjonalnie, w portalu, spróbuj o administratora IT, przejdź do **usługi Azure Active Directory** > **urządzeń** > **Roaming stanu dla przedsiębiorstw** Wyłącz i ponownie włącz **użytkownicy mogą synchronizować ustawień i danych aplikacji na urządzeniach**. Po włączeniu ponownie, uruchom ponownie urządzenie i mieć dane logowania użytkownika. Jeśli to nie rozwiąże problemu, **SettingsUrl** może być pusty w przypadku certyfikatu zły urządzenia. W przypadku uruchamiania "*dsregcmd.exe /leave*" w okno Wiersz polecenia z podwyższonym poziomem uprawnień, ponowne uruchamianie i podjęcie ponownej próby rejestracji może pomocy dotyczącej tego problemu.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Roaming stanu dla przedsiębiorstw i uwierzytelniania wieloskładnikowego 

W pewnych okolicznościach Roaming stanu dla przedsiębiorstw może zakończyć się niepowodzeniem na synchronizowanie danych, jeśli skonfigurowano uwierzytelnianie wieloskładnikowe systemu Azure. Więcej informacji na temat tych objawów, zobacz dokument pomocy technicznej [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potencjalny problem**: Jeśli urządzenie jest skonfigurowany do wymagania uwierzytelniania wieloskładnikowego w portalu usługi Azure Active Directory, może zakończyć się niepowodzeniem, synchronizowanie ustawień podczas logowania się do urządzenia z systemem Windows 10 przy użyciu hasła. Ten typ konfiguracji usługi Multi-Factor Authentication ma na celu ochronę konta administratora platformy Azure. Administratorzy nadal można zsynchronizować, logując się do urządzeń z systemem Windows 10 za pomocą ich Microsoft Passport pracy numeru PIN lub wykonując uwierzytelnianie wieloskładnikowe podczas uzyskiwania dostępu do innych usług platformy Azure, takich jak Office 365.

**Potencjalny problem**: Synchronizacja może zakończyć się niepowodzeniem, jeśli administrator konfiguruje zasady dostępu warunkowego uwierzytelniania Multi-Factor Authentication usług federacyjnych Active Directory i wygaśnięcia ważności tokenu dostępu na urządzeniu. Upewnij się, zaloguj się, a następnie się wylogować, przy użyciu Microsoft Passport pracy numeru PIN lub wykonać uwierzytelnianie wieloskładnikowe podczas uzyskiwania dostępu do innych usług platformy Azure, takich jak Office 365.

### <a name="event-viewer"></a>Podgląd zdarzeń

Do zaawansowanego rozwiązywania problemów, aby znaleźć określone błędy może służyć podglądu zdarzeń. Te są opisane w poniższej tabeli. Zdarzenia można znaleźć w Podglądzie zdarzeń > Dzienniki aplikacji i usług > **Microsoft** > **Windows** > **SettingSync Azure** i związanych z tożsamościami problemów z synchronizacją **Microsoft** > **Windows** > **AAD**.

## <a name="known-issues"></a>Znane problemy

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synchronizacja nie działa na urządzeniach, które znajdują się aplikacje ładowanych za pomocą oprogramowania MDM

Wpływa na urządzeniach z systemem Windows 10 Anniversary Update (wersja 1607). W Podglądzie zdarzeń w obszarze dzienników SettingSync Azure 6013 identyfikator zdarzenia z powodu błędu 80070259 często jest widoczny.

**Zalecana akcja**  
Upewnij się, klient v1607 systemu Windows 10 ma 23 sierpnia 2016 aktualizacji zbiorczej ([KB3176934](https://support.microsoft.com/kb/3176934) 14393.82 kompilacji systemu operacyjnego). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Ulubione programu Internet Explorer nie są synchronizowane.

Wpływa na urządzeniach z systemem Windows 10 listopada Update (wersja 1511).

**Zalecana akcja**  
Upewnij się, klient v1511 systemu Windows 10 ma lipca 2016 aktualizacji zbiorczej ([KB3172985](https://support.microsoft.com/kb/3172985) 10586.494 kompilacji systemu operacyjnego).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Motyw nie synchronizuje, a także dane są chronione za pomocą Windows Information Protection 

Aby zapobiec wyciekom danych, danych, który jest chroniony za pomocą [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) nie będą synchronizowane za pośrednictwem Roaming stanu dla przedsiębiorstw dla urządzeń w usłudze Windows Update rozliczenia 10.

**Zalecana akcja**  
Brak. Przyszłe aktualizacje Windows może rozwiązać ten problem.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Ustawienia daty, godziny i regionu nie są synchronizowane na urządzeniu przyłączonym do domeny 
  
Urządzenia, które są przyłączone do domeny nie będzie działać z synchronizacji dla ustawienie daty, godziny i regionu: automatyczne czasu. Przy użyciu automatycznego czasu może zastąpić inne ustawienia daty, godziny i Region i spowodować, że te ustawienia nie można zsynchronizować. 

**Zalecana akcja**  
Brak. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>Monitów kontroli konta użytkownika podczas synchronizacji haseł

Wpływa na urządzeniach z systemem Windows 10 listopada Update (wersja 1511) z sieci bezprzewodowej kart interfejsu Sieciowego, który jest skonfigurowany do synchronizacji haseł.

**Zalecana akcja**  
Upewnij się, klient v1511 systemu Windows 10 jest to wersja aktualizacji zbiorczej ([KB3140743](https://support.microsoft.com/kb/3140743) 10586.494 kompilacji systemu operacyjnego).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synchronizacja nie działa na urządzeniach, które użycie karty inteligentnej do logowania

Jeśli spróbujesz się zalogować się do urządzenia Windows przy użyciu karty inteligentnej lub wirtualnej karty inteligentnej, synchronizacja ustawień przestaną działać.     

**Zalecana akcja**  
Brak. Przyszłe aktualizacje Windows może rozwiązać ten problem.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Urządzenia przyłączone do domeny nie synchronizuje się po opuszczeniu sieci firmowej     

Przyłączone do domeny urządzeń zarejestrowanych w usłudze Azure AD może wystąpić błąd synchronizacji, jeśli urządzenie znajduje się poza siedzibą firmy przez dłuższy czas i nie można ukończyć uwierzytelniania przez domenę.

**Zalecana akcja**  
Podłącz urządzenie do sieci firmowej, aby wznowić synchronizację.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Nie synchronizuje urządzeń dołączono do usługi AD na platformie Azure, a użytkownik ma mieszanej wielkości liter nazwy głównej nazwy użytkownika.

Jeśli użytkownik ma mieszane przypadek nazwy UPN (np. nazwa_użytkownika zamiast nazwy użytkownika), a użytkownik na urządzeniu dołączono usługi Azure AD, który został uaktualniony z systemu Windows 10 kompilacji 10586 do 14393, urządzenie użytkownika może uniemożliwić synchronizacji. 

**Zalecana akcja**  
Użytkownik należy odłączyć, a następnie ponowne dołączenie urządzenia do chmury. Aby to zrobić, zaloguj się jako użytkownika administratora lokalnego i Odłącz urządzenie, przechodząc do **ustawienia** > **systemu** > **o** i wybierz pozycję "Manage lub Odłącz od pracy lub nauki". Czyszczenie plików poniżej i Azure AD Join urządzenia ponownie w **ustawienia** > **systemu** > **o** i wybierając pozycję "Połącz z pracy lub Służbowe". W dalszym ciągu przyłączenie urządzenia do usługi Azure Active Directory, a następnie Ukończ przepływ.

W kroku oczyszczania oczyszczania następujące pliki:
- Settings.dat w `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Wszystkie pliki w folderze `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Identyfikator zdarzenia 6065: 80070533 ten użytkownik nie może się zalogować, ponieważ to konto jest obecnie wyłączona.  

W Podglądzie zdarzeń Dzienniki SettingSync/Debug ten błąd może być widoczny, jeśli wygasły poświadczenia użytkownika. Ponadto mogą wystąpić, gdy dzierżawy nie miał automatycznie AzureRMS aprowizowane. 

**Zalecana akcja**  
W pierwszym przypadku należy mieć użytkownika, zaktualizuj swoje poświadczenia i zaloguj się do urządzenia za pomocą nowych poświadczeń. Aby rozwiązać ten problem AzureRMS, wykonaj czynności opisane w [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Identyfikator zdarzenia 1098: Błąd: Operacja brokera tokenu 0xCAA5001C nie powiodła się  

W Podglądzie zdarzeń w obszarze dzienniki usługi AAD/Operational tego błędu mogą być widoczne z 1104 zdarzeń: AP chmury AAD wtyczki wywołanie Get token zwrócony kod błędu: 0xC000005F. Ten problem występuje, gdy nie ma uprawnień lub własności atrybutów.  

**Zalecana akcja**  
Wykonaj kroki wymienione [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać przegląd, zobacz [Przegląd roaming stanu dla przedsiębiorstw](enterprise-state-roaming-overview.md).
