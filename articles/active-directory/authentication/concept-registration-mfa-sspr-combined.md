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
ms.openlocfilehash: 536d26abf563f18ed7cec6668fcd1d4223f5a135
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370163"
---
# <a name="combined-security-information-registration-preview"></a>Połączone zabezpieczeń informacji o rejestracji (wersja zapoznawcza)

Przed rejestracją połączone użytkownicy zarejestrowani metod uwierzytelniania dla usługi Azure Multi-Factor Authentication (MFA) i samoobsługowego resetowania haseł (SSPR) w dwóch różnych środowiskach. Osoby zostały mylić podobne metody były używane dla usługi Azure MFA i samoobsługowego resetowania HASEŁ, ale musieli zarejestrować dla każdej funkcji oddzielnie. Łączna rejestracja w usłudze Użytkownicy mogą teraz zarejestrować jeden raz i Uzyskaj korzyści z usługi Azure MFA i samoobsługowego resetowania HASEŁ.

![Mój profil przedstawiający zarejestrowane informacje zabezpieczające dla użytkownika](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Przed włączeniem nowe środowisko, przejrzyj tej dokumentacji skoncentrowane na administratora i dokumentacji skoncentrowane na użytkownika, aby upewnić się, że rozumiesz, funkcje i wpływ tej funkcji. Bazowy szkolenia na temat dokumentacji użytkownika przygotowanie użytkowników do nowego środowiska i aby zapewnić pomyślne wdrożenie.

|     |
| --- |
| Połączone zabezpieczeń informacji o rejestracji dla usługi Azure Multi-Factor Authentication i Azure AD samoobsługowego resetowania haseł to funkcja publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

> [!IMPORTANT]
> Jeśli użytkownik jest włączony dla oryginalnej wersji zapoznawczej oraz na podstawie doświadczeń rozszerzone połączone rejestracji, zobaczy nowe środowisko. Użytkownicy, którzy są włączone dla obu środowiska będą widzieć tylko nowe środowisko Mój profil. Nowy profil Moje wyrównane wyglądu i działania połączone rejestracji i zapewnia bezproblemowe środowisko dla użytkowników. Użytkownicy widzą mój profil, przechodząc do [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Strony Mój profil są zlokalizowane w oparciu o bieżące ustawienia języka na komputerze uzyskiwanie dostępu do strony. Firma Microsoft przechowuje najnowszych języka, wykorzystywane w pamięci podręcznej przeglądarki, więc kolejnych prób dostępu będzie w dalszym ciągu renderowane w ostatnim język używany. Wyczyszczenie pamięci podręcznej spowoduje, że strony w celu ponownego renderowania. Jeśli chcesz wymusić, dodając określonego języka `?lng=de-DE` na końcu adresu URL gdzie `de-DE` jest ustawiony na odpowiedni język kodu wymusi strony do renderowania, w tym języku.

![Konfigurowanie samoobsługowego resetowania HASEŁ lub innych metod weryfikacji zabezpieczeń](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>Metody dostępne w konwergentnej rejestracji

W tej chwili połączony rejestracji obsługuje następujących metod i działań dla tych metod:

|   | Zarejestruj subskrypcję | Change | Usuwanie |
| --- | --- | --- | --- |
| Microsoft Authenticator | Tak (maks. 5) | Nie | Yes |
| Inną aplikację uwierzytelniania | Tak (maks. 5) | Nie | Yes |
| Token sprzętowy | Nie | Nie | Yes |
| Numer telefonu | Yes | Yes | Yes |
| Alternatywny numer telefonu | Yes | Yes | Yes |
| Telefon służbowy | Nie | Nie | Nie |
| Email | Yes | Yes | Yes |
| Pytania zabezpieczające | Yes | Nie | Yes |
| Hasła aplikacji | Yes | Nie | Yes |

> [!NOTE]
> Hasła aplikacji są dostępne tylko dla użytkowników, którzy została wymuszona usługi MFA. Hasła aplikacji nie są dostępne dla użytkowników, którzy są włączone dla usługi MFA za pośrednictwem zasad dostępu warunkowego.

Użytkownicy mogą ustawić następujące opcje jako metody domyślnego dla usługi MFA:

- Microsoft Authenticator — powiadomienie
- Aplikacja Authenticator lub sprzętu token — kod:
- Połączenie telefoniczne
- Wiadomość SMS

Ponieważ stale dodajemy więcej metod uwierzytelniania, takich do usługi Azure AD, te metody będą dostępne w połączone rejestracji.

## <a name="combined-registration-modes"></a>Łączna rejestracja tryby

Istnieją dwa "tryby" połączone rejestracji: przerwań i zarządzanie nimi.

Tryb przerwań, to środowisko podobne do kreatora, wyświetlana użytkownikowi podczas rejestrowania, lub Odśwież swoje informacje zabezpieczające podczas logowania.

Zarządzanie tryb jest częścią profilu użytkownika oraz pozwala na zarządzanie swoje informacje zabezpieczające.

Dla obu trybów Jeśli użytkownik został wcześniej zarejestrowane metody, która może służyć do uwierzytelniania Wieloskładnikowego, należy wykonać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do swoich informacji zabezpieczających.

### <a name="interrupt-mode"></a>Tryb przerwania

Łączna rejestracja szanuje zasad uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ, jeśli obie są włączone dla Twojej dzierżawy. Te kontrolują zasady, czy użytkownik jest przerywany do rejestracji podczas logowania i metody, które są dostępne do zarejestrowania.

Poniżej listy kilka scenariuszy, w którym użytkownik może zostać wyświetlony monit do zarejestrowania lub Odśwież swoje informacje zabezpieczające:

* Rejestracja w usłudze MFA wymuszane za pomocą usługi Identity Protection: Użytkownicy, poprosimy Cię o rejestracji podczas logowania. Ich zarejestrować metody uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ metody (Jeśli użytkownik jest włączony dla funkcji samoobsługowego resetowania HASEŁ).
* Rejestracja w usłudze MFA wymuszane za pośrednictwem usługi MFA na użytkownika: Użytkownicy, poprosimy Cię o rejestracji podczas logowania. Ich zarejestrować metody uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ metody (Jeśli użytkownik jest włączony dla funkcji samoobsługowego resetowania HASEŁ).
* Rejestracja w usłudze MFA wymuszane za pośrednictwem dostępu warunkowego lub innymi zasadami: Użytkownicy zostaną poproszeni o rejestrowania się podczas uzyskiwania dostępu do zasobu, który wymaga uwierzytelniania Wieloskładnikowego. Użytkownicy będą rejestrować metody uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ metody (Jeśli użytkownik jest włączony dla funkcji samoobsługowego resetowania HASEŁ).
* Wymuszone rejestracji samoobsługowego resetowania HASEŁ: Użytkownicy zostaną poproszeni o rejestracji podczas logowania. Tylko zgłosić metody funkcji samoobsługowego resetowania HASEŁ
* Odświeżanie funkcji samoobsługowego resetowania HASEŁ, wymuszane: Użytkownicy muszą przejrzeć swoje informacje zabezpieczające z interwałem ustawione przez administratora. Użytkownicy są wyświetlane w jego informacje i można wybrać "Wygląda dobrze" lub wprowadzić zmiany, jeśli to konieczne.

Podczas rejestracji są wymuszane, użytkownicy otrzymują minimalna liczba metod wymaganych do zgodne z zasadami uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ przez większość do najmniej bezpieczna.

Przykład:

* Użytkownik jest włączony dla funkcji samoobsługowego resetowania HASEŁ. Zasady samoobsługowego resetowania HASEŁ wymagane dwie metody, aby zresetować i włączył kod aplikacji mobilnej, poczty e-mail i telefon.
   * Ten użytkownik jest wymagany do zarejestrowania dwóch metod.
      * Domyślnie są one wyświetlane Authenticator i telefon.
      * Użytkownika można zarejestrować e-mail zamiast aplikacji authenticator lub numeru telefonu.

Następujące schemat blokowy opisano metody, które są wyświetlane dla użytkownika, w przypadku przerwania do rejestracji podczas logowania:

![Schemat blokowy informacje zabezpieczeń połączone](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Jeśli masz zarówno usługi MFA, jak i funkcji samoobsługowego resetowania HASEŁ jest włączony, firma Microsoft zaleca wymuszania rejestracji usługi MFA.

Jeśli zasady samoobsługowego resetowania HASEŁ wymaga od użytkowników przejrzeć swoje informacje zabezpieczające w regularnych odstępach czasu, użytkownicy są przerwane podczas logowania i przedstawiono ich zarejestrowane metody. Można wybrać "Wygląda dobrze", czy informacje są aktualne zdecydują "Edytuj info" Aby wprowadzić zmiany.

### <a name="manage-mode"></a>Tryb zarządzania

Użytkownicy mogą uzyskiwać dostęp do zarządzania trybu, przechodząc do [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) lub wybierając pozycję "Informacje zabezpieczające" z ustawień Mój profil. Z tego miejsca użytkownicy mogą Dodaj metody, usunąć lub zmienić istniejących metod, zmień metodę domyślną i nie tylko.

## <a name="key-usage-scenarios"></a>Scenariusze użycia klucza

### <a name="set-up-security-info-during-sign-in"></a>Skonfiguruj informacje o zabezpieczeniach podczas logowania

Administrator ma być wymuszane rejestracji.

Użytkownik nie skonfigurowała wszystkie wymagane informacje zabezpieczające i przechodzi do witryny Azure portal. Po wprowadzeniu nazwy użytkownika i hasła, użytkownik jest monitowany, aby skonfigurować informacje o zabezpieczeniach. Użytkownik wykona następnie kroki wyświetlane w kreatorze, aby skonfigurować wymagane informacje zabezpieczające. Użytkownik może wybrać do konfigurowania metod innych niż co wyświetlany domyślnie, jeśli ustawienia pozwalają. Na końcu kreatora użytkownik przegląda metody, które są skonfigurowane i metod ich domyślne dla usługi MFA. Aby ukończyć proces instalacji, użytkownik potwierdza, informacje i kontynuuje do witryny Azure portal.

### <a name="set-up-security-info-from-my-profile"></a>Skonfiguruj informacje zabezpieczające z ustawień Mój profil

Administrator nie jest wymuszane rejestracji.

Użytkownik, który nie został jeszcze skonfigurowany wszystkie wymagane informacje zabezpieczające przechodzi do [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). Następnie wybraniu **zabezpieczające** nawigacji po lewej stronie. Z tego miejsca użytkownik zdecyduje się dodać metodę, wybiera dowolnej z metod, które są dostępne dla nich i kolejne kroki, aby skonfigurować tę metodę. Po zakończeniu, użytkownik zobaczy metodę, którą one po prostu jest ustawiona na stronę informacji zabezpieczających.

### <a name="delete-security-info-from-my-profile"></a>Usuń informacje zabezpieczające z ustawień Mój profil

Użytkownik, który został wcześniej skonfigurowany co najmniej jedną metodę przechodzi do [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Użytkownik wybierze opcję Usuń jedną z wcześniej zarejestrowane metody. Po zakończeniu użytkownik zobaczy na stronie informacje o zabezpieczeń nie jest już tej metody.

### <a name="change-default-method-from-my-profile"></a>Zmień domyślną metodę z ustawień Mój profil

Użytkownik, który został wcześniej skonfigurowany co najmniej jedną metodę, która może służyć do uwierzytelniania Wieloskładnikowego przechodzi do [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). Użytkownik zmienia ich bieżącej domyślnej metody do metody różne domyślne. Po zakończeniu użytkownik zobaczy ich nowych domyślną metodę na stronę informacji zabezpieczających.

## <a name="next-steps"></a>Kolejne kroki

[Zezwolenie na zarejestrowanie połączone w ramach dzierżawy](howto-registration-mfa-sspr-combined.md)

[Dostępne metody uwierzytelniania Wieloskładnikowego i samoobsługowego resetowania HASEŁ](concept-authentication-methods.md)

[Konfigurowanie samoobsługowego resetowania haseł](howto-sspr-deployment.md)

[Konfigurowanie usługi Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
