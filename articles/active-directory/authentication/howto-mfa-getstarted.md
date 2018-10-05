---
title: Uzyskaj wprowadzenie do usługi Azure MFA w chmurze
description: Microsoft Azure Multi-Factor Authentication Rozpoczynanie pracy przy użyciu dostępu warunkowego
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 07e9126b73b0282f61567fe8d06f785bf9a04fef
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803481"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>Wdrażanie oparte na chmurze usługi Azure Multi-Factor Authentication

Wprowadzenie do usługi Azure Multi-Factor Authentication (Azure MFA) jest dość proste.

Przed rozpoczęciem upewnij się, że masz następujące wymagania wstępne:

* Konto administratora globalnego w dzierżawie usługi Azure AD. Jeśli potrzebujesz pomocy, ukończenie tego kroku, zobacz artykuł naszych [Rozpoczynanie pracy z usługą Azure AD](../get-started-azure-ad.md).
* Prawidłowe licencje przypisane do użytkowników. Aby uzyskać więcej informacji, zobacz artykuł [sposobu uzyskania usługi Azure Multi-Factor Authentication](concept-mfa-licensing.md).

## <a name="choose-how-to-enable"></a>Wybierz sposób włączania

**Obsługiwane przez zasady dostępu warunkowego** — metoda ta jest omówiona w tym artykule. Jest najbardziej elastyczny sposób Włącz weryfikację dwuetapową dla użytkowników. Włączanie przy użyciu tylko zasad dostępu warunkowego działa w przypadku usługi Azure MFA w chmurze i jest funkcją premium usługi Azure AD.

**Obsługiwane przez usługę Azure AD Identity Protection** — ta metoda używa zasad ryzyka usługi Azure AD Identity Protection, które wymuszają weryfikację dwuetapową, oparte tylko na ryzyko logowania dla wszystkich aplikacji w chmurze. Ta metoda wymaga licencji usługi Azure Active Directory P2. Więcej informacji na temat tej metody można znaleźć w [sposobu konfigurowania zasad ryzyka użytkownika](../identity-protection/howto-user-risk-policy.md).

**Włączone przez zmianę stanu użytkownika** — jest to tradycyjne metody do wymagania weryfikacji dwuetapowej. Działa z zarówno usługi Azure MFA w chmurze i serwera Azure MFA. Za pomocą tej metody wymaga od użytkowników weryfikacji dwuetapowej **za każdym razem, gdy** Zaloguj się i przesłania zasady dostępu warunkowego. Więcej informacji na temat tej metody można znaleźć w [jak, które wymuszają weryfikację dwuetapową dla użytkownika](howto-mfa-userstates.md).

> [!Note]
> Więcej informacji na temat licencji i ceny można znaleźć na [usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) i [uwierzytelnianie wieloskładnikowe](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) stronach z cennikami.

## <a name="choose-authentication-methods"></a>Wybieranie metod uwierzytelniania

Włącz co najmniej jedną metodę uwierzytelniania dla użytkowników na podstawie wymagań Twojej organizacji. Uważamy, że po włączeniu dla użytkowników aplikacji Microsoft Authenticator oferuje najlepsze środowisko użytkownika. Jeśli potrzebujesz zrozumieć metody, które są dostępne i sposobu ich ustawiania, zobacz artykuł [metody uwierzytelniania](concept-authentication-methods.md).

## <a name="get-users-to-enroll"></a>Zachęcenia użytkowników do rejestracji

Po włączeniu zasady dostępu warunkowego, użytkownicy będą zmuszeni do zarejestrowania przy następnym używają aplikacji chronionej przy użyciu zasad. Jeśli włączysz zasadę, która wymaga uwierzytelniania Wieloskładnikowego dla wszystkich użytkowników na wszystkich aplikacji w chmurze, ta akcja może spowodować utrudnień dla użytkowników i usługi pomocy technicznej. Zaleca się Poproś użytkowników, aby zarejestrować metody uwierzytelniania wcześniej za pomocą portalu rejestracji pod [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). W wielu organizacjach znaleźć, wspomóc przyjęcie pomaga w tworzeniu plakaty, karty tabeli i wiadomości e-mail.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Włącz uwierzytelnianie wieloskładnikowe przy użyciu dostępu warunkowego

Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego.

### <a name="choose-verification-options"></a>Wybierz opcje weryfikacji

Przed włączeniem usługi Azure Multi-Factor Authentication, organizacji, należy określić opcje weryfikacji, jakie dział. Na potrzeby tego ćwiczenia możesz włączyć wywołanie telefonu i treść wiadomości na telefon są one ogólne opcje, że większość otrzymują możliwość korzystania. Więcej informacji na temat metod uwierzytelniania i ich użycia można znaleźć w artykule [metody uwierzytelniania?](concept-authentication-methods.md)

