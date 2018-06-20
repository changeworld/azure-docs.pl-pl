---
title: Zbierania danych użytkownika w usłudze Azure Multi-Factor Authentication
description: Jakie informacje umożliwiają uwierzytelnianie użytkowników przez uwierzytelnianie wieloskładnikowe Azure?
services: multi-factor-authentication
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.service: multi-factor-authentication
ms.workload: identity
ms.topic: article
ms.date: 05/01/2018
ms.openlocfilehash: 2281a35d1616aa88b0c646fb96cb9f95c3272536
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264372"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Zbierania danych użytkownika w usłudze Azure Multi-Factor Authentication

Tym dokumencie wyjaśniono, jak można znaleźć informacje o użytkowniku zebrane przez Azure Multi-Factor Authentication serwer (MFA) i usługi Azure MFA (oparte na chmurze), w przypadku, gdy chcesz go usunąć.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Zebrane informacje

Serwera usługi MFA, rozszerzenia serwera zasad Sieciowych systemu Windows Server 2016 Azure MFA Adapter AD FS zbierać i przechowywać następujące informacje dotyczące 90 dni.

Próby uwierzytelnienia (używane do raportowania i rozwiązywania problemów):

- Sygnatura czasowa
- Nazwa użytkownika
- Imię
- Nazwisko
- Adres e-mail
- Grupa użytkownika
- Metoda uwierzytelniania (połączenie telefoniczne, Token OATH tekst wiadomości, aplikacji mobilnej)
- Tryb połączenia telefonicznego (standardowy, PIN)
- Kierunek wiadomości tekstowej (jednokierunkowe, dwukierunkowe)
- Tryb wiadomości tekstowych (OTP, OTP + kod PIN)
- Tryb aplikacji mobilnej (standardowy, PIN)
- Tryb tokenu OATH (standardowy, PIN)
- Typ uwierzytelnienia
- Nazwa aplikacji
- Wywołania podstawowego numer kierunkowy kraju
- Wywołania podstawowego numeru telefonu
- Wywołania podstawowego rozszerzenia
- Wywołania podstawowego uwierzytelniony
- Wynik wywołania podstawowego
- Kod kraju wywołania kopii zapasowej
- Numer telefonu wywołania kopii zapasowej
- Rozszerzenie wywołania kopii zapasowej
- Uwierzytelnione wywołania kopii zapasowej
- Wynik wywołania kopii zapasowej
- Ogólne uwierzytelniony
- Wynik ogólny
- Wyniki
- Uwierzytelniony
- Wynik
- Inicjowanie adresu IP
- Urządzenia
- Token urządzenia
- Typ urządzenia
- Wersja aplikacji na urządzenia przenośne
- Wersja systemu operacyjnego
- Wynik
- Sprawdź, czy używane powiadomień

Aktywacje (próbuje aktywować konto w aplikacji mobilnej Authenticator firmy Microsoft):
- Nazwa użytkownika
- Nazwa konta
- Sygnatura czasowa
- Uzyskanie wyniku kod aktywacji
- Aktywuj Powodzenie
- Błąd podczas aktywowania
- Wynik stan aktywacji
- Nazwa urządzenia
- Typ urządzenia
- Wersja aplikacji
- Token OATH włączone

Bloki (używany do określenia stanu zablokowanych oraz raportowanie):

- Sygnatura czasowa bloku
- Blok według nazwy użytkownika
- Nazwa użytkownika
- Kod kraju
- Numer telefonu
- Numer telefonu sformatowany
- Wewnętrzny
- Czyszczenie rozszerzenia
- Zablokowany
- Przyczyna blokady
- Sygnatura czasowa zakończenia
- Przyczyna zakończenia
- Blokada konta
- Alert oszustwa
- Alert o oszustwie nie jest zablokowany
- Język

Pomija (używane na potrzeby raportowania):

- Sygnatura czasowa obejścia
- Liczba sekund obejścia
- Obejście według nazwy użytkownika
- Nazwa użytkownika
- Kod kraju
- Numer telefonu
- Numer telefonu sformatowany
- Wewnętrzny
- Czyszczenie rozszerzenia
- Przyczyna obejścia
- Sygnatura czasowa zakończenia
- Przyczyna zakończenia
- Obejście używane

Zmiany (używane do synchronizacji zmiany użytkowników do serwera usługi MFA lub AAD):

- Zmiana sygnatury czasowej
- Nazwa użytkownika
- Nowy kod kraju
- Nowy numer telefonu
- Nowy numer wewnętrzny
- Nowe zapasowy numer kierunkowy kraju
- Nowy numer telefonu
- Nowe rozszerzenie kopii zapasowej
- Nowy kod PIN
- Wymagana zmiana numeru PIN
- Stary Token urządzenia
- Token nowego urządzenia

## <a name="gather-data-from-mfa-server"></a>Zbieranie danych z serwera usługi MFA

Dla serwera usługi MFA w wersji 8.0 lub nowszej następujący proces umożliwia administratorom wyeksportować wszystkie dane użytkowników:

