---
title: Tworzenie haseł aplikacji na stronie Informacje zabezpieczające (wersja zapoznawcza) — Azure AD
description: Utwórz automatycznie wygenerowane hasła (hasła aplikacji) do użycia z dowolną aplikacją niebędącą przeglądarką lub dowolną aplikacją, która nie obsługuje weryfikacji dwuskładnikowej w organizacji. To hasło aplikacji jest oddzielone od zwykłego hasła i można je skonfigurować ze strony Informacje o zabezpieczeniach.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 787fa67ee77997fd1f9967db3abdbfc83d4ffad2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064074"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>Tworzenie haseł aplikacji na stronie Informacje zabezpieczające (wersja zapoznawcza)

Niektóre aplikacje, takie jak Outlook 2010, nie obsługują weryfikacji dwuetapowej. Ten brak pomocy technicznej oznacza, że jeśli używasz weryfikacji dwuetapowej w organizacji, aplikacja nie będzie działać. Aby obejść ten problem, można utworzyć automatycznie wygenerowane hasło do użycia z każdą aplikacją niebędącą przeglądarką, niezależnie od normalnego hasła.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Administrator może nie zezwolić na używanie haseł aplikacji. Jeśli nie widzisz **opcji haseł aplikacji,** nie są one dostępne w Twojej organizacji.

Podczas korzystania z haseł aplikacji należy pamiętać:

- Hasła aplikacji są generowane automatycznie i powinny być tworzone i wprowadzane raz na aplikację.

- Istnieje limit 40 haseł na użytkownika. Jeśli spróbujesz utworzyć go po tym limicie, zostanie wyświetlony monit o usunięcie istniejącego hasła, zanim zostanie wyświetlony możliwość utworzenia nowego.

    >[!Note]
    >Klienci pakietu Office 2013 (w tym program Outlook) obsługują nowe protokoły uwierzytelniania i mogą być używane z weryfikacją dwuetapową. Ta obsługa oznacza, że po włączeniu weryfikacji dwuetapowej nie będziesz już potrzebować haseł aplikacji dla klientów pakietu Office 2013. Aby uzyskać więcej informacji, zobacz [artykuł Jak działa nowoczesne uwierzytelnianie dla pakietu Office 2013 i aplikacji klienckich pakietu Office 2016.](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)

## <a name="create-new-app-passwords"></a>Tworzenie nowych haseł aplikacji

Jeśli korzystasz z weryfikacji dwuetapowej na koncie służbowym lub szkolnym, a administrator włączył funkcje informacji zabezpieczających, możesz utworzyć i usunąć hasła aplikacji za pomocą strony **Informacje zabezpieczające.**

>[!Note]
>Jeśli administrator nie włączył funkcji informacji zabezpieczających, należy postępować zgodnie z instrukcjami i informacjami w sekcji [Zarządzanie hasłami aplikacji w celu weryfikacji dwuetapowej.](multi-factor-authentication-end-user-app-passwords.md)

### <a name="to-create-a-new-app-password"></a>Aby utworzyć nowe hasło aplikacji

1. Zaloguj się na swoje konto służbowe, https://myprofile.microsoft.com/ a następnie przejdź do strony.

    ![Strona Mój profil z wyróżnionymi linkami informacji o zabezpieczeniach](media/security-info/securityinfo-myprofile-page.png)

2. Wybierz **pozycję Informacje o zabezpieczeniach** z lewego okienka nawigacji lub z łącza w bloku Informacje o **zabezpieczeniach,** a następnie wybierz pozycję **Dodaj metodę** ze strony Informacje o **zabezpieczeniach.**

    ![Strona informacji o zabezpieczeniach z wyróżnioną opcją Dodaj metodę](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stronie **Dodawanie metody** wybierz pozycję **Hasło aplikacji** z listy rozwijanej, a następnie wybierz pozycję **Dodaj**.

    ![Pole Dodaj metodę z wybranym hasłem aplikacji](media/security-info/securityinfo-myprofile-addpassword.png)

4. Wpisz nazwę aplikacji, która wymaga hasła aplikacji, a następnie wybierz przycisk **Dalej**.

    ![Strona hasła aplikacji z nazwą aplikacji](media/security-info/securityinfo-myprofile-password-appname.png)

5. Skopiuj tekst z pola **Hasło,** wklej hasło w obszarze hasła aplikacji (w tym przykładzie outlook 2010), a następnie wybierz pozycję **Gotowe**.

    ![Strona hasła aplikacji z nazwą aplikacji](media/security-info/securityinfo-myprofile-password-copytext.png)

    Hasło zostanie dodane i można pomyślnie zalogować się do aplikacji w przyszłości.

## <a name="delete-your-app-passwords"></a>Usuwanie haseł aplikacji

Jeśli nie musisz już używać aplikacji, która wymaga hasła do aplikacji, możesz usunąć skojarzone hasło aplikacji. Usunięcie hasła aplikacji zwalnia jeden z dostępnych miejsc na hasło aplikacji do wykorzystania w przyszłości.

>[!Important]
>Jeśli usuniesz hasło aplikacji przez pomyłkę, nie ma sposobu, aby je cofnąć. Musisz utworzyć nowe hasło aplikacji i ponownie wprowadzić je do aplikacji, wykonując kroki opisane w sekcji [Tworzenie nowych haseł aplikacji](#create-new-app-passwords) w tym artykule.

### <a name="to-delete-an-app-password"></a>Aby usunąć hasło aplikacji

1. Na stronie **Informacje o zabezpieczeniach** wybierz łącze **Usuń** obok opcji **Hasło aplikacji** dla określonej aplikacji.

    ![Łącze, aby usunąć metodę hasła aplikacji z informacji zabezpieczających](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Z pola potwierdzenia wybierz **opcję Tak,** aby usunąć **hasło aplikacji**. Po usunięciu hasła aplikacji jest ono usuwane z informacji zabezpieczających i znika ze strony **Informacje o zabezpieczeniach.**

## <a name="for-more-information"></a>Więcej informacji

- Aby uzyskać więcej informacji na temat strony **Informacje o zabezpieczeniach** i sposobu jej konfigurowania, zobacz [Omówienie informacji o zabezpieczeniach](user-help-security-info-overview.md)