1. Przejdź do **usługi Azure Active Directory**, **użytkowników**, **uwierzytelnianie wieloskładnikowe**.

   ![Dostęp do portalu usługi Multi-Factor Authentication z bloku użytkownicy usługi Azure AD w witrynie Azure portal](media/howto-mfa-getstarted/users-mfa.png)

1. W nowej karcie, która zostanie otwarta, przejdź do **ustawienia usługi**.
1. W obszarze **opcje weryfikacji**, zaznacz wszystkie pola dla metody dostępne dla użytkowników.

   ![Konfigurowanie metod weryfikacji, na karcie Ustawienia usługi Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. Kliknij przycisk **Zapisz**.
5. Zamknij **ustawienia usługi** kartę.

### <a name="create-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
1. Przejdź do usługi **Azure Active Directory** i wybierz pozycję **Dostęp warunkowy**.
1. Wybierz **nowe zasady**.
1. Podaj znaczącą nazwę zasady.
1. W obszarze **użytkowników i grup**:
   * Na **Include** zaznacz **wszyscy użytkownicy** przycisku radiowego
   * ZALECANE: Na **wykluczyć** kartę, zaznacz pole **użytkowników i grup** i wybierz grupę służący do wykluczenia, gdy użytkownicy nie mają dostępu do metody ich uwierzytelniania.
   * Kliknij przycisk **Gotowe**.
1. W obszarze **aplikacje w chmurze**, wybierz opcję **wszystkie aplikacje w chmurze** przycisku radiowego.
   * OPCJONALNIE: Na **wykluczyć** karty, wybierz aplikacje w chmurze, które Twoja organizacja nie wymaga uwierzytelniania Wieloskładnikowego dla.
   * Kliknij przycisk **Gotowe**.
1. W obszarze **warunki** sekcji:
   * OPCJONALNIE: Po włączeniu usługi Azure Identity Protection, istnieje możliwość oceny ryzyka logowania jako część zasad.
   * OPCJONALNIE: Jeśli masz skonfigurowane zaufanych lokalizacji lub lokalizacje z nazwą, można określić zostać dołączone lub wykluczone z tych lokalizacji z zasad.
1. W obszarze **Grant**, upewnij się, że **udzielić dostępu** przycisk radiowy zostanie wybrany.
    * Pole wyboru dla **Wymagaj uwierzytelniania wieloskładnikowego**.
    * Kliknij pozycję **Wybierz**.
1. Pomiń **sesji** sekcji.
1. Ustaw **Włącz zasady** Przełącz, aby **na**.
1. Kliknij pozycję **Utwórz**.

![Tworzenie zasad dostępu warunkowego, aby włączyć usługę MFA dla użytkowników portalu platformy Azure w grupie pilotażowej](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>Testowanie usługi Azure Multi-Factor Authentication

Aby upewnić się, że zasady dostępu warunkowego działa, przetestuj logowanie do zasobu, który nie należy wymagać uwierzytelniania Wieloskładnikowego, a następnie do witryny Azure portal, która wymaga uwierzytelniania Wieloskładnikowego.

1. Otwórz nowe okno przeglądarki w trybie incognito lub InPrivate i przejdź do adresu [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Zaloguj użytkownika testowego utworzone w ramach sekcji wymagania wstępne w tym artykule i należy pamiętać, że powinna wymaga do zakończenia uwierzytelniania MFA.
   * Zamknij okno przeglądarki.
2. Otwórz nowe okno przeglądarki w trybie incognito lub InPrivate i przejdź do adresu [https://portal.azure.com](https://portal.azure.com).
   * Zaloguj się przy użyciu testu użytkownika utworzonego w ramach sekcji wymagania wstępne w tym artykule i zwróć uwagę, że powinno być teraz musieli zarejestrować i używać usługi Azure Multi-Factor Authentication.
   * Zamknij okno przeglądarki.

## <a name="next-steps"></a>Kolejne kroki

Gratulacje, po skonfigurowaniu usługi Azure Multi-Factor Authentication w chmurze.

Aby skonfigurować dodatkowe ustawienia, takie jak zaufane adresy IP, niestandardowe wiadomości głosowe i alertów oszustwa, zobacz artykuł [ustawienia skonfiguruj usługę Azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

Informacje o zarządzaniu ustawienia użytkownika dla usługi Azure Multi-Factor Authentication można znaleźć w artykule [Zarządzanie ustawieniami użytkownika przy użyciu usługi Azure Multi-Factor Authentication w chmurze](howto-mfa-userdevicesettings.md).

[Włącz konwergentnej rejestracji dla usługi Azure Multi-Factor Authentication i Azure AD samoobsługowego resetowania haseł](concept-registration-mfa-sspr-converged.md).