- Zaloguj się do serwera usługi MFA, przejdź do **użytkowników** , wybierz użytkownika, a kliknij **Edytuj** przycisku. Zrzutów (Alt — PrtScn) w każdej karty, aby przyznać użytkownikowi ich bieżące ustawienia uwierzytelniania MFA.
- Z poziomu wiersza polecenia serwera usługi MFA, uruchom następujące polecenie Zmiana ścieżki zgodnie z instalacji `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` wygenerowało JSON pliku w formacie.
- Administratorzy mogą również wyeksportować wszystkie informacje MFA chmurze usługi, zbierane dla danego użytkownika za pomocą operacji GetUserGdpr zestawu SDK usługi sieci Web jest dostępna opcja lub zastosować w większych rozwiązanie raportowania.
- Wyszukiwanie `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` i kopii zapasowych na potrzeby "<username>" (w wyszukiwaniu uwzględnić cudzysłowy) aby znaleźć wszystkie wystąpienia rekordu użytkownika zostanie dodane lub zmienione.
   - Te rekordy mogą być ograniczone (ale nie została wyeliminowana) usuwając zaznaczenie pola wyboru **"Rejestruj zmiany użytkowników"** w UX serwera usługi MFA, sekcji rejestrowanie, karta pliki dziennika.
   - Jeśli skonfigurowano syslog, i **"Rejestruj zmiany użytkowników"** ewidencjonowaniu UX serwera usługi MFA, sekcji rejestrowanie, karta dziennik systemowy, a następnie wpisów dziennika można zbierać z syslog zamiast tego.
- Inne zdarzenia, nazwy użytkownika w MultiFactorAuthSvc.log i inny serwer usługi MFA logowania pliki należące do uwierzytelniania, który prób są traktowane jako działania i powielanymi podany przy użyciu eksportu MultiFactorAuthGdpr.exe lub zestawu SDK usług sieci Web GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Usuwanie danych z serwera usługi MFA

Z poziomu wiersza polecenia serwera usługi MFA, uruchom następujące polecenie Zmiana ścieżki zgodnie z instalacji `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` można usunąć wszystkich MFA informacji usługi chmury zbierane dla tego użytkownika.

- Usunięcie danych uwzględnienie go podczas eksportu w czasie rzeczywistym, ale może potrwać do 30 dni, operacyjne lub powielanymi danych ma zostać całkowicie usunięty.
- Administratorzy mogą również za pomocą operacji DeleteUserGdpr zestawu SDK usługi sieci Web jako opcji do usunięcia wszystkich informacji MFA chmurze usługi, zbierane dla danego użytkownika lub zastosować w większych rozwiązanie raportowania.

## <a name="gather-data-from-nps-extension"></a>Zbieranie danych z rozszerzenia serwera NPS

Użyj [Portal prywatności Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) Wyślij żądanie eksportu.

- Uwierzytelnianie wieloskładnikowe informacje znajdują się w pliku eksportu, co może zająć godziny a nawet dni.
- Wystąpienia nazwę użytkownika w dziennikach zdarzeń AzureMfa/autoryzacja/AuthZOptCh AzureMfa/AuthN/AuthNOptCh, AuthZAdminCh-AzureMfa/autoryzacja i są traktowane jako działania i powielanymi dostarczony do eksportu.

## <a name="delete-data-from-nps-extension"></a>Usuwanie danych z rozszerzenia serwera NPS

Użyj [Portal prywatności Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) żądania dla konta Zamknij, aby usunąć wszystkie MFA informacji usługi chmury zbierane dla tego użytkownika.

- Może upłynąć dane mają zostać całkowicie usunięte w ciągu 30 dni.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Zbieranie danych z systemu Windows Server 2016 Azure MFA Adapter AD FS

Użyj [Portal prywatności Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) Wyślij żądanie eksportu. 

- Uwierzytelnianie wieloskładnikowe informacje znajdują się w pliku eksportu, co może zająć godziny a nawet dni.
- Wystąpienia nazwy użytkownika w dziennikach zdarzeń AD FS śledzenie/Debug (jeśli jest włączona) są traktowane jako działania i powielanymi dostarczony do eksportu.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Usuwanie danych z systemu Windows Server 2016 Azure MFA Adapter AD FS

Użyj [Portal prywatności Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) żądania dla konta Zamknij, aby usunąć wszystkie MFA informacji usługi chmury zbierane dla tego użytkownika.

- Może upłynąć dane mają zostać całkowicie usunięte w ciągu 30 dni.

## <a name="gather-data-for-azure-mfa"></a>Zbieranie danych dla usługi Azure MFA

Użyj [Portal prywatności Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) Wyślij żądanie eksportu.

- Uwierzytelnianie wieloskładnikowe informacje znajdują się w pliku eksportu, co może zająć godziny a nawet dni.

## <a name="delete-data-for-azure-mfa"></a>Usuń dane dla usługi Azure MFA

Użyj [Portal prywatności Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) żądania dla konta Zamknij, aby usunąć wszystkie MFA informacji usługi chmury zbierane dla tego użytkownika.

- Może upłynąć dane mają zostać całkowicie usunięte w ciągu 30 dni.

## <a name="next-steps"></a>Kolejne kroki

[Raportowanie serwera usługi MFA](howto-mfa-reporting.md)
