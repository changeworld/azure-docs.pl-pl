---
title: Zbieranie danych użytkowników usługi Azure MFA — usługa Azure Active Directory
description: Jakie informacje są używane do uwierzytelniania użytkowników za pomocą uwierzytelniania wieloskładnikowego platformy Azure?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bc0f0dbcd08df887b2484be6ca8c92a85962c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848293"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Zbieranie danych użytkowników uwierzytelniania wieloskładnikowego usługi Azure

W tym dokumencie wyjaśniono, jak znaleźć informacje o użytkowniku zebrane przez serwer uwierzytelniania wieloskładnikowego platformy Azure (SERWER MFA) i usługę Azure MFA (oparte na chmurze) w przypadku, gdy chcesz je usunąć.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Zebrane informacje

Serwer usługi MFA, rozszerzenie serwera NPS i karta usług AD FS usługi Azure systemu Windows Server 2016 zbierają i przechowują następujące informacje przez 90 dni.

Próby uwierzytelniania (używane do raportowania i rozwiązywania problemów):

- Znacznik czasu
- Nazwa użytkownika
- Imię
- Nazwisko
- Adres e-mail
- Grupa użytkowników
- Metoda uwierzytelniania (rozmowa telefoniczna, wiadomość tekstowa, aplikacja mobilna, token OATH)
- Tryb rozmowy telefonicznej (standardowy, PIN)
- Kierunek wiadomości tekstowych (jednokierunkowy, dwukierunkowy)
- Tryb wiadomości tekstowych (OTP, OTP + PIN)
- Tryb aplikacji mobilnej (standardowy, PIN)
- Tryb tokenu OATH (standardowy, PIN)
- Typ uwierzytelniania
- Nazwa aplikacji
- Podstawowy kod kraju połączenia
- Podstawowy numer telefonu połączenia
- Podstawowe rozszerzenie połączeń
- Uwierzytelnione wywołanie podstawowe
- Wynik wywołania podstawowego
- Kod kraju połączenia zapasowego
- Kopia zapasowa numeru telefonu połączenia
- Rozszerzenie połączenia kopii zapasowej
- Uwierzytelnione wywołanie kopii zapasowej
- Wynik wywołania kopii zapasowej
- Całkowita uwierzytelniona
- Wynik ogólny
- Wyniki
- Uwierzytelnione
- Wynik
- Inicjowanie adresu IP
- Urządzenia
- Token urządzenia
- Typ urządzenia
- Wersja aplikacji mobilnej
- Wersja systemu operacyjnego
- Wynik
- Używane Sprawdzanie powiadomienia

Aktywacje (próby aktywowania konta w aplikacji mobilnej Microsoft Authenticator):
- Nazwa użytkownika
- Nazwa konta
- Znacznik czasu
- Uzyskaj wynik kodu aktywacyjnego
- Aktywuj sukces
- Błąd aktywacji
- Wynik stanu aktywacji
- Nazwa urządzenia
- Typ urządzenia
- Wersja aplikacji
- Token OATH włączony

Bloki (używane do określania stanu zablokowanego i raportowania):

- Sygnatura czasowa bloku
- Zablokuj według nazwy użytkownika
- Nazwa użytkownika
- Kod kraju
- Numer telefonu
- Sformatowany numer telefonu
- Wewnętrzny
- Czyste przedłużenie
- Zablokowane
- Przyczyna bloku
- Sygnatura czasowa zakończenia
- Przyczyna ukończenia
- Blokada konta
- Ostrzeżenie o oszustwach
- Alert o oszustwach nie został zablokowany
- Język

Obejścia (używane do raportowania):

- Sygnatura czasowa obejścia
- Sekundy obejścia
- Bypass By Username
- Nazwa użytkownika
- Kod kraju
- Numer telefonu
- Sformatowany numer telefonu
- Wewnętrzny
- Czyste przedłużenie
- Przyczyna obejścia
- Sygnatura czasowa zakończenia
- Przyczyna ukończenia
- Obejście używane

Zmiany (używane do synchronizowania zmian użytkowników z serwerem MFA server lub usługą Azure AD):

- Zmienianie sygnatury czasowe
- Nazwa użytkownika
- Nowy kod kraju
- Nowy numer telefonu
- Nowe rozszerzenie
- Nowy kod kraju kopii zapasowej
- Nowy zapasowy numer telefonu
- Nowe rozszerzenie kopii zapasowej
- Nowy kod PIN
- Wymagana zmiana numeru PIN
- Stary token urządzenia
- Nowy token urządzenia

## <a name="gather-data-from-mfa-server"></a>Zbieranie danych z serwera usługi MFA

W przypadku serwera usługi MFA w wersji 8.0 lub nowszej następujący proces umożliwia administratorom eksportowanie wszystkich danych dla użytkowników:

- Zaloguj się do serwera usługi MFA, przejdź do karty **Użytkownicy,** wybierz danego użytkownika i kliknij przycisk **Edytuj.** Ramić zrzuty ekranu (Alt-PrtScn) każdej karty, aby zapewnić użytkownikowi ich bieżące ustawienia usługi MFA.
- Z wiersza polecenia serwera usługi MFA uruchom następujące polecenie zmieniając `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` ścieżkę zgodnie z instalacją w celu uzyskania pliku sformatowanego json.
- Administratorzy mogą również użyć operacji GetUserGdpr usługi sieci Web jako opcji eksportowania wszystkich informacji o usłudze w chmurze usługi MFA zebranych dla danego użytkownika lub włączenia ich do większego rozwiązania raportowania.
- Wyszukaj `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` i\<wszelkie kopie zapasowe dla " nazwa użytkownika>" (uwzględnić cytaty w wyszukiwaniu), aby znaleźć wszystkie wystąpienia rekordu użytkownika dodawane lub zmieniane.
   - Te rekordy mogą być ograniczone (ale nie wyeliminowane) przez odznaczenie **"Zaloguj zmiany użytkownika"** w ux serwera usługi MFA, rejestrowanie sekcji, log files kartę.
   - Jeśli syslog jest skonfigurowany, a **"Log zmiany użytkownika"** jest zaznaczone w UX serwera mfa, rejestrowanie sekcji, karta Syslog, a następnie wpisy dziennika mogą być zbierane z syslog zamiast.
- Inne wystąpienia nazwy użytkownika w MultiFactorAuthSvc.log i innych plików dziennika serwera usługi MFA odnoszących się do prób uwierzytelniania są uważane za operacyjne i powielające się do informacji dostarczonych przy użyciu eksportu MultiFactorAuthGdpr.exe lub SDK usługi sieci Web GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Usuwanie danych z serwera usługi MFA

Z wiersza polecenia serwera usługi MFA uruchom następujące polecenie zmieniając `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` ścieżkę zgodnie z instalacją, aby usunąć wszystkie informacje o usłudze w chmurze usługi usługi mfa zebrane dla tego użytkownika.

- Dane zawarte w eksporcie są usuwane w czasie rzeczywistym, ale całkowite usunięcie danych operacyjnych lub powielających może potrwać do 30 dni.
- Administratorzy mogą również użyć operacji SDK DeleteUserGdpr usługi sieci Web jako opcji usunięcia wszystkich informacji o usłudze w chmurze usługi MFA zebranych dla danego użytkownika lub włączenia ich do większego rozwiązania raportowania.

## <a name="gather-data-from-nps-extension"></a>Zbieranie danych z rozszerzenia NPS

Użyj [portalu prywatności firmy Microsoft,](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) aby złożyć żądanie eksportu.

- Informacje o uwierzytelniania wieloskładnikowym są zawarte w eksporcie, co może potrwać wiele godzin lub dni.
- Wystąpienia nazwy użytkownika w dziennikach zdarzeń AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh i AzureMfa/AuthZ/AuthZOptCh są uważane za operacyjne i powielające informacje podane w eksporcie.

## <a name="delete-data-from-nps-extension"></a>Usuwanie danych z rozszerzenia NPS

Użyj [portalu prywatności firmy Microsoft,](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) aby poprosić o zamknięcie konta, aby usunąć wszystkie informacje o usłudze w chmurze usługi mfa zebrane dla tego użytkownika.

- Całkowite usunięcie danych może potrwać do 30 dni.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Zbieranie danych z karty usług AD FS usługi Windows Server 2016 Azure MFA

Użyj [portalu prywatności firmy Microsoft,](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) aby złożyć żądanie eksportu. 

- Informacje o uwierzytelniania wieloskładnikowym są zawarte w eksporcie, co może potrwać wiele godzin lub dni.
- Wystąpienia nazwy użytkownika w dziennikach zdarzeń śledzenia/debugowania usług AD FS (jeśli jest włączona) są uważane za operacyjne i powielane informacji podanych podczas eksportowania.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Usuwanie danych z karty usług AD FS usługi Windows Server 2016 Azure MFA

Użyj [portalu prywatności firmy Microsoft,](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) aby poprosić o zamknięcie konta, aby usunąć wszystkie informacje o usłudze w chmurze usługi mfa zebrane dla tego użytkownika.

- Całkowite usunięcie danych może potrwać do 30 dni.

## <a name="gather-data-for-azure-mfa"></a>Zbieranie danych dla usługi Azure MFA

Użyj [portalu prywatności firmy Microsoft,](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) aby złożyć żądanie eksportu.

- Informacje o uwierzytelniania wieloskładnikowym są zawarte w eksporcie, co może potrwać wiele godzin lub dni.

## <a name="delete-data-for-azure-mfa"></a>Usuń dane dla usługi Azure MFA

Użyj [portalu prywatności firmy Microsoft,](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) aby poprosić o zamknięcie konta, aby usunąć wszystkie informacje o usłudze w chmurze usługi mfa zebrane dla tego użytkownika.

- Całkowite usunięcie danych może potrwać do 30 dni.

## <a name="next-steps"></a>Następne kroki

[Raportowanie serwera usługi MFA](howto-mfa-reporting.md)
