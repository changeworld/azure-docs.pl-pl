---
title: Usługa Azure AD samoobsługowego resetowania haseł Windows 7 i 8.1 — usługi Azure Active Directory
description: Jak włączyć samoobsługowego resetowania haseł za pomocą zapomniane hasło na ekran logowania 8.1 lub Windows 7
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08794a7605ffbd3cd5d4b021b783f32afb190727
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861577"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>Instrukcje: Włącz Resetowanie z Windows 7, 8 i 8.1 haseł

Jako administrator włączono Samoobsługowe resetowanie haseł (SSPR), ale użytkownicy nadal wywoływanie pomocy technicznej do zresetowania swojego hasła, ponieważ nie można pobrać do okna przeglądarki dostęp do [portalu samoobsługowego resetowania HASEŁ](https://aka.ms/sspr). W przypadku maszyn z systemem Windows 10 można włączyć link "Resetuj hasło" na ekranie logowania przy użyciu samouczka [haseł usługi Azure AD z ekranu logowania](tutorial-sspr-windows.md), poniższe wskazówki pomogą umożliwianie użytkownikom resetowania Windows 7, 8 i 8.1 hasła na ekranie logowania Windows za pomocą funkcji samoobsługowego resetowania HASEŁ.

Inaczej niż w przypadku maszyn z systemem Windows 10, Windows 7, 8 i 8.1 maszyny nie mają dołączonych do domeny usługi Azure AD, lub zresetuj wymaganie przyłączone do domeny usługi Active Directory dla hasła.

![Ekran logowania Windows 7 przykładowe "Nie pamiętasz hasła?" linku](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Włączona funkcja samoobsługowego resetowania haseł w usłudze Azure AD.
* Poprawionego Windows 7 lub Windows 8.1, systemu operacyjnego.
* Protokół TLS 1.2, włączone za pomocą wytycznych znaleziony w [zabezpieczeń TLS (Transport Layer), ustawień rejestru](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).

> [!WARNING]
> Musi być włączony protokół TLS 1.2, negocjowania nie tylko zestaw automatycznego.

## <a name="install"></a>Instalowanie

1. Pobierz instalatora właściwe dla wersji systemu Windows, które chcesz włączyć.

   1. Oprogramowanie jest dostępne w Centrum pobierania Microsoft pod adresem [https://aka.ms/sspraddin](https://aka.ms/sspraddin)

1. Zaloguj się do maszyny, na którym chcesz zainstalować i uruchomić Instalatora.
1. Po zakończeniu instalacji zdecydowanie zaleca się ponowne uruchomienie komputera.
1. Po ponownym uruchomieniu komputera na ekranie logowania wybrać użytkownika i kliknij przycisk "Nie pamiętasz hasła?" Aby zainicjować hasło zresetować przepływu pracy.
1. Ukończ przepływ pracy, zgodnie z wyświetlanymi na ekranie kroki, aby zresetować hasło.

![Przykład Windows 7 kliknięto "Nie pamiętasz hasła?" Przepływu samoobsługowego resetowania HASEŁ](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>Instalacja dyskretna

* W przypadku instalacji dyskretnej użyj polecenia "msiexec /i SsprWindowsLogon.PROD.msi /qn"
* Odinstalowywanie w trybie dyskretnym użyj polecenia "msiexec /x SsprWindowsLogon.PROD.msi /qn"

## <a name="caveats"></a>Ostrzeżenia

Użytkownik musi zarejestrować na potrzeby samoobsługowego resetowania HASEŁ zanim będzie można użyć linku "Nie pamiętam hasła".

![Dodatkowe informacje zabezpieczające jest wymaganych do zresetowania hasła](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

Przy użyciu aplikacji Microsoft Authenticator dla powiadomień i kodów, aby zresetował Twoje hasło nie działa w tej wersji początkowej. Użytkownicy muszą mieć alternatywnych metod zarejestrowanych, które spełniają wymagania zasad.

Jeśli więcej niż jeden 3 dostawcy poświadczeń jest włączona na komputerze, użytkownicy będą widzieć więcej niż jeden profil użytkownika na ekranie logowania.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Zarówno na komputerze, jak i w usłudze Azure AD, będą rejestrowane zdarzenia.

Zdarzenia usługi Azure AD zostaną wyświetlone informacje dotyczące adresu IP i typu klienta, w którym wystąpił resetowania hasła.

![Przykład Windows 7 resetowania haseł w dzienniku inspekcji usługi AD platformy Azure](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

Jeśli wymagane jest dodatkowe rejestrowanie, można zmienić klucza rejestru na komputerze, aby włączyć pełne rejestrowanie. Włącz pełne rejestrowanie na potrzeby tylko rozwiązywania problemów.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

* Aby włączyć pełne rejestrowanie, należy utworzyć REG_DWORD: "EnableLogging" i ustaw ją na 1.
* Aby wyłączyć pełne rejestrowanie, należy zmienić REG_DWORD "EnableLogging" na wartość 0.

W przypadku maszyn z systemem Windows 7, 8 i 8.1 znajdują się za serwerem proxy lub zapory, powinien być dozwolony ruch HTTPS (443) do passwordreset.microsoftonline.com.

## <a name="next-steps"></a>Kolejne kroki

* [Umożliwianie użytkownikom systemu Windows 10 do zresetowania swojego hasła na ekranie logowania](tutorial-sspr-windows.md)
