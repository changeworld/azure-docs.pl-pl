---
title: Samoobsługowe resetowanie hasła dla systemu Windows — usługa Azure Active Directory
description: Jak włączyć samoobsługowe resetowanie hasła przy użyciu zapomnianego hasła na ekranie logowania systemu Windows
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4f08161daf1d9c1a4431d9e3fba3ca741d88b16
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743346"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Jak: Włącz resetowanie hasła z ekranu logowania systemu Windows

W przypadku komputerów z systemami Windows 7, 8, 8.1 i 10 można umożliwić użytkownikom resetowanie hasła na ekranie logowania systemu Windows. Użytkownicy nie muszą już znajdować urządzenia z przeglądarką internetową, aby uzyskać dostęp do [portalu SSPR.](https://aka.ms/sspr)

![Przykładowe ekrany logowania systemu Windows 7 i 10 z wyświetlonym linkiem SSPR](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Ogólne ograniczenia

- Resetowanie hasła nie jest obecnie obsługiwane na pulpicie zdalnym ani w sesjach rozszerzonych funkcji Hyper-V.
- Wiadomo, że niektórzy dostawcy poświadczeń innych firm powodują problemy z tą funkcją.
- Wyłączenie uac poprzez modyfikację [enablelua klucz rejestru](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) jest znany powodować problemy.
- Ta funkcja nie działa w przypadku sieci z wdrożonym uwierzytelnianiem sieciowym 802.1x i opcją "Wykonaj bezpośrednio przed logowaniem użytkownika". W sieciach z wdrożonym uwierzytelnianiem sieci 802.1x zalecane jest używanie uwierzytelniania maszynowego w celu włączenia tej funkcji.
- Hybrydowe maszyny przyłączone do usługi Azure AD muszą mieć dostęp do linii wzroku łączności sieciowej z kontrolerem domeny, aby używać nowego hasła i aktualizować poświadczenia w pamięci podręcznej.
- W przypadku korzystania z obrazu przed uruchomieniem sysprep upewnij się, że pamięć podręczna sieci web jest wyczyszczona dla wbudowanego administratora przed wykonaniem copyprofile kroku. Więcej informacji na temat tego kroku można znaleźć w artykule pomocy technicznej [Wydajność niska podczas korzystania z niestandardowego domyślnego profilu użytkownika](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- Wiadomo, że następujące ustawienia zakłócają możliwość używania i resetowania haseł na urządzeniach z systemem Windows 10
    - Jeśli ctrl+Alt+Del jest wymagany przez zasady w wersjach systemu Windows 10 przed wersją 1809, **resetowanie hasła** nie będzie działać.
    - Jeśli powiadomienia na ekranie blokady są wyłączone, pozycja **Resetuj hasło** nie będzie działać.
    - Ustawienie HideFastUserSwitching jest włączone lub ustawione na wartość 1
    - Ustawienie DontDisplayLastUserName jest włączone lub ustawione na wartość 1
    - Ustawienie NoLockScreen jest włączone lub ustawione na wartość 1
    - Ustawienie EnableLostMode jest określone na urządzeniu
    - Plik Explorer.exe został zastąpiony niestandardową powłoką
- Połączenie następujących trzech określonych ustawień może spowodować, że ta funkcja nie działa.
    - Logowanie interaktywne: nie wymagaj klawiszy CTRL+ALT+DEL = wyłączone
    - DisableLockScreenNotifications = 1 lub Włączone
    - Jednostka SKU systemu Windows nie jest wersjami domowymi ani profesjonalnymi

## <a name="windows-10-password-reset"></a>Resetowanie hasła w systemie Windows 10

### <a name="windows-10-prerequisites"></a>Wymagania wstępne systemu Windows 10

- Administrator musi włączyć samoobsługowe resetowanie hasła usługi Azure AD z witryny Azure Portal.
- **Użytkownicy muszą zarejestrować się w celu uzyskania prawa do pracy w celu uzyskania takiego sprzydencji numerów SSPR przed użyciem tej funkcji**
- Wymagania dotyczące serwera proxy sieci
   - Urządzenia z systemem Windows 10 
       - Port 443 `passwordreset.microsoftonline.com` do i`ajax.aspnetcdn.com`
       - Urządzenia z systemem Windows 10 obsługują tylko konfigurację serwera proxy na poziomie komputera
- Uruchom co najmniej system Windows 10, wersja april 2018 Update (v1803), a urządzenia muszą być następujące:
    - Dołączone do usługi Azure AD
    - hybrydowym dołączonym do usługi Azure AD.

### <a name="enable-for-windows-10-using-intune"></a>Włącz dla systemu Windows 10 przy użyciu usługi Intune

Wdrażanie zmiany konfiguracji w celu włączenia możliwości resetowania hasła z poziomu ekranu logowania przy użyciu usługi Intune jest metodą najbardziej elastyczną. Usługa Intune umożliwia wdrażanie zmiany konfiguracji dla określonej zdefiniowanej grupy maszyn. Ta metoda wymaga rejestracji urządzenia w usłudze Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Tworzenie zasad konfiguracji urządzenia w usłudze Intune

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Intune**.
1. Tworzenie nowego profilu konfiguracji urządzenia przez przejście do**Profilu** >  **konfiguracji** > urządzenia**Tworzenie profilu**
   - Podaj znaczącą nazwę profilu
   - Opcjonalnie podaj znaczący opis profilu
   - Platforma: **Windows 10 lub nowsza**
   - Typ profilu: **Niestandardowy**
1. Skonfiguruj obszar **Ustawienia**
   - **Dodaj** następujące ustawienie OMA-URI, aby włączyć link resetowania hasła
      - Podaj znaczącą nazwę, aby ułatwić zrozumienie działania ustawienia
      - Opcjonalnie podaj znaczący opis ustawienia
      - Ustaw pozycję **OMA-URI** na wartość `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - Ustaw pozycję **Typ danych** na **Liczba całkowita**
      - Ustaw pozycję **Wartość ** na **1**
      - Kliknij **przycisk OK**
   - Kliknij **przycisk OK**
1. Kliknij **przycisk Utwórz**
1. Te zasady można przypisać do określonych użytkowników, urządzeń lub grup. Więcej informacji można znaleźć w artykule [Przypisywanie profilów użytkowników i urządzeń w usłudze Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Włącz dla systemu Windows 10 przy użyciu rejestru

1. Zaloguj się do komputera PC z systemem Windows przy użyciu poświadczeń administracyjnych
1. Uruchom **regedit** jako administrator
1. Ustaw poniższy klucz rejestru
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Rozwiązywanie problemów z resetowaniem hasła w systemie Windows 10

Dziennik inspekcji usługi Azure AD zawiera informacje dotyczące adresu IP i typu klienta, które są powiązane z żądaniem resetowania hasła.

![Przykładowe resetowanie hasła systemu Windows 7 w dzienniku inspekcji usługi Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Gdy użytkownicy resetują swoje hasło z ekranu logowania urządzenia z systemem `defaultuser1` Windows 10, tworzone jest tymczasowe konto o niskich uprawnieniach. To konto jest używane, aby zapewnić bezpieczeństwo procesu resetowania hasła. Samo konto ma losowo wygenerowane hasło, nie jest wyświetlane do logowania urządzenia i zostanie automatycznie usunięte po zresetowaniu hasła przez użytkownika. Istnieje `defaultuser` wiele profili, ale można je bezpiecznie zignorować.

## <a name="windows-7-8-and-81-password-reset"></a>Resetowanie hasła w systemie Windows 7, 8 i 8.1

### <a name="windows-7-8-and-81-prerequisites"></a>Wymagania wstępne systemu Windows 7, 8 i 8.1

- Administrator musi włączyć samoobsługowe resetowanie hasła usługi Azure AD z witryny Azure Portal.
- **Użytkownicy muszą zarejestrować się w celu uzyskania prawa do pracy w celu uzyskania takiego sprzydencji numerów SSPR przed użyciem tej funkcji**
- Wymagania dotyczące serwera proxy sieci
   - Urządzenia z systemem Windows 7, 8 i 8.1
       - Port 443 do`passwordreset.microsoftonline.com`
- Poprawiony system operacyjny Windows 7 lub Windows 8.1.
- Protokół TLS 1.2 włączony przy użyciu wskazówek znalezionych w [ustawieniach rejestru TLS (Transport Layer Security).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- Jeśli na komputerze jest włączony więcej niż jeden dostawca poświadczeń innej firmy, użytkownicy zobaczą więcej niż jeden profil użytkownika na ekranie logowania.

> [!WARNING]
> TLS 1.2 musi być włączony, a nie tylko ustawiony na automatyczne negocjacje

### <a name="install"></a>Zainstaluj

1. Pobierz odpowiedni instalator dla wersji systemu Windows, którą chcesz włączyć.
   - Oprogramowanie jest dostępne w centrum pobierania firmy Microsoft pod adresem[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Zaloguj się do urządzenia, na którym chcesz zainstalować, i uruchom instalatora.
1. Po instalacji zaleca się ponowne uruchomienie komputera.
1. Po restarcie, na ekranie logowania wybrać użytkownika i kliknij "Zapomniałeś hasła?" , aby zainicjować przepływ pracy resetowania hasła.
1. Wykonaj przepływ pracy zgodnie z instrukcjami wyświetlanymi na ekranie, aby zresetować hasło.

![Przykładowy system Windows 7 kliknął "Zapomniałeś hasła?" Przepływ SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Instalacja w trybie dyskretnym

- W przypadku instalacji dyskretnej należy użyć polecenia "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- Aby odinstalować dyskretnie, użyj polecenia "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Rozwiązywanie problemów z resetowaniem hasła w systemie Windows 7, 8 i 8.1

Zdarzenia będą rejestrowane zarówno na komputerze, jak i w usłudze Azure AD. Zdarzenia usługi Azure AD będą zawierać informacje o adresie IP i typie klienta, w którym wystąpił resetowanie hasła.

![Przykładowe resetowanie hasła systemu Windows 7 w dzienniku inspekcji usługi Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Jeśli wymagane jest dodatkowe rejestrowanie, można zmienić klucz rejestru na komputerze, aby włączyć pełne rejestrowanie. Włącz rejestrowanie pełne tylko w celu rozwiązywania problemów.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Aby włączyć pełne rejestrowanie, utwórz `REG_DWORD: "EnableLogging"`plik , i ustaw go na 1.
- Aby wyłączyć pełne rejestrowanie, zmień `REG_DWORD: "EnableLogging"` na 0.

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Teraz, gdy skonfigurowano resetowanie hasła dla urządzeń z systemem Windows, jakie zmiany dla użytkownika? Skąd użytkownicy będą wiedzieć, że mogą zresetować swoje hasło na ekranie logowania?

![Przykładowe ekrany logowania systemu Windows 7 i 10 z wyświetlonym linkiem SSPR](./media/howto-sspr-windows/windows-reset-password.png)

Gdy użytkownicy próbują się zalogować, teraz widzą link **Resetowanie hasła** lub **Zapomniał hasła,** który otwiera samoobsługowe resetowanie hasła na ekranie logowania. Ta funkcja umożliwia użytkownikom zresetowanie hasła bez konieczności uzyskiwania dostępu do przeglądarki internetowej przy użyciu innego urządzenia.

Wskazówki dotyczące używania tej funkcji będzie można znaleźć w artykule [Reset your work or school password (Resetowanie hasła służbowego)](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Następne kroki

[Planowanie metod uwierzytelniania, aby umożliwić](concept-authentication-methods.md)

[Konfigurowanie systemu Windows 10](https://docs.microsoft.com/windows/configuration/)
