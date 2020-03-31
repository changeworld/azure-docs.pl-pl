---
title: Pobieranie i instalowanie aplikacji Microsoft Authenticator — Azure AD
description: Pobierz i zainstaluj aplikację Microsoft Authenticator, aby zweryfikować swoją tożsamość podczas korzystania z weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 9d8ea02c91dc6aec41a1b548eff148e85111fc32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063904"
---
# <a name="download-and-install-the-microsoft-authenticator-app"></a>Pobieranie i instalowanie aplikacji Microsoft Authenticator

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników. Jeśli jesteś administratorem, więcej informacji na temat konfigurowania i zarządzania środowiskiem usługi Azure Active Directory (Azure AD) możesz znaleźć w [dokumentacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

Aplikacja Microsoft Authenticator pomaga w logowaniu się do kont, jeśli używana jest weryfikacja dwuskładnikowa. Weryfikacja dwuskładnikowa zapewnia bezpieczniejszy dostęp do kont, szczególnie podczas wyświetlania informacji poufnych. Ponieważ hasła mogą zostać zapomniane, skradzione lub ujawnione, weryfikacja dwuskładnikowa zapewnia dodatkowy poziom zabezpieczeń, który pomaga chronić konto, utrudniając włamanie się na nie innym osobom.

Aplikacji Microsoft Authenticator można używać na wiele sposobów, na przykład:

- **Weryfikacja dwuskładnikowa.** Standardową metodą weryfikacji, gdzie jednym z czynników jest hasło. Po zalogowaniu się przy użyciu nazwy użytkownika i hasła możesz zatwierdzić powiadomienie lub wprowadzić podany kod weryfikacyjny.

- **Logowanie telefoniczne.** Wersja weryfikacji dwuskładnikowej, która umożliwia logowanie się bez konieczności używania hasła, używanie nazwy użytkownika i urządzenia mobilnego za pomocą odcisku palca, twarzy lub numeru PIN.

- **Generowanie kodu.** Jako generator kodu dla innych kont, które obsługują aplikacje uwierzytelniającego.

> [!Important]
> Aplikacja Microsoft Authenticator współpracuje z dowolnym kontem, które korzysta z weryfikacji dwuskładnikowej i obsługuje oparte na czasie standardy haseł jednorazowych (TOTP).
>
> Twoja organizacja może wymagać korzystania z aplikacji uwierzytelnienia do logowania się i uzyskiwania dostępu do danych organizacji i dokumentów. Mimo iż Twoja nazwa użytkownika może być wyświetlana w aplikacji, konto nie będzie skonfigurowane jako metoda weryfikacji, dopóki nie ukończysz procesu rejestracji. Aby uzyskać więcej informacji, zobacz [Dodawanie konta służbowego](user-help-auth-app-add-work-school-account.md).
> 
> [!NOTE]
> Jeśli masz problemy z zalogowaniem się do swojego konta, zobacz temat [Kiedy nie możesz zalogować się na swoje konto Microsoft](https://support.microsoft.com/help/12429).  Uzyskaj więcej informacji o tym, co zrobić, gdy podczas próby zalogowania się do konta Microsoft pojawia się komunikat [„To konto Microsoft nie istnieje”](https://support.microsoft.com/help/13811).

## <a name="download-and-install-the-app"></a>Pobieranie i instalowanie aplikacji

Zainstaluj najnowszą wersję aplikacji Microsoft Authenticator na podstawie systemu operacyjnego:

- **Google Android.** Na urządzeniu z systemem Android przejdź do Google Play, aby [pobrać i zainstalować aplikację Microsoft Authenticator](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator).

- **Apple iOS.** Na urządzeniu z systemem Apple iOS przejdź do sklepu App Store, aby [pobrać i zainstalować aplikację Microsoft Authenticator](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458).

>[!Important]
>Jeśli nie korzystasz obecnie z urządzenia przenośnego, nadal możesz pobrać aplikację Microsoft Authenticator, wysyłając sobie łącze do pobrania ze [strony Microsoft Authenticator](https://www.microsoft.com/en-us/account/authenticator).

## <a name="next-steps"></a>Następne kroki

Po pobraniu i zainstalowaniu aplikacji należy dodać różne konta. Aby uzyskać więcej informacji, zobacz:

- **Authenticator.** Pobierz aplikację uwierzytelniającego i użyj aplikacji uwierzytelniającego, aby uzyskać powiadomienie o zatwierdzeniu lub losowo wygenerowany kod zatwierdzenia do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania i używania aplikacji Microsoft Authenticator, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z aplikacji uwierzytelniającego](security-info-setup-auth-app.md).

- **Tekst urządzenia mobilnego.** Wpisz numer urządzenia mobilnego i otrzymasz sms-a z kodem, którego użyjesz do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą wiadomości SMS, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z wiadomości tekstowych (SMS).](security-info-setup-text-msg.md)

- **Połączenie telefoniczne z urządzeniem mobilnym lub służbowym.** Wprowadź numer urządzenia mobilnego i uzyskaj połączenie telefoniczne w celu weryfikacji dwuetapowej lub zresetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą numeru telefonu, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z połączeń telefonicznych.](security-info-setup-phone-number.md)

- **Klucz zabezpieczeń.** Zarejestruj klucz zabezpieczeń zgodny z firmą Microsoft i użyj go wraz z kodem PIN do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikacji tożsamości za pomocą klucza zabezpieczeń, zobacz [Konfigurowanie informacji zabezpieczających w celu używania klucza zabezpieczeń](security-info-setup-security-key.md).

- **Adres e-mail.** Wprowadź służbowy adres e-mail, aby otrzymać wiadomość e-mail z prośbą o zresetowanie hasła. Ta opcja nie jest dostępna w przypadku weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania poczty e-mail, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z poczty e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na niektóre pytania zabezpieczające utworzone przez administratora w organizacji. Ta opcja jest dostępna tylko w przypadku resetowania hasła, a nie weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania pytań zabezpieczających, zobacz artykuł [Konfigurowanie informacji zabezpieczających do używania pytań zabezpieczających.](security-info-setup-questions.md)
