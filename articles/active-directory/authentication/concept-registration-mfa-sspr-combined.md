---
title: Połączone rejestracji dla usługi Azure AD SSPR oraz uwierzytelnianie wieloskładnikowe (wersja zapoznawcza) — usługi Azure Active Directory
description: Uwierzytelnianie wieloskładnikowe systemu Azure AD i samoobsługi resetowaniem hasła rejestracji (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cf8d5cb13b39d58920555ff9d99a4949e1bfc20
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60415794"
---
# <a name="combined-security-information-registration-preview"></a>Połączone zabezpieczeń informacji o rejestracji (wersja zapoznawcza)

Przed rejestracją połączonych użytkowników zarejestrowania metod uwierzytelniania dla usługi Azure Multi-Factor Authentication i samoobsługowego resetowania haseł (SSPR) oddzielnie. Osoby zostały mylić podobne metody były używane do uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ, ale musieli zarejestrować dla obu funkcji. Łączna rejestracja w usłudze Użytkownicy mogą teraz zarejestrować jeden raz i Uzyskaj korzyści z funkcji samoobsługowego resetowania HASEŁ i uwierzytelniania wieloskładnikowego.

![Mój profil przedstawiający zarejestrowane informacje zabezpieczające dla użytkownika](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Przed włączeniem nowe środowisko, przejrzyj tej dokumentacji skoncentrowane na administratora i dokumentacji skoncentrowane na użytkownika, aby upewnić się, że rozumiesz, funkcje i wpływ tej funkcji. Bazowy szkolenia na temat dokumentacji użytkownika przygotowanie użytkowników do nowego środowiska i aby zapewnić pomyślne wdrożenie.

Usługa Azure AD w połączeniu informacji zabezpieczających rejestracja nie jest obecnie dostępny dla chmury krajowe, takie jak Azure US Government, Azure (Niemcy) lub Azure (Chiny) — firmą 21Vianet.

|     |
| --- |
| Połączone zabezpieczeń informacji rejestracji uwierzytelniania wieloskładnikowego i resetowania hasła usługi Azure Active Directory (Azure AD) to funkcja publicznej wersji zapoznawczej usługi Azure AD. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Użytkownicy, którzy mają włączoną oryginalnej wersji zapoznawczej oraz na podstawie doświadczeń rozszerzone połączone rejestracji zostanie wyświetlony nowe zachowanie. Użytkownicy, którzy są włączone dla obu środowiska zostaną wyświetlone tylko nowe środowisko Mój profil. Nowy profil Moje wyrównane wyglądu i działania połączone rejestracji i zapewnia bezproblemowe środowisko dla użytkowników. Użytkownicy widzą mój profil, przechodząc do [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Moje strony profilu są zlokalizowane w oparciu o ustawienia języka komputera, dostęp do strony. Firma Microsoft przechowuje najnowszych język używany w pamięci podręcznej przeglądarki, więc kolejne próby, aby uzyskać dostęp do stron będą w dalszym ciągu renderowane w ostatnim język używany. Po wyczyszczeniu pamięci podręcznej stron ponownie będą renderowane. Jeśli chcesz wymusić określonego języka, można dodać `?lng=<language>` na końcu adresu URL, gdzie `<language>` znajduje się kod języka ma być renderowany.

![Konfigurowanie samoobsługowego resetowania HASEŁ lub innych metod weryfikacji zabezpieczeń](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metody dostępne w scalonej rejestracji

Połączone rejestracji obsługuje następujące metody uwierzytelniania i akcje:

|   | Zarejestruj subskrypcję | Change | Usuwanie |
| --- | --- | --- | --- |
| Microsoft Authenticator | Tak (maksymalnie 5) | Nie | Yes |
| Inną aplikację uwierzytelniania | Tak (maksymalnie 5) | Nie | Yes |
| Token sprzętowy | Nie | Nie | Yes |
| Numer telefonu | Yes | Yes | Yes |
| Alternatywny numer telefonu | Yes | Yes | Yes |
| Telefon służbowy | Nie | Nie | Nie |
| Email | Yes | Yes | Yes |
| Pytania zabezpieczające | Yes | Nie | Yes |
| Hasła aplikacji | Yes | Nie | Yes |

> [!NOTE]
> Hasła aplikacji są dostępne tylko dla użytkowników, którzy wymusić uwierzytelnianie wieloskładnikowe. Hasła aplikacji nie są dostępne dla użytkowników, którzy są włączone dla usługi Multi-Factor Authentication za pośrednictwem zasad dostępu warunkowego.

Użytkownicy mogą ustawić jedną z następujących opcji jako domyślną metodą uwierzytelniania wieloskładnikowego:

- Microsoft Authenticator — powiadomienie.
- Aplikacja Authenticator lub sprzętu token — kodu.
- Połączenie telefoniczne.
- wiadomości SMS.

Ponieważ w dalszym ciągu dodawać więcej metod uwierzytelniania w usłudze Azure AD, tych metod będą dostępne w scalonej rejestracji.

## <a name="combined-registration-modes"></a>Tryby połączone rejestracji

Istnieją dwa tryby połączone rejestracji: przerwań i zarządzanie nimi.

- **Tryb przerwań** to środowisko podręczne, wyświetlone dla użytkowników podczas rejestrowania, lub Odśwież swoje informacje zabezpieczające podczas logowania.

- **Zarządzanie tryb** jest częścią profilu użytkownika i umożliwia użytkownikom zarządzanie swoje informacje zabezpieczające.

Dla obu trybów osób, które zostały wcześniej zarejestrowane metody, która może służyć do uwierzytelniania wieloskładnikowego należy wykonywać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do swoich informacji zabezpieczających.

### <a name="interrupt-mode"></a>Tryb przerwania

Łączna rejestracja szanuje zasad uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ, jeśli obie są włączone dla Twojej dzierżawy. Zasady te umożliwiają kontrolowanie tego, czy użytkownik zostaje przerwany rejestracji podczas logowania i metody, które są dostępne do rejestracji.

Poniżej przedstawiono kilka scenariuszy, w których użytkownicy mogą być monitowani do zarejestrowania lub Odśwież swoje informacje zabezpieczające:

* Wymuszone za pomocą usługi Identity Protection rejestracji w usłudze Multi-Factor Authentication: Użytkownicy zostaną poproszeni o rejestracji podczas logowania. Ich zarejestrować metody uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ metody (Jeśli użytkownik jest włączony dla funkcji samoobsługowego resetowania HASEŁ).
* Wymuszane za pośrednictwem uwierzytelniania wieloskładnikowego dla użytkownika rejestracji w usłudze Multi-Factor Authentication: Użytkownicy zostaną poproszeni o rejestracji podczas logowania. Ich zarejestrować metody uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ metody (Jeśli użytkownik jest włączony dla funkcji samoobsługowego resetowania HASEŁ).
* Wymuszane za pośrednictwem dostępu warunkowego lub innymi zasadami rejestracji w usłudze Multi-Factor Authentication: Użytkownicy zostaną poproszeni o zarejestrować, korzystając z zasobem, który wymaga uwierzytelniania wieloskładnikowego. Ich zarejestrować metody uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ metody (Jeśli użytkownik jest włączony dla funkcji samoobsługowego resetowania HASEŁ).
* Wymuszone rejestracji samoobsługowego resetowania HASEŁ: Użytkownicy zostaną poproszeni o rejestracji podczas logowania. Zarejestruj są tylko metody z funkcji samoobsługowego resetowania HASEŁ.
* Odświeżanie funkcji samoobsługowego resetowania HASEŁ, wymuszane: Użytkownicy muszą przejrzeć swoje informacje zabezpieczające z interwałem ustawione przez administratora. Użytkownicy są wyświetlane w jego informacje i można potwierdzić bieżące informacje o lub wprowadzić zmiany, jeśli to konieczne.

Podczas rejestracji są wymuszane, użytkownicy zostaną wyświetlone minimalna liczba metod wymaganych do zgodne z zasadami uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ, od najbardziej do najmniej bezpieczna.

Na przykład:

* Użytkownik jest włączony dla funkcji samoobsługowego resetowania HASEŁ. Zasady samoobsługowego resetowania HASEŁ wymagane dwie metody, aby zresetować i włączył kod aplikacji mobilnej, poczty e-mail i telefon.
   * Ten użytkownik jest wymagany do zarejestrowania dwóch metod.
      * Użytkownik jest domyślnie wyświetlany Authenticator i telefon.
      * Użytkownika można zarejestrować e-mail zamiast aplikacji authenticator lub numeru telefonu.

Ten schemat blokowy opisano metody, które są wyświetlane dla użytkownika, w przypadku przerwania do rejestracji podczas logowania:

![Schemat blokowy informacje zabezpieczeń połączone](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Jeśli masz zarówno usługi Multi-Factor Authentication, jak i funkcji samoobsługowego resetowania HASEŁ jest włączony, firma Microsoft zaleca wymuszania rejestracji usługi Multi-Factor Authentication.

Jeśli zasady samoobsługowego resetowania HASEŁ wymaga od użytkowników przejrzeć swoje informacje zabezpieczające w regularnych odstępach czasu, użytkownicy są przerwane podczas logowania i przedstawiono ich zarejestrowane metody. Bieżące informacje o ich można potwierdzić, czy jest on aktualny, może wprowadzić zmiany w razie potrzeby.

### <a name="manage-mode"></a>Tryb zarządzania

Użytkownicy mogą uzyskiwać dostęp do zarządzania trybu, przechodząc do [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) lub wybierając **zabezpieczające** z ustawień Mój profil. Z tego miejsca użytkowników można dodać metody, usunąć lub zmienić istniejących metod, zmienić domyślną metodę i nie tylko.

## <a name="key-usage-scenarios"></a>Scenariusze użycia klucza

### <a name="set-up-security-info-during-sign-in"></a>Skonfiguruj informacje o zabezpieczeniach podczas logowania

Administrator ma być wymuszane rejestracji.

Użytkownik nie skonfigurowała wszystkie wymagane informacje zabezpieczające i prowadzi do witryny Azure portal. Po wprowadzeniu nazwy użytkownika i hasła, użytkownik jest monitowany, aby skonfigurować informacje o zabezpieczeniach. Użytkownik wykona następnie kroki wyświetlane w kreatorze, aby skonfigurować wymagane informacje zabezpieczające. Jeśli ustawienia na to zezwalają, użytkownik można zdefiniować metody inne niż te wyświetlane domyślnie. Po ukończeniu kreatora, użytkownicy dokonują przeglądu metody, które są skonfigurowane i ich domyślną metodą uwierzytelniania Multi-Factor Authentication. Aby ukończyć proces instalacji, użytkownik potwierdza, informacje i kontynuuje do witryny Azure portal.

### <a name="set-up-security-info-from-my-profile"></a>Skonfiguruj informacje zabezpieczające z ustawień Mój profil

Administrator nie jest wymuszane rejestracji.

Użytkownik, który nie zostało jeszcze skonfiguruj wszystkie wymagane informacje zabezpieczające przechodzi do [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). Użytkownik wybierze **zabezpieczające** w okienku po lewej stronie. Z tego miejsca użytkownik zdecyduje się dodać metodę, wybiera dowolnej z dostępnych metod i kolejne kroki, aby skonfigurować tę metodę. Gdy skończysz, użytkownik zobaczy metodę, która została właśnie skonfigurowanym na stronie informacje.

### <a name="delete-security-info-from-my-profile"></a>Usuń informacje zabezpieczające z ustawień Mój profil

Użytkownik, który został wcześniej skonfigurowany co najmniej jedną metodę przechodzi do [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Użytkownik wybierze opcję Usuń jedną z wcześniej zarejestrowane metody. Po zakończeniu użytkownik zobaczy na stronie informacje o zabezpieczeń nie jest już tej metody.

### <a name="change-the-default-method-from-my-profile"></a>Zmień metodę domyślną z ustawień Mój profil

Użytkownik, który został wcześniej skonfigurowany co najmniej jedną metodę, która może służyć do uwierzytelniania wieloskładnikowego przechodzi do [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Użytkownik zmienia bieżącej domyślnej metody do metody różne domyślne. Po zakończeniu użytkownik zobaczy nowej metody domyślne na stronę informacji zabezpieczających.

## <a name="next-steps"></a>Kolejne kroki

[Zezwolenie na zarejestrowanie połączone w ramach dzierżawy](howto-registration-mfa-sspr-combined.md)

[Dostępne metody uwierzytelniania wieloskładnikowego i samoobsługowego resetowania HASEŁ](concept-authentication-methods.md)

[Konfigurowanie samoobsługowego resetowania haseł](howto-sspr-deployment.md)

[Konfigurowanie usługi Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
